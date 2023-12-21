# The Christmas key (osint) (author: stn)

## Tools

**SSH tooling** - both ssh-keygen and ssh itself are available on Windows and Kali Linux by default.

**putty** and **puttygen** could also be used :)

## Description

```shell
Here you go, a mysterious key - can you find the lock it opens?

#-----BEGIN OPENSSH PRIVATE KEY----- 
#b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW QyNTUxOQAAACAfK9/sU/RNgYLrBCn6upy2EWZTh3lK3L/9Guz65R4AAQAAAKipXYqHqV2K hwAAAAtzc2gtZWQyNTUxOQAAACAfK9/sU/RNgYLrBCn6upy2EWZTh3lK3L/9Guz65R4AAQ AAAECp31B4R4Z/w4Vuq/u42VZyRwNTb7R3YROF+lCdwF1YrB8r3+xT9E2BgusEKfq6nLYR ZlOHeUrcv/0a7PrlHgABAAAAI2d1ZXN0QHNvbWUtaW5zdGFuY2Utb24tdGhlLWludGVybm
#V0AQI= 
#-----END OPENSSH PRIVATE KEY-----

PS. we have been a bit lazy here… the key have been used from both sides of the lock...
```

## Task analysis & solution

First thing's first - this is an OSINT challenge. OSINT stands for open-source intelligence or in other words, we should be able to find the information we need online through search engines, social media etc etc. No special tools or knowledge should be required here.. Well, it might be beneficial to know how cryptographic keys work, but in a few words - every private key has a corresponding public key and they both form a pair that is used to secure communication between a client and a server. During authentication, the private key signs challenges presented by the server, allowing it to verify your identity without the key ever leaving your machine, ensuring a secure and encrypted connection.

Now, we got ourselves a private key and the description of the task also mentions that they've been lazy and that key is used from both sides of the lock :) I think it is time we extract some more information about this key. We can try and create a public key out of it. After a quick online search, we stumble upon this thread on StackOverflow that explains how we can create a public openssh key out of the private one: https://stackoverflow.com/questions/38290929/generate-public-ssh-key-from-private-key

Let's give this a go. First save the actual private key into a file and then we can run the **ssh-keygen** command on that (ssh-keygen is available in both Kali and Windows). 

One very **IMPORTANT NOTE** here - make sure the editor you are using to save the private key data is using Unix line feed / carriage returns (Select UNIX LF bottom right in Notepad++ for example). For some reason, ssh-keygen is very nitpicky about the format of the file and if it doesn't end up in a single LF, it will continue to throw an error when trying to generate a public key. We actually spent a lot of time on this and it was proving to be a showstopper, until we used ssh-keygen to generate a pair of keys and compare them to what we had. This helped us move forward.

```shell
PS C:\Users\stn\cyberxmas> ssh-keygen -y -f .\openssh-key.private
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIB8r3+xT9E2BgusEKfq6nLYRZlOHeUrcv/0a7PrlHgAB guest@some-instance-on-the-internet
```

Ok, let's break this output down:

```shell
#ssh-ed25519
This is the type of cryptographic algorithm used for key generation.

#AAAAC3NzaC1lZDI1NTE5AAAAIB8r3+xT9E2BgusEKfq6nLYRZlOHeUrcv/0a7PrlHgAB
The actual public key in base64

#guest@some-instance-on-the-internet
This is additional information about the key, such as the user - guest, and the system - some-instance-on-the-internet
```

At this point, we need to remember that we've been provided with two internet infrastructure search engines in the Cyberxmas tools list - search.censys and shodan.io. Even though **some-instance-on-the-internet** sounded way too generic, I decided to look for it on shodan.io. Expectedly, this didn't yield any results, but then I noticed something very specific (I'm not well versed into these search engines, I must admit. So I had to research into them on the fly). In the cheat sheet of the website, there is an example of how to search for ssh servers, which is what we are looking for, based on the private key that we have. The search just goes for a special filter on port 22, but that's not the most important part.

![Image Preview](1.png)

If you look closely at that first result, you can see there's **key type** (which we know), but also the **key**, which should be our public key, as that is what the server will present. Let's try and search for the public key we generated:

![Image Preview](2.png)

Sweet! We got an exact match - a machine with IP 3.65.97.242, which is probably our **some-instance-on-the-internet**. Now we need to remember what the description said, the key was being used from both sides, which should mean that we can initiate an ssh connection with the private key file that we already saved, on address 3.65.97.242 and user **guest**.

```shell
PS C:\Users\stn\cyberxmas> ssh -i .\openssh-key.private guest@3.65.97.242
The authenticity of host '3.65.97.242 (3.65.97.242)' can't be established.
ED25519 key fingerprint is SHA256:C8TXP9zdI8JXmHL3euNoYf9ylWfaB1pVfgLLpmm+3ho.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '3.65.97.242' (ED25519) to the list of known hosts.

A newer release of "Amazon Linux" is available.
  Version 2023.3.20231211:
  Version 2023.3.20231218:
Run "/usr/bin/dnf check-release-update" for full release and version update info
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
Last login: Thu Dec 21 07:27:42 2023 from 195.34.113.66
[guest@ip-192-168-200-12 ~]$
```

Awesome, we are in. Now let's look around:

```shell
[guest@ip-192-168-200-12 ~]$ ls -R
.:
flag

./flag:
flag.txt
[guest@ip-192-168-200-12 ~]$ cat flag/flag.txt
MilestoneCTF{I-proudly-announce-That-I-Found-THE-FLAG}
```

We first list dirs and files with **ls -R** and we can see there's a flag folder containing a flag.txt, so we just show the content of it.

And the flag is **MilestoneCTF{I-proudly-announce-That-I-Found-THE-FLAG}**