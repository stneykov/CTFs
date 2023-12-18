# Cyber secret (crypto) (author: stn)

## Tools

**john the ripper** - password cracker tool, our tool of choice

**zip2john** - extracts hashes from encrypted zip files

**bkcrack** - password cracking tool capable of executing a plain-text attack

## Description

```
Santa locked next flag in encrypted zip file. Please help the elf get it!
```

## Task analysis & solution

As the description states, we're provided with an encrypted zip file. And as with all such challenges, I fired up zip2john to extract the hash, which I would subsequently try to figure out with **john**. This didn't exactly go as planned.

Now, to extract the hash, we need to run the following command:

```shell
└─$ zip2john my_xmas_flag.zip > passwordhash.txt

ver 2.0 my_xmas_flag.zip/flag.txt PKZIP Encr: cmplen=58, decmplen=46, crc=9DA8E57E ts=75D1 cs=9da8 type=0
ver 2.0 my_xmas_flag.zip/MilestoneXProtectVMSProducts2023_R3(23.3a)_Readme.txt PKZIP Encr: cmplen=4963, decmplen=4951, crc=3D4BDC1F ts=8411 cs=3d4b type=0
NOTE: It is assumed that all files in each archive have the same password.
If that is not the case, the hash may be uncrackable. To avoid this, use
option -o to pick a file at a time.

```

The tool will run its magic on Cake.doc and then save the output to passwordhash.txt. Let's see what's in there:

```
my_xmas_flag.zip:$pkzip$2*1*1*0*0*24*3d4b*8d4cff8b425e7cd98217effb83a9139d253c53f7f3413f2b90ec6600c7c39882cf0534f3*2*0*3a*2e*9da8e57e*0*26*0*3a*9da8*d1441b7cc7d40394a532c55258424ec80f1e150e81a045621e7bb6b3ba4264c18923cc3262f83f6488ef985144884668a1408d4cfffb40aa2985*$/pkzip$::my_xmas_flag.zip:flag.txt, MilestoneXProtectVMSProducts2023_R3(23.3a)_Readme.txt:my_xmas_flag.zip
```

Now we need to run John with a wordlist file and this password hash. What John will do, is that the tool will start hashing all the predefined strings in that wordlist and compare them to the hash we extracted previously. This is the so-called dictionary attack. It will run until it finds the password, or until it runs out of words :)) The wordlist of choice here would come from rockyou.txt. The command would be as follows:

```shell
└─$ john passwordhash.txt--wordlist=rockyoutxt                                                                                                                                                                                           
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:00 DONE (2023-12-17 05:13) 0g/s 17929Kp/s 17929Kc/s 17929KC/s "2flower"..*7¡Vamos!
Session completed. 

```

And... NOTHING. I was surprised at this point. Even reran my command several times, but it would just exit, session completes, no password is cracked, no nothing. I was getting a bit nervous at this point. Then I remembered something I saw when running the first command:

```
ver 2.0 my_xmas_flag.zip/flag.txt 
ver 2.0 my_xmas_flag.zip/MilestoneXProtectVMSProducts2023_R3(23.3a)_Readme.txt 
```

There are two files inside this zip. One of them is actually a known file, Milestone's very own Readme that we upload on our website along with the installer for 23.3a (so I can easily get my hands on it). That must have some meaning. So I started googling for decrypting zips that contain several files, even known files and I found something called **known plaintext attack**. Now, for us to execute that type of attack, we need to know at least 12 bytes of plaintext, that would allow us to break the cipher of the encrypted data. Luckily for us, we have one of the zipped files (assuming it wasn't edited), but we also know what the flag.txt would contain - **MilestoneCTF{**. 

I put what I know in a text file:

```shell
└─$ echo -n -e 'MilestoneCTF{' > plaintext.txt
```

Then I used the plaintext.txt file to launch an attack with bkcrack (I had to download that tool manually, as it wasn't preinstalled on Kali Linux). I put everything we need in its directory.

```shell
└─$ ./bkcrack -C my_xmas_flag.zip -c flag.txt -p plaintext.txt
bkcrack 1.5.0 - 2022-07-07
[02:45:50] Z reduction using 6 bytes of known plaintext
100.0 % (6 / 6)
[02:45:50] Attack on 986347 Z values at index 6
Keys: ac4276d1 fb611cdc ef2ff976
```

The three keys are what we need, to actually 'decrypt' the content.

I'll make a note here: we can see in the output, that we're attacking with 6 bytes, that's half of the recommended 12 and it might be slow. Thus IMHO, there's a better way to about this. Namely, using the **MilestoneXProtectVMSProducts2023_R3(23.3a)_Readme.txt** from Milestone's website, which is the second file in the archive. Here are the first few lines of the file:

```
************************************************
Milestone XProtect  VMS Products 2023 R3 (23.3a)
************************************************
```

Thus, we execute the same command, with more plaintext data but with the other file specified:

```shell
└─$ echo -n -e '************************************************' > plaintext2.txt

└─$ ./bkcrack -C my_xmas_flag.zip -c "MilestoneXProtectVMSProducts2023_R3(23.3a)_Readme.txt" -p plaintext2.txt
bkcrack 1.5.0 - 2022-07-07
[03:54:25] Z reduction using 41 bytes of known plaintext
100.0 % (41 / 41)
[03:54:25] Attack on 197948 Z values at index 6
Keys: ac4276d1 fb611cdc ef2ff976
```

Now that we have our keys, there's only one thing to do - get the decrypted data. The command is almost the same, except we need to pass our keys with a -k switch and a decrypted file name with a -d switch:

```shell
└─$ ./bkcrack -C my_xmas_flag.zip -c flag.txt -k ac4276d1 fb611cdc ef2ff976 -d decrypted_flag.txt
bkcrack 1.5.0 - 2022-07-07
[16:03:26] Writing deciphered data decrypted_flag.txt (maybe compressed)
Wrote deciphered data.
```

Deciphered data is written and our flag is:

```shell
└─$ cat decrypted_flag.txt 
MilestoneCTF{sometimes-password-is-not-enough}
```
