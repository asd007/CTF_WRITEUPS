# Problem Statement:
Whoops. It seems Luffy played with my picture and I'm not able to open it anymore. Please help me.

Creator: 2phi

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

Bingo! 8 "unknown" chunks

# Fix

