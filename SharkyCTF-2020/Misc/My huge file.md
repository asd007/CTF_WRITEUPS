# Problem Statement

I have a very big file for you. I hid a present inside.

Classic tools wont be useful here.

Connect with ssh big@sharkyctf.xyz -p 9000. Password : big.

Creator : Nofix

# Solution

Let's see!

    big@ee6e73167aa0:~$ ls -l
    total 32
    -rw-r--r-- 1 root root 17592176640066 May 10 08:33 my_huge_file

Oh wow! It's huuuge (queue jokes here). Mother of the unholy it's huge. No way we can lookup anything. Let's be dumb:

    big@ee6e73167aa0:~$ cat my_huge_file
    😏                                                                                                

Funny to see how this was actually an emoji =)) Nice touch, NoFix! On my screen it's just 3 weird printable characters. I even had a theory it was some kind of offset. Spoiler: it wasn't :)

Add some naive directionless scripting to try and get some "listing" going (which I deleted because of the same naive scripting that lead me to se see other people's /tmp folders on the machine and didn't want my precious flag stolen). I could not find anything useful myself, but at the point that I saw other people's work I also sort of knew what I needed, and wanted to avoid C/Python for as long as possible (again, I'm a very lazy individual).

On to the most fallback fallback of fallbacks (no hexdump, no xxd, no cp, no bless, the list goes on), I find a command/tool that at least outputs something:

    big@ee6e73167aa0:~$ od -xc my_huge_file
    0000000    9ff0    8f98    0000    0000    0000    0000    0000    0000
            360 237 230 217  \0  \0  \0  \0  \0  \0  \0  \0  \0  \0  \0  \0
    0000020    0000    0000    0000    0000    0000    0000    0000    0000
             \0  \0  \0  \0  \0  \0  \0  \0  \0  \0  \0  \0  \0  \0  \0  \0
    *
    ^C

I had to stop it as it had stopped outputting rows after that second line. Hmmm a whole bunch of nulls, I wonder if there's more? I tried sed/tr, but that would take forever. I can't see them, but so many nulls and traps for regexes and no way to see progress. Machine specs were also probably tweaked to eliminate this approach. To make things worse...

    big@ee6e73167aa0:~$ file my_huge_file
    my_huge_file: UTF-8 Unicode text, with no line terminators

What a pickle! Then it hit me. I've heard of sparse arrays/matrices and sparse tables in databases. What if there's such a thing as a sparse file and some magic tools to deal with it? So I took this new keyword and googled around.

Having eliminated solutions that relied on cp and tar and others because of the limitations on the console, the most promissing of the bunch seemed (https://unix.stackexchange.com/questions/521917/how-to-view-contents-of-a-sparse-file). 

I did not use this in the CTF, I used filepart to identify the data segments and then wrote a python script to read the length 42 block - since 42 is the answer to everything, the prefix was obvious But this code's output is too beautiful not to share, a tool that should have been part of the tooling all along :):

    [copy the file sparse_cat.c from said thread, then:]
    big@ee6e73167aa0:/tmp/NaN$ cc -Wall -O2 sparse_cat.c -s -o sparse_cat
    big@ee6e73167aa0:/tmp/NaN$ ./sparse_cat </home/big/my_huge_file >/dev/tty
              0             1000
    😏
    47868c00000      47868c01000
    s   
    77359400000      77359401000
     h 
    a6e49c00000      a6e49c01000
      k 
    e57a5680000      e57a5681000
       C 
    ffffe380000      ffffe381000
        TF  
    fffff708000      fffff708042
          {sp4rs3_f1l3s_4r3_c001_6cf61f47f6273dfa225ee3366eacb8eb}

# Flag:

shkCTF{sp4rs3_f1l3s_4r3_c001_6cf61f47f6273dfa225ee3366eacb8eb}
