# Problem Statement

I have a very big file for you. I hid a present inside.

Classic tools wont be useful here.

Connect with ssh big@sharkyctf.xyz -p 9000. Password : big.

Creator : Nofix

# Solution
I will try the best I can to write this up, but the ssh session will probably no longer be available later... I will try to  "re-make the scenario". The difficulty in this one was the tool limitation (trivial to solve with cp, dd, du and probably other tools, which were not available in the ssh session). Perhaps I'm missing something more obvious...

Need sleep, full writeup will come. Keywords: stat --format="%b" /home/big/my_huge_file, filepart, sparse file
