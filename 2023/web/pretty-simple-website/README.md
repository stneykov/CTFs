# Pretty simple website (web) (author: stn)

## Tools

An internet browser application

Visual Studio Code with REST Client plugin - for sending easy HTTP requests (https://code.visualstudio.com/Download) (curl / wget could also be used here)

## Description

```shell
There is a flag hidden in this pretty simple website. Is it really well hidden?

#http://cyberxmas.milestone.dk:8080/justawebsite/
```

## Task analysis & solution

I like to start these by going through all the available files - html, css, js etc, and look for specific keywords like **milestone** and **ctf**, as those are the 'static' ones in every flag. I use VSCode's REST client plugin to request the web page and got this result:

```html
<html>
<body>
  <span id="element"></span>

  <script src="js/typed.ob.js"></script>

  <script>
    var typed = new Typed('#element', {
      strings: ['<i>Christmas</i> is just around the corner.', 'Never forget Nakatomi Plaza!'],
      typeSpeed: 50,
    });
  </script>
</body>
<h1>XProtect is amazing product!</h1><br>
And soon it will be build with a new amazing toolchain!
</body>
</html>
```

Nothing in the HTML, but there is a weirdly named javascript file - typed.ob.js. I assume 'ob' stands for obfuscated, but I've never seen that naming before (it has been a while since I last worked with javascript). I performed a get request for the javascript file and looked for my keywords in it. I could actually find two strings in there that kind of let me believe I was in the right direction: **MilestoneC** and **TF{obfusca**. Thus I had the first few characters of the flag - MilestoneCTF{obfusca.

After trying a couple of things that yielded no results, I looked online for javascript deobfuscators. The first result I stumbled upon was https://deobfuscate.io/ and I gave it all of our typed.ob.js file. To my surprise, the code was immediately recognized and I had this notification popup:

```
Notification
Your code looks like it has been obfuscated using Obfuscator.io, would you like to go to the deobfuscator specific for that?
```

Upon a second submission of the code, I could see the deobfuscated (to some degree) version of it, holding the flag:

```javascript
var _0x2b56bc = {
    'strings': ["These are the default values...", "You know what you should do?", "Use your own!", "Have a great day!", "MilestoneCTF{obfuscation-is-not-a-security}"],
    'stringsElement': null,
    'typeSpeed': 0x0,
    'startDelay': 0x0,
    'backSpeed': 0x0,
    'smartBackspace': true,
    'shuffle': false,
    'backDelay': 0x2bc,
    'fadeOut': false,
    'fadeOutClass': "typed-fade-out",
    'fadeOutDelay': 0x1f4,
    'loop': false,
    'loopCount': Infinity,
    'showCursor': true,
    'cursorChar': '|',
    'autoInsertCss': true,
    'attr': null,
    'bindInputFocusEvents': false,
    'contentType': "html",
    'onBegin': function (_0x1d3969) {},
    'onComplete': function (_0x457d7d) {},
    'preStringTyped': function (_0x30b715, _0x940a94) {},
    'onStringTyped': function (_0x60656, _0x4c7099) {},
    'onLastStringBackspaced': function (_0x670867) {},
    'onTypingPaused': function (_0x1b2b4a, _0x3b89fe) {},
    'onTypingResumed': function (_0x5c4ba4, _0xdef888) {},
    'onReset': function (_0x2703a3) {},
    'onStop': function (_0x3fe6ed, _0xbfadf) {},
    'onStart': function (_0x5a0844, _0x2bf5e3) {},
    'onDestroy': function (_0x52f1e2) {}
  };
```

The flag was **MilestoneCTF{obfuscation-is-not-a-security}**