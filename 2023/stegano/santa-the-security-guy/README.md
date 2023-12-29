# Santa the Security Guy (stegano) (author: snn)

## Tools

binwalk, strings, file, exiftool are all decent investigation tools for hidden data within files or metadata

## Description

```
We have a very nice picture of Santa Claus as a Video Security Guy. Is it hidding some secrets?
```

> [security_santa_claus.png](./security_santa_claus.png)

## Task analysis & solution

Here in this stegano category challenge we have a file to download - **security_santa_claus.png**

The first thing that came to my mind was to open a console and use the [exif](https://github.com/exiftool/exiftool) data analyzer tool and see what we have inside. I used `exiftool` from Kali Linux:

```shell
exiftool security_santa_claus.png
```

Results:
```shell
┌──(snn㉿BGVM-SNN-KALI)-[~/Desktop/cyber]
└─$ exiftool security_santa_claus.png 
ExifTool Version Number         : 12.67
File Name                       : security_santa_claus.png
Directory                       : .
File Size                       : 1166 kB
File Permissions                : -rw-------
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Image Width                     : 800
Image Height                    : 800
Bit Depth                       : 8
Color Type                      : RGB
Compression                     : Deflate/Inflate
Filter                          : Adaptive
Interlace                       : Noninterlaced
Profile Name                    : scr
Warning                         : Error inflating iCCP
ICC Profile                     : (Binary data 303 bytes, use -b option to extract)
Image Size                      : 800x800
Megapixels                      : 0.640
```

Nothing special. 
So the next tool that I tried was strings - this is a tool that tries to read all strings from specified file

```shell
strings security_santa_claus.png
```

Results:
```shell
0000178471 00000 n 
0000178790 00000 n 
0000179242 00000 n 
0000179651 00000 n 
0000231614 00000 n 
0000232015 00000 n 
0000232497 00000 n 
0000232521 00000 n 
0000246715 00000 n 
0000246949 00000 n 
0000246973 00000 n 
0000264667 00000 n 
0000267936 00000 n 
0000267980 00000 n 
0000268122 00000 n 
trailer
/Size 77
/Root 1 0 R
/Info 57 0 R
/ID[<B5071D44CCF9114EAB68FE1A5B363DA0><2FF8B1931E9D0F4F97B501A0FD567993>]
/Encrypt 75 0 R
startxref
268172
%%EOF
flag.txt
Milestone_EULA_en-US.pdf
ZvyrfgbarPGS{qvt-qrrc-naq-svaq-lbhe-jnl-gb-lbhe-fbhy}PK
IEND
```

I got interested in the text before the IEND tag, but it is important to note that the PNG file actually hides two zipped files that are visible in the strings output above - `flag.txt` and `Milestone_EULA_en-US.pdf`. Those are decoys, but could be easily extracted by changing the extension of the file to zip :) The string `ZvyrfgbarPGS{qvt-qrrc-naq-svaq-lbhe-jnl-gb-lbhe-fbhy}` is actually a comment added to one of those files.

From the previous CTFs we were introduced to [dcode.fr](https://www.dcode.fr) web site:

![Alt text](image-dcode.fr.png)

There is a section where it can try to find the cypher, so let's give it a try:

![Alt text](image-dcode.fr.analyze.png)

On the left side there are the results:

![Alt text](image-dcode.fr.analyze.result.png)

Click on `ROT-13` Cypher and decrypt the message:

![Alt text](image-dcode.fr.decrypt.ROT13.png)

:tada:
The flag is **MilestoneCTF{dig-deep-and-find-your-way-to-your-soul}**
