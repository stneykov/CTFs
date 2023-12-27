# Flag? What flag? (misc) (author: stn)

## Tools

I have referred to ffmpeg here, but there's a plethora of online tools that can be used and ffmpeg is not at all a requirement.

## Description

![Image Preview](not_a_flag.gif)

## Task analysis & solution

In the challenge's description, we are only provided with a single gif file. I was a bit skeptical about the flag being hidden in there, but upon downloading it, one couldn't help but notice the size of the file - 35mbs. This can't be a simple gif with a blinking man ;) 

If we look at the gif animation more closely, we can identify a few 'states'. Quick blinks, long blinks, extended periods of time where eyes of the man are open. This vaguely resembles morse code, so we decided to go with that. We even found an open source project on github that does exactly that - turns a message into morse code and then that code to a gif file that consists of just two frames (or states): eyes open and eyes closed. The project is this one: https://github.com/pgrauman/morsegif

### Approach A

A brute-force kind of way to go about decoding the morse code. We could turn the gif into a video file and then play it back slowed down, writing down the morse code: quick blinks for dots (.) and long blinks for dashes (-). There are numerous online converters that can do this, but I have **fmpeg** handy and I like the tool, so this is how you can convert the gif to mp4 with it:

```shell
ffmpeg.exe -i not_a_flag.gif -pix_fmt yuv420p not_a_flag.mp4
```

### Approach B

Approach A is fine, but a bit boring for my taste :) I did some studying of the python code for morsegif and I noticed that there are several parameters: how many 'eyes closed' frames there would be for dots, how many for dashes, how many 'eyes opened' frames would represent a character end and word end, how many 'eyes opened' frames are we going to start with (please note we have no way to verify that this code was actually used for creating the gif file, it was all a speculation). All of this lead me to believe that if I could split the gif into frames and find a way to create a sequence of 0s and 1s, representing both states, I should be able to deduce the morse code out of it. So I got to work..

First step was to split to frames. Again, this could be done with online converters, but ffmpeg was my tool of choice:

```shell
ffmpeg.exe -i not_a_flag.gif frame_%d.png
```

The %d is just a placeholder, it will create a counter and include a subsequent number into each file name. So far, so good, I had my folder full of PNGs. At a quick glance, there were just the two frames, so I could whip a script that creates a sequence of 0s and 1s based on the images. I used the size for an indication of what's what:

```powershell
$imageFolderPath = "<path-to-frame-pngs>"

$imageFiles = Get-ChildItem -Path $imageFolderPath -Filter *.png
$firstImageSize = $imageFiles[0].Length

$imageFiles = $imageFiles | Sort-Object { [int]($_.BaseName -replace '\D') }

foreach ($imageFile in $imageFiles) 
{
    $imageSize = $imageFile.Length

    if ($imageSize -eq $firstImageSize) 
    {
        "0" | Out-File -Append -FilePath gif_output.txt -NoNewline
    }
    else
    {
        "1" | Out-File -Append -FilePath gif_output.txt -NoNewline
    }
}
```

We end up with similar output (just the first 100 symbols here)

```shell
0000010101010111100011110101000000000000011110101010100011110111101111011110100000000000001111010101...
```

Looking at the gif, it starts with a long open eyed pause, then four quick blinks, one drawn out blink and a quick pause. What we can assume from that is:
1. a quick blink (dot) is 10
2. a long blink (dash) is 11110
3. the starter sequence is 00000
4. the character separator is 00
5. whatever is left (000000000000) is the word separator

With those assumptions in mind, we can do some quick replacements in our output file either with a text editor or another script (update the old one)

```powershell
$outputContent = Get-Content -Path gif_output.txt -Raw
$outputContent = $outputContent -replace '11110', '-' -replace '10', '.' -replace '00000', '' -replace '00', ' ' -replace '000000000000', '  '
$outputContent | Out-File -FilePath morse_code.txt -Force
```

It is important we do the 11110 first, because doing 10 before that will mess up the output.

We end up with this representation of morse code:

```shell
....- -.. -.... ----. -.... -.-. -.... ..... --... ...-- --... ....- -.... ..-. -.... . -.... ..... ....- ...-- ..... ....- ....- -.... --... -... -.... --... -.... ----. -.... -.... --... ...-- ..--- -.. -.... .---- --... ..--- -.... ..... ..--- -.. -.... ...-- -.... ..-. -.... ..-. -.... -.-. --... -.. 
```

Plug this representation into any online morse code translator and we get:

```shell
4D 69 6C 65 73 74 6F 6E 65 43 54 46 7B 67 69 66 73 2D 61 72 65 2D 63 6F 6F 6C 7D
```

Converting the hexadecimal representation above to ASCII (with CyberChef), we get our flag: **MicrosoftCTF{gifs-are-cool}**