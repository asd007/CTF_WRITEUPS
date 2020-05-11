# Problem Statement:
Whoops. It seems Luffy played with my picture and I'm not able to open it anymore. Please help me.

Creator: 2phi

[7uffy_original](../Images/7uffy.png)

# Analysis

First, the obvious. Image does not load.

Is this a png file?

    asd007@host:/mnt/e/ctf$ file 7uffy.png
    7uffy_1.png: PNG image data, 1113 x 885, 8-bit/color RGBA, non-interlaced

So the header must be right, stating it's a PNG

Let's check for "hidden" stuff

    asd007@host:/mnt/e/ctf$ binwalk 7uffy.png
    DECIMAL       HEXADECIMAL     DESCRIPTION
    --------------------------------------------------------------------------------
    0             0x0             PNG image, 1113 x 885, 8-bit/color RGBA, non-interlaced

No luck here.

Metadata? Strings? 

    asd007@host:/mnt/e/ctf$ identify -verbose 7uffy.png
    asd007@host::/mnt/e/ctf$ strings 7uffy.png
    
No dice. ImageMagick listed nothing. Strings listed some mangled random printable chars, nothing useful.

Ok, time to bring the big guns:

    asd007@host:/mnt/e/ctf$ sudo apt-get install pngtools
    [moments later]
    asd007@host:/mnt/e/ctf$ pngchunks 7uffy.png
    Chunk: Data Length 13 (max 2147483647), Type 1380206665 [IHDR]
      Critical, public, PNG 1.2 compliant, unsafe to copy
      IHDR Width: 1113
      IHDR Height: 885
      IHDR Bitdepth: 8
      IHDR Colortype: 6
      IHDR Compression: 0
      IHDR Filter: 0
      IHDR Interlace: 0
      IHDR Compression algorithm is Deflate
      IHDR Filter method is type zero (None, Sub, Up, Average, Paeth)
      IHDR Interlacing is disabled
      Chunk CRC: -1537989379
    Chunk: Data Length 6 (max 2147483647), Type 1145523042 [bKGD]
      Ancillary, public, PNG 1.2 compliant, unsafe to copy
      ... Unknown chunk type
      Chunk CRC: -113001601
    Chunk: Data Length 9 (max 2147483647), Type 1935231088 [pHYs]
      Ancillary, public, PNG 1.2 compliant, safe to copy
      ... Unknown chunk type
      Chunk CRC: 10132504
    Chunk: Data Length 7 (max 2147483647), Type 1162692980 [tIME]
      Ancillary, public, PNG 1.2 compliant, unsafe to copy
      ... Unknown chunk type
      Chunk CRC: 1626594388
    Chunk: Data Length 29 (max 2147483647), Type 1951945833 [iTXt]
      Ancillary, public, PNG 1.2 compliant, safe to copy
      ... Unknown chunk type
      Chunk CRC: 1680762119
    Chunk: Data Length 8192 (max 2147483647), Type 1498628421 [EASY]
      Critical, public, PNG 1.2 compliant, unsafe to copy
      ... Unknown chunk type
      Chunk CRC: -2090358537
    Chunk: Data Length 8192 (max 2147483647), Type 1498628421 [EASY]
      Critical, public, PNG 1.2 compliant, unsafe to copy
      ... Unknown chunk type
      Chunk CRC: 558130939
    Chunk: Data Length 8192 (max 2147483647), Type 1498628421 [EASY]
      Critical, public, PNG 1.2 compliant, unsafe to copy
      ... Unknown chunk type
      Chunk CRC: 1857725745
    Chunk: Data Length 2584 (max 2147483647), Type 1498628421 [EASY]
      Critical, public, PNG 1.2 compliant, unsafe to copy
      ... Unknown chunk type
      Chunk CRC: 43823534
    Chunk: Data Length 0 (max 2147483647), Type 1145980233 [IEND]
      Critical, public, PNG 1.2 compliant, unsafe to copy
      IEND contains no data
      Chunk CRC: -1371381630

Bingo! 8 "unknown" chunks We need a closer look

# Solution

    asd007@host:/mnt/e/ctf$ pngcheck -v 7uffy.png
    File: 7uffy.png (27352 bytes)
      chunk IHDR at offset 0x0000c, length 13
        1113 x 885 image, 32-bit RGB+alpha, non-interlaced
      chunk bKGD at offset 0x00025, length 6
        red = 0x0000, green = 0x0000, blue = 0x0000
      chunk pHYs at offset 0x00037, length 9: 2835x2835 pixels/meter (72 dpi)
      chunk tIME at offset 0x0004c, length 7:bbe Mar 2020 00:45:09 UTC
      chunk iTXt at offset 0x0005f, length 29, keyword: Comment
        uncompressed, no language tag
        no translated keyword, 18 bytes of UTF-8 text
      chunk EASY at offset 0x00088, length 8192:  illegal (unless recently approved) unknown, public chunk
    ERRORS DETECTED in 7uffy_1.png

EASY. That's not in the specification. Looking at the output of the previous command, there are no IDAT chunks in there. Perhaps this chunk is supposed to be IDAT. Not mentally ready for a hex editor yet :). Let's see if we can get around it. I wish sed worked on binary files. Short google search reveals there's a tool called bbe doing just that. Dead end, could not get it to work. But I'm on a quest, so I'm not giving up:

    asd007@host:/mnt/e/ctf$ echo -n "EASY" | xxd -ps
    45415359
    asd007@host:/mnt/e/ctf$ echo -n "IDAT" | xxd -ps
    49444154
    //let's get creative and take a risk
    asd007@host:/mnt/e/ctf$ hexdump -ve '1/1 "%.2X"' 7uffy.png | sed 's/45415359/49444154/g' | xxd -r -p > 7uffy_1.png
    asd007@host:/mnt/e/ctf$ diff 7uffy.png 7uffy_1.png
    Binary files 7uffy_1.png and 7uffy_2.png differ

Wohoo! Let's take a breather. (chug beer)
    
    asd007@host:/mnt/e/ctf$ pngcheck -v 7uffy_1.png
    File: 7uffy_1.png (27352 bytes)
      chunk IHDR at offset 0x0000c, length 13
        1113 x 885 image, 32-bit RGB+alpha, non-interlaced
      chunk bKGD at offset 0x00025, length 6
        red = 0x0000, green = 0x0000, blue = 0x0000
      chunk pHYs at offset 0x00037, length 9: 2835x2835 pixels/meter (72 dpi)
      chunk tIME at offset 0x0004c, length 7: 26 Mar 2020 00:45:09 UTC
      chunk iTXt at offset 0x0005f, length 29, keyword: Comment
        uncompressed, no language tag
        no translated keyword, 18 bytes of UTF-8 text
      chunk IDAT at offset 0x00088, length 8192
        zlib: deflated, 32K window, maximum compression
      chunk IDAT at offset 0x02094, length 8192
      chunk IDAT at offset 0x040a0, length 8192
      chunk IDAT at offset 0x060ac, length 2584
      chunk IEND at offset 0x06ad0, length 0
    No errors detected in 7uffy_2.png (10 chunks, 99.3% compression).
    
Fingers crossed! It Worked!

# Flag

![](../Images/7uffy_1.png)

