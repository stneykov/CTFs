# A challenge under construction (web) (author: stn)

## Tools

A web browser is all one could possibly need. My personal favourite here VSCode's REST client, although other similar tools could also be used.

## Description

```shell
Our elves started developing a site for a new challenge but... they ran out of time. Hopefully there is nothing left on the temporary website.

http://cyberxmas.milestone.dk:8080/
```

## Task analysis & solution

I started working on this web challenge as I do with pretty much all of these - by looking at the source of the page for any hints in the html, css, javascript or any other resource. Unfortunately, this one had nothing hidden in there, just a lonely JPG:

```html
GET http://cyberxmas.milestone.dk:8080/

<!--response headers omitted for brevity -->

<html>
<body>
<center><img src=workinprogress.jpg>
</denter>
</body>
</html>
```

I must admit, the malformed HTML threw me off a bit, but I couldn't think of anything related to it, so I started looking through the image with all the known tools about hidden strings, files or metadata. It looked as clean as it could be. So no luck there either.

Then I thought about checking for a robots.txt file. **robots.txt** is a text file on a website's root directory that guides web crawlers and bots. It helps control crawling, indexing, and conserves server resources by specifying which pages or sections should not be crawled. It's used for privacy, security, and efficiency purposes. The file includes directives like "User-agent" (specifying the bot) and "Disallow" (indicating disallowed paths or pages). Of course, while reputable bots do respect those rules, malicious ones might not. 

Anyway, I hit the URL for our standard robots.txt file:

```shell
GET http://cyberxmas.milestone.dk:8080/robots.txt

#<response headers omitted for brevity>

User-agent: Googlebot
Disallow: /
User-agent: Nutch
Disallow: 
User-agent: *
Disallow: /
Disallow: /cgi-bin/
Disallow: /secret-stuff-do-not-touch/
```

And indeed the elves seemed to had hidden something and forgot to clean it up. Unfortunately, browsing the /secret-stuff-do-not-touch/ directory was forbidden, but I decided to go with something standard or namely, a flag.txt in that directory:

```shell
GET http://cyberxmas.milestone.dk:8080/secret-stuff-do-not-touch/flag.txt

#<response headers omitted for brevity>

MilestoneCTF{This-one-was-not-the-hardest}
```

And I'm glad I did, as our flag was revealed **MilestoneCTF{This-one-was-not-the-hardest}** :)