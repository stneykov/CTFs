# Otherworldly conversation (misc)

## Description

```
We captured this weird conversation… What does it mean? https://kind-ground-01f0dde03.4.azurestaticapps.net/

```

## Task analysis

As with any other task that starts with a web page, I fire up my beloved Visual Studio Code with REST Client plugin and send a GET request to the page. The idea is to see whether anything is hidden within the HTML. Turns out I wasn't disappointed:

```

          <div class="time">
            Today at 11:41
          </div>
          <div class="message psirt deleted">
            Hello
          </div>
          <div class="message chatbot deleted">
            Hi, how can I help you?
          </div>
          <div class="message psirt deleted">
            Do you know about any interesting encoding I can use for a CTF challenge?
          </div>
          <div class="message chatbot deleted">
            Yes, I do. There is one that can fit 16 bits into a single character. It is useful, for example, for tweeting
            long posts.
          </div>
          <div class="message psirt">
            𒁄𖤠𓍯ꔠ陥啮院驳樶栵琶
          </div>
          <div class="message chatbot">
            𖡅顡ꍴ啹鵴𓉡𒀠驮ᔮ
          </div>
          <div class="message psirt">
            鵔𐙡啫𒁹噵素𓁥啥𓅩𓈠驨鬠陬潧舠ꍩ𓅥𒁴驮襃𠅆鹦𓉴𐙩执𒁭驲餭𓉡扡𐙩𒁴阭𓄭𐙩ꍧ扥𔕴驥𠍴
          </div>
          <div class="message chatbot">
            鹎驣𒀠驮唡啉𒁨驰𒄠𒁥ꍰ啥鹷ꍬꌠꉩ啥鵴啥鵣ꍡ马鱮捥砠𐙡縠鴠ꍥ啰𒁹啵鹷鵴𓄠ꕯ𓉥鹨鱮騠𓅬瑥
          </div>
          <div class="message psirt">
            𒁎唬鵴𓉡𓄧鸠捴褠陨ꉮ啳𒁦啲𒁹𓁵鴠ꍥ捰
          </div>
          <div class="message chatbot">
            陈𒅰啹𒁴售驍𓁲啹鵃鹲𓉳陭捳
          </div>

```

There seems to be a hidden conversation (see the div tags with class deleted that aren't showing up in the page), stating that there is an encoding which can fit 16 bits into a single character and they also hint at that being used by twitter. OK, sure, I guess we could google that up.

I immediately stumbled upon this article: https://qntm.org/twitcodings 
It has mentions of **Base2048** and **Base65536**.

## Solution

I went for Base2048 first. CyberChef does not seem to be able to decode either of the two, so I had to google for another online decoder. Found this: https://nerdmosis.com/tools/encode-and-decode-base2048, but it couldn't decode the text from Base2048, so I thought I should move on. 

Found a Base65536 decoder at https://www.better-converter.com/Encoders-Decoders/Base65536-Decode and I input all the jibberish text once again.

```

𒁄𖤠𓍯ꔠ陥啮院驳樶栵琶
𖡅顡ꍴ啹鵴𓉡𒀠驮ᔮ
鵔𐙡啫𒁹噵素𓁥啥𓅩𓈠驨鬠陬潧舠ꍩ𓅥𒁴驮襃𠅆鹦𓉴𐙩执𒁭驲餭𓉡扡𐙩𒁴阭𓄭𐙩ꍧ扥𔕴驥𠍴
鹎驣𒀠驮唡啉𒁨驰𒄠𒁥ꍰ啥鹷ꍬꌠꉩ啥鵴啥鵣ꍡ马鱮捥砠𐙡縠鴠ꍥ啰𒁹啵鹷鵴𓄠ꕯ𓉥鹨鱮騠𓅬瑥
𒁎唬鵴𓉡𓄧鸠捴褠陨ꉮ啳𒁦啲𒁹𓁵鴠ꍥ捰
陈𒅰啹𒁴售驍𓁲啹鵃鹲𓉳陭捳

```

This time I got some real readable text back..

```

Do you mean base65536?
Exactly that one.
Thank you! Here is the flag: MilestoneCTF{fitting-more-data-into-a-single-tweet}
Nice one! I hope people will like the challenge. Can I help you with something else?
No, that's it. Thanks for your help.
Happy to. Merry Christmas.

```

I did indeed like the challenge! The answer was **MilestoneCTF{fitting-more-data-into-a-single-tweet}**

## Author (stn)