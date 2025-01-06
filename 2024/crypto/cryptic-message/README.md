# Cryptic message (crypto) (author: stn)

## Description

```shell
We've received a cryptic message, but we don't know how to read it. What could it contain? A flag? A story? Or a bitcoin wallet? Who knows, it could be anything.
```
[CrypticMessage.txt](CrypticMessage.txt)

## Task analysis & solution

We're provided with a message in the CrypticMessage.txt file.

```shell
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
```

It is obvious to the trained eye that this is Base64 encoded data. The hard part comes afterwards - the resulting byte data and what it represents. I saved it to a file and ran it through `binwalk` and `file`, unfortunately, those didn't yield results for any known file types or data structured in a particular way - it was what it was, just plain byte data. I even went through the trouble to compare it to data that represents a bitcoin wallet, following that 'hint' in the description of the challenge.

Now, considering this is a crypto challenge and having exhausted all my crypto wallet possibilities, I had to move on to the next hint in the description. The provided data looked to be a little too long to be just a simple flag, thus it must be a story, whatever that might mean. In my mind, if this was a story then it must be in plain English and it is also 'encrypted' in some way to reflect the category of the challenge.

I did a little investigation online and my working theory at the time was that the data might be XOR ciphered. XOR ciphers are relatively easy to use, very fast and are widely used in CTF challenges as a way to introduce people to cryptography problems. I read that you can actually measure the data's entropy to get an understanding of whether it might have been XORed. So I went to our trusted tool CyberChef and plugged the data, first decoding it from Base64 and then using the ``Entropy`` recipe. This is what it came up with:

```shell
Shannon entropy: 6.569903110128441
        


        - 0 represents no randomness (i.e. all the bytes in the data have the same value) whereas 8, the maximum, represents a completely random string.
        - Standard English text usually falls somewhere between 3.5 and 5.
        - Properly encrypted or compressed data of a reasonable length should have an entropy of over 7.5.

        The following results show the entropy of chunks of the input data. Chunks with particularly high entropy could suggest encrypted or compressed sections.
```

The good thing about my theory is that XOR ciphers are vulnerable to known plaintext attacks. In our case, if this is a story, then I assume the most common character would likely be a space. The process of cracking is nowhere near simple, but here's how I understand it:
- we need a way to determine a likely key length. There are several approaches and one of them is calculating the ``Index of Coincidence`` for different lengths, let's say in the range of 2 to 32. What IC does is that measures how often characters are being repeated within each block of ciphertext, when split by the key length.
- for each candidate key length, we split the ciphertext into blocks of that length and we analyze each block's character frequency. Or in our case we XOR the most frequent bytes with 0x20, which is space in ASCII.

Luckily for me, I found a tool online that does all of this for me - https://node-security.com/posts/xor-encryption-cracker/. I quickly input the base64 supposed ciphertext and left the default options in for minimum and maximum key lengths. To my disbelief, I got the story back:

```
The Christmas Firewall

It was a snowy Christmas Eve, and the Security Operations Center (SOC) at Milestone was eerily quiet. Sam was halfway through his second mug of hot chocolate when Jess called out from her terminal.

“Hey, I’m seeing some strange activity targeting our firewall,” she said, spinning her chair around.

Sam joined her and looked over the logs. There it was—requests originating from an IP labeled "Santa’s Workshop." The user agent? "SleighBrowser v2.5".

Sam chuckled. “Festive, but suspicious. What’s it hitting?”

Jess frowned as she drilled down. “Looks like they’re probing for vulnerabilities in the main firewall. Oh, here we go—someone’s trying to exploit that old misconfiguration we patched last quarter.”

But then Jess paused, pointing to something peculiar. “Wait, look at this payload.”

They expanded the logs and found a string buried in the attack code:

4d696c6573746f6e654354467b786f722d6369706865722d776974682d72657065617465642d6b65797d  

Sam raised an eyebrow. “Are we the target, or just the backdrop for someone’s festive cyber prank?”

Jess smirked. “Great, now we’re part of a Christmas-themed cyber scavenger hunt.”

Curiosity piqued, they ran a quick analysis on the string but left it untouched for the incident report. Whoever sent it was clearly more interested in showing off than causing damage—for now.

“Still, we can’t take chances,” Sam said. Together, they reviewed the firewall configurations, double-checked patch deployments, and locked down the attack vector. Within an hour, their systems were fortified, and the malicious IP was blocked.

As they wrapped up, Jess leaned back in her chair and said, “Do you think they expected us to find the flag?”

Sam laughed. “Maybe. But they probably didn’t expect us to patch up faster than Santa delivers presents.”

By midnight, the SOC was quiet again, save for the hum of the monitors. Somewhere out there, an attacker realized they’d just been thwarted by the best in the business.

And for Milestone’s customers, it was a silent—and secure—Christmas night.
```

One thing stood out to me and that was the string in the attack code - ``4d696c6573746f6e654354467b786f722d6369706865722d776974682d72657065617465642d6b65797d``. Interpreting the hex string into text we got our flag - **``MilestoneCTF{xor-cipher-with-repeated-key}``**