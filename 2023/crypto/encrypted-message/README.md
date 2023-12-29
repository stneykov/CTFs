# Encrypted message (crypto) (author: stn)

## Tools

**nmap** – a network scanner, useful for scanning certain hosts for open ports and figuring out what is listening on those
**telnet client tool** – command-line tool used for initiating a connection to the target machine, could be installed as a Windows feature (PuTTy is also an option here, or any other tool that can perform the same)

*some IDE and a bit of programming knowledge might go a long way with the last part of the solution, although there probably is a tool online that could help with the same.


## Description

```
We managed to capture a message containing the flag. Unfortunately, the flag is encrypted. Not sure if it helps, but we have a public key that was used to encrypting the message.

MilestoneCTF{kRVmqUDmNbH8pjKYbZIsbQ==}
```

> [public_key.key](./public_key.key)

## Task analysis & solution

In the description of this challenge, we are also provided with a public_key.key file, that contains the public part of the key used to encrypt. Our task here is to generate a private key, which we can then use to decrypt the flag. 

The public key we have is an RSA key:

```shell
└─$ openssl rsa -pubin -inform PEM -text -noout < public_key.key
Public-Key: (128 bit)
Modulus:
    00:9d:d3:81:34:9e:8a:31:c6:69:4a:8c:9a:78:5b:
    7c:cf
Exponent: 65537 (0x10001)
```

It is actually a very small number of bits for the modulus (typically a minimum of 512), so that should make the task easy-ish.

But first, in order to solve this, we need to know how RSA keys are formed. Let's have the basics outlined here:

```
RSA modulus (n) is the product of two large prime numbers p and q, n = p * q

The derived number (e) must be greater than 1 and less than (p - 1)(q - 1), also, e and (p - 1)(q - 1) are coprime

Public key is formed by the pair (n, e) and they're made public. Large p and q ensure that RSA is very hard to crack.

Private key is calculated from p. q and e. For any given n and e, there's a d. Number d is the inverse of e modulo (p - 1)(q - 1).
```

Now we know the greatest strenght of RSA is comes from selecting two very large numbers p and q that would then form the n. In our case, n seems to be very small, so this makes the encryption weak and easily crackable.

As a first step, we need to convert our modulus (00:9d:d3:81:34:9e:8a:31:c6:69:4a:8c:9a:78:5b:7c:cf) to a decimal. First we need to strip the colon signs then convert, we end up with:

```
n = 209786990575151660853627245258243538127
```

Now we can use an online tool like http://factordb.com/ to factorize that number. What we get is:

```
p = 13875965828226893617
q = 15118730701137708031
```

Let's put everything together within some python code and see what happens. I personally prefer C#, but unfortunately the RSA algorithms have some induced size limitations that won't work in our case... Google is your friend when it comes to scripts like these :)

```python
import sympy
import base64

def rsa_decrypt(ciphertext, private_key):
    n, d = private_key
    encrypted_message = int.from_bytes(base64.b64decode(ciphertext), byteorder='big')
    decrypted_message = pow(encrypted_message, d, n)
    return decrypted_message.to_bytes((decrypted_message.bit_length() + 7) // 8, byteorder='big').decode('utf-8')

n = 209786990575151660853627245258243538127
p = 13875965828226893617
q = 15118730701137708031
e = 65537

phi_n = (p - 1) * (q - 1)
d = sympy.mod_inverse(e, phi_n)
private_key = (n, d)
decrypted_text = rsa_decrypt("kRVmqUDmNbH8pjKYbZIsbQ==", private_key)

print("Decrypted Text:", decrypted_text)

```

```shell
└─$ python rsa_decrypt.py
Decrypted Text: k3y_5iz3_m@tt3r5

```

The flag we are looking for is **MilestoneCTF{k3y_5iz3_m@tt3r5}**