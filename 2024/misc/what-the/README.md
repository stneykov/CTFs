# What the..? (misc) (author: stn)

## Description

```shell
Hey!

We have a fancy new application. Run it and retrieve the flag

++++++++++ [ > + > +++ > +++++++ > ++++++++++ <<<< - ] >>> +++++++ . > +++++ . +++ . ------- . ++++++++++++++ . + . ----- . - . --------- . < ---------- . +++++++++++++++++ . -------------- . > ++++++++++++++++++++++ . ------------------------- . ++++++++++++++++ . ----------------- . ++++++++ . +++++ . << +++++++++++++++ . >> ----------- . ++ . +++++++++ . +++++ . ---- . +++ . ------------- . - . << . ++++ . ++++++ .. - . -- . ++ . -- . -- . ++++ . ---- . >> +++++++++++++++++++++++++ .
```

## Task analysis & solution

The challenge presents a sequence of symbols such as +, -, [ ], ., and >. At first glance, this might seem confusing, but the clue lies in recognizing the pattern. The goal is to identify what this sequence represents and then use the appropriate tools to extract the flag.

When faced with an unusual sequence like this, a good first step is to Google key elements of the sequence (e.g., + > [ ]). This search leads us to Brainfuck, a minimalist esoteric programming language known for its unique syntax and challenging code structure.

#### What is Brainfuck?
Brainfuck operates with an array of memory cells, a pointer, and a limited set of commands like +, -, >, <, [, ], and .. It is notoriously difficult to read, but it can output meaningful text when interpreted.

Now that we’ve identified the language, the next step is to find an interpreter. There are many online tools to decode Brainfuck scripts. One of the most popular tools is dCode, which simplifies the process.

Let's visit the dCode Brainfuck Decoder - https://www.dcode.fr/brainfuck-language and input the sequence. The output is then revealed to us in the form of the flag, which is MilestoneCTF{brain-censored-1776464262}.