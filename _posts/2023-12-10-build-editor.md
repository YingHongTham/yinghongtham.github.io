---
layout: post
title:  "Build Your Own Text Editor"
published: true
---

Several weeks ago, I came across the
[Build Your Own X](https://github.com/codecrafters-io/build-your-own-x)
GitHub repo, which has many step-by-step tutorials on
how to do things from scratch ("from scratch" being a relative term).
I wanted to try something pretty low-level,
and decided to go with
[C: Build Your Own Text Editor](
https://viewsourcecode.org/snaptoken/kilo/index.html).

The tutorial is very well paced and clearly explained,
and it feels very natural to follow.
It builds features incrementally, starting with very basic versions,
then modifying them to handle more complicated versions,
for example, handling user input begins with only single characters,
then later modified to also handle escaped sequences.

It has been quite a while, probably about 8 years,
since I used C in any substantial manner,
and it brought up some nostalgia for my early coding days.
It was refreshing and surprisingly fun to have to once again deal with
low-level bit-manipulation, e.g. bit flags and pointer arithmetic,
after so many years of mostly coding only in high-level languages.

One takeaway I have is: a text editor is just loads an array of strings
into memory and provides an interface to transform those strings.
I mean, obviously that is what it is, and one could say that virtually
for any program, e.g. a browser is just a fancy way of displaying html.
But somehow I felt like a veil had been lifted.
I think the fact that the editor uses the terminal as a sketchpad
contributed to this feeling - I've always felt that the terminal
as this mysterious box, complete and inert, somehow untouchable,
even though I use it everyday for nvim.

Doing this project has inspired me to want to try other Build Your Own X
projects. I would very much like to do one of the Physics Engine
tutorials, as I really like visualizations
(e.g. [Nils Berglund](https://www.youtube.com/@NilsBerglund)'s channel).
