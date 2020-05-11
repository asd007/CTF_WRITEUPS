# Problem Statement

I intercepted an image in the communication of 2 sharkies from a shark gang. Those sharks knew I was listening and they hid a message in this image.

Do you think you can do something about it?

Creator: Fratso

[pretty_Cat]

# Analysis

The file mentions LSB openly. Least significant bit information hiding is a method of hiding information in images, audio and videos by modifying information in such a way that a human would not have a visual or audio clue while viewing the file that there is information hidden. Whether it's color or audio, the least significant byte usually has the less impact on the visual/audio. For visuals such as images, when the least significant bits, means the least significant bit of each pixel.

There are tools to detect and extract such information, ehich could probably be used to extract this data (and I used one during the CTF but I can't remember which one). With time pressure no longer an issue, let's have some fun.

First things first:

    asd007@host:/mnt/e/ctf$ file pretty_cat.png
    pretty_cat.png: PNG image data, 800 x 674, 8-bit/color RGB, non-interlaced
    
We're in luck, it's an 8 bit image, so 1 byte per pixel, so no need to worry about "color channels"? (I think). 

(I spent way too much on this trying to be cool) 
* I spent hours battling xxd and hexdump and awk and sed. Binary manipulation is not bash's strong part. 
* Ok I'll write some (surprised?):C# code for this. 
* I spent more hours battling C# and the intricacies of the png file format. 

I could probably write such a tool given time and not from scratch (wanted to be cool and use no external libraries/code/inspiration). At least now I know it's not necessarily trivial to write something like this.

In the end here is the solution I really used in the CTF:

When I have time I might actually write a tool, probably based around pngtools, as I don't trust C# and its forced 24bpp Indexed format.

Here we go, our bitter win

![](/Images/stego_!.png)

# Flag
shkCTF{Y0u_foUnD_m3_thr0ugH_LSB_6a5e99dfacf793e27a}





