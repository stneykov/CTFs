# Lost in Time (web) (author: snn)

## Tools

A web browser is all one could possibly need. I use curl in here, as my tool of choice for sending HTTP requests :-)

## Description

```shell
Visit our fancy website and get the flag. https://delightful-mushroom-0076c5703.4.azurestaticapps.net/
```

## Task analysis & solution

The first thing was to open the [link](https://delightful-mushroom-0076c5703.4.azurestaticapps.net) and read the poem :-)

```
In the world of computer challenges and code,
A story unfolds, of a flag in a digital abode.
Sadly, we must share a not-so-happy tale,
The flag, it seems, has left a virtual trail.

In the dance of cyberspace, in our digital quest,
We tried our best, put the flag to rest.
No trace to follow, no digital sign,
The flag has vanished, by design.

We searched high and low, in the vast digital sea,
To cut ties with the flag, set its data free.
But, alas, no route remains in our quest,
The flag has vanished, a secret at best.

With a heavy heart, we share this news today,
The flag's digital dance led it far away.
As we say goodbye, may your day still gleam,
In the world of cybersecurity, where dreams stream.
```

At this moment I realized that there is nothing here, so I sent a quick GET with *curl*

```shell
curl https://delightful-mushroom-0076c5703.4.azurestaticapps.net
```

Again, nothing of interest. I read the poem again and something struck a cord with me - `The flag, it seems, has left a virtual trail`. Virtual trail? These two words gave me an idea to try with one of the tools on the CyberXmas tool's list -> [Wayback Machine](https://web.archive.org). The Wayback Machine is a digital archive of sorts, it crawls sites for public information and saves copies of them, which can be reviewed at a later time.

I did a search for our site and I could see that on 4th of December, there were 4 entries captured:

```web
Saved 4 times between December 4, 2023 and December 4, 2023.
```

Curl'ed each one of them (coupled with grep) to see if the flag could be hidden somewhere on those:

```shell
curl --silent https://web.archive.org/web/20231204110732/https://delightful-mushroom-0076c5703.4.azurestaticapps.net/ | grep -n --color -C 1 "CTF"
```


and voala, the flag was in an HTML comment section on one of the captures: 

```html
28-
29:  <!-- MilestoneCTF{once-on-the-internet-always-on-the-internet} -->
30-
```

There we go, the flag was **MilestoneCTF{once-on-the-internet-always-on-the-internet}**