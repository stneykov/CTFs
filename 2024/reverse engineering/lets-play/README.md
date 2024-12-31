# Let's play! (reverse engineering) (authors: M1dwinter.sln)

## Description

```shell
Hey,

You've been working so hard on the challenges, it time to rest a bit :)

Can you reach the terminal which hides the secret?

https://4d6b0c4c5f954ed3a5192354.z6.web.core.windows.net/
```

## Task analysis & solution

The provided link leads to a 2D game where you can move your player using arrow keys. The interface includes:

- Coordinate Indicator (bottom left): Displays your player’s position.
- Star Counter (top right): Keeps track of collected stars.
- Save and Load Buttons (top left): Save the game state or load a previously saved state.
- Terminal: The goal of the challenge, but it is surrounded by obstacles, preventing direct access

When using the Save button, a hex string appears in the input field. Experimenting with movement and saving again reveals that the hex string changes based on your position. This suggests that the string encodes game state data, including player coordinates.

Instead of manually testing the Save/Load mechanics, I inspected the game’s JavaScript code by opening the browser’s Developer Tools. The code was heavily obfuscated, as shown below:

```javascript

(function(_0x35e314,_0x2b8738){const _0x322316=_0x35e314();
....

```

I decided to run that through https://deobfuscate.io/ which redirected me to https://obf-io.deobfuscate.io/

While the deobfuscated code wasn't much more readable (most likely obfuscated more than once), quickly going through it would reveal a very peculiar function with an alert call inside:

```javascript

function xo() {
      alert("7d7434337033722d6434304c2d79663164306d2d337634737b465443656e6f7473656c694d");
};
```

That looked interesting. The string inside appears to be a hex representation of something, so I quickly plugged it into CyberChef (https://cyberchef.org/) using the 'From Hex' recipe - CyberChef is invaluable for CTF challenges, as you can quickly test any input against a plethora of transformation mechanisms. This yielded the following result:

```shell
}t43p3r-d40L-yf1d0m-3v4s{FTCenotseliM
```

Ok, that looked like a reversed flag :) Let's continue with a 'Reverse' recipe in CyberChef:

```shell
MilestoneCTF{s4v3-m0d1fy-L04d-r3p34t}
```

And there we go, the flag is *MilestoneCTF{s4v3-m0d1fy-L04d-r3p34t}* :)

### Working with the Save/Load functionality

Deobfuscating that code revealed yet another solution, exploiting the Save/Load functionality of the game. Here's the code to the save function:

```javascript
function saveGame() {
  let _0xba7dcf = new ArrayBuffer(10);
  let _0x2d953f = new DataView(_0xba7dcf);
  _0x2d953f.setUint8(0, 83);
  _0x2d953f.setUint8(1, 71);
  _0x2d953f.setInt8(2, playerX);
  _0x2d953f.setInt8(3, -playerX);
  _0x2d953f.setInt8(4, playerY);
  _0x2d953f.setInt8(5, -playerY);
  _0x2d953f.setInt16(6, starCount, true);
  _0x2d953f.setInt16(8, -starCount, true);
  let _0xeb9a9d = '';
  for (let _0x57f9d1 = 0; _0x57f9d1 < _0xba7dcf.byteLength; _0x57f9d1++) {
    _0xeb9a9d += ('0' + _0x2d953f.getUint8(_0x57f9d1).toString(16)).slice(-2) + " ";
  }
  prompt("Save Game:", _0xeb9a9d.trim());
}
```

This function generates a save state in hex format, encoding:

- Player X/Y coordinates (positions 2–5).
- Star count (positions 6–9).


To bypass the obstacles and reach the terminal, I crafted a hex string that places the player near the terminal:

```shell
53 47 0e f2 07 f9 00 00 00 00

- 53 47: Header (S and G in ASCII).
- 0e f2: X = 14, -X = -14.
- 07 f9: Y = 7, -Y = -7.
Remaining values represent the star count, initially set to 0.
```

Once near the terminal, moving right triggers an alert stating "You need 1337 star points". To bypass this, I updated the star count in the hex string:

```shell
53 47 0e f2 07 f9 39 05 c7 fa

- 39 05: Star count = 1337.
- c7 fa: Negative star count = -1337
```

Loading this save and moving one step to the right calls the same alert I found earlier ;)