# Cyber Xmas Carols (misc)

## Description

```
We love to sing the Cyber Xmas Carols!

Probably you have already seen this flag, but now it's time to put it in the system. Some of you are trying to submit this flag since the annoucement of the Cyber Xmas! :)

Note: To make this more fun, please put this flag in reverse! i.e. MilestoneCTF{abc1234} -> MilestoneCTF{4321cba}

```

## Task analysis

The description of the task claims that we've already seen the flag and people have been trying to submit it **SINCE THE ANNOUNCEMENT** of the Cyber Xmas :) Truth is, we did have an announcement email that contained some interesting things, but let's see. Here's the content of that announcement email:

```

Annual Milestone CyberXmas is back!

;TLDR register at https://cyberxmas.milestone.dk and get familiar with the rules 😊
NOTE: This year we introduce possibility to score extra points for submitting a write-up of the solutions – just tell us how you got the flag! (see the rules)

Milestone's CyberXmas CTF, a festive quest so grand,
invites you to join and take a stand.
lively challenges await, in this digital land,
even amid EO compliance, fun is at hand.
strategize and solve, with toolchains so bright,
twenty-four puzzles to test your cyber might.
our twist this year, a call to write,
narrate your journey, in the moon's soft light.
extra points for stories, told with flair and insight,
CTF begins with 'MilestoneCTF', a guiding light.
Tales of cyber conquest, into the night,
Flags await, hidden out of sight.

{ enclosed within these curves, the secrets lie,
explore the depths, let your codes fly.
as you decrypt and delve, with a keen eye,
seek the hidden truths, that in the code lie.
your quest in this cyber realm, don't be shy,

- a dash in the puzzle, a clue sly,
follow the paths where the bytes lie.
look beyond the text, to the graphic sky,
a secret hidden, in an image nigh.
grasp the unseen, let your spirits fly,

} End your quest where secrets tie.
In the spirit of the season, 'hohoho!' don't deny,
A whisper of mystery, in the festive sky!



MilestoneCTF… ?{hahaha-nice-try!... :)

```

## Solution

If we look closely at that poem, the second paragraph starts with an opening curly bracket and the last paragraph starts with a closing one. Reading the first letters on the few lines in between yields **{easy-flag}**. Now obviously that would be our flag. 

Now let's remember what the description of the task noted: the flag should be reversed. Thus we get **MilestoneCTF{galf-ysae}** and that would be the solution :)