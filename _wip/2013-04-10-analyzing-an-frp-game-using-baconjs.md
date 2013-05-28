---
title: Analyzing an FRP game using bacon.js
layout: post
---

[Wizard of Wor](http://strategywiki.org/wiki/Wizard_of_Wor), released in 1980, was a notable arcade game due to its use of speech synthesis for the time. Suffice it to say, I wasn't even born when it was around, so I couldn't have enjoyed it in its original glory. A [Commodore 64 version](https://www.youtube.com/watch?v=LYJ5_bMLwpU) was released a few years later, and from the look of YouTube's comment thread, a good number of people had fond memories playing this at home. Rather than remain passive spectators of this classic, why no recreate it in the assembly language of the web?

In the second part of my series of [analyzing source code](2013/03/31/analyzing-mbostocks-queue-js.html), I present a clone of *Wizard of Wor* which uses [Juha Paananen's](https://github.com/raimohanska) [bacon.js](https://github.com/raimohanska/bacon.js) library. This discussion is essentially an adapted form of [slides](http://raimohanska.github.io/bacon.js-slides/index.html) he presented sometime in the previous year. 

Over the course of these analyses, I want to experiment with different methods of narrating code. The previous code analysis was a line-by-line walkthrough through a piece of code. The format was in a vaguely [literate style](http://ashkenas.com/literate-coffeescript/); this time, inspired by some [*Learnable Programming*](http://worrydream.com/LearnableProgramming/) principles, I'm focused mainly on *showing data*, *showing flow* through parts of the code base, which works well visualized.

The *bacon.js* library presented today is an implementation (or reimplementation) of a functional reactive programming library. Juha started development out of frustrations he had with the *Rxjs* library (an open-source contribution by Microsoft), in turn which was based on their own Reactive Extension (Rx) library for .NET. There are many [longer definitions](http://stackoverflow.com/questions/1028250/what-is-functional-reactive-programming/1030631#1030631) of what functional reactive programming *is*, but lets take a duck typing approach and note that it facilitates *composition* and *transformation* of events, much like one would compose sequences of animations or audio events in *Quartz Composer* or *Max/MSP* / *Pure Data*, respectively (these are sometimes described as *dataflow* languages).





 although detailed, I feel both the *formatting* and the *format* of the document could change: the *formatting*, with the detailed  this moThis time, inspired by some of the concepts put forth in   Rather than explaining in detail what [bacon.js](https://github.com/raimohanska/bacon.js)

? I can't say I have, but Today I'm focusing on a use of the relatively new *bacon.js* library, which I'm 