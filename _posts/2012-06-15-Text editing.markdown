---
title: Text editing
layout: post
---

Of the two essential instruments of software development, the mind of the developer and a software interface to translate our thoughts, we realize that only the latter is capable of being reasonably transformed given the technology of today. A typical developer begins and ends their day with the disarmingly simple text editor — the textual interface between the mind and body and the hardware and software machine. Characters are typed, translated by a series of transformations to machine instructions, and computers willingly obey. Unlike typical communication between humans or historically inflexible (read: *non-Turing complete*) industrial machines, with software, we are able to dictate the level of abstraction which we're willing to communicate to our machines, assuming we're capable of creating the necessary software. In essence, the language we choose to speak with a computer is always mutually agreed upon by the lowest common denominator, but unlike interpersonal communication, this denominator is completely determining by our *own* ability as a community to recapture our *own* created language and re-interpret it successfully.

Our computational tools: our text editors, our programming languages, our hardware input devices, are an artifact of our own ability to discover, conceptualize, adapt, and control computational language. The standardization on the keyboard, mouse, and text editor input paradigm of countless software developer professionals today should be examined in this light. Perhaps these all represent the apex of our ability to communicate effectively with machines to serve the end goals most pertinent to our world today. On the other hand, perhaps consider that our current input methods (by this, I mean hardware inputs, development environments, as well as programming languages) are but a local maxima of communication effectiveness, just enough to solve present day problems in enough different contexts so as to not needing to be fundamentally questioned. 

## Forms of interaction

The *form* of an input indicates its capacity to change; if we are to take a systems view of the problem, this is determined by its relation to its inputs and outputs. As mentioned previously, the fundamental forms of communication for man-machine software interaction include: hardware input devices (HIDs), the development environment, and programming languages. Physical / hardware input devices are created in response to human need: ergonomic factors and improving cognition by way of proper affordances, and the type of their output is partially determined by the capabilities of the corresponding machine. Development environments (the "computer" itself and its operating system) comprise both the hardware a machine runs on as well as the underlying software platform layers in which communication occurs; the form of this is largely determined of the input hardware's physical form and means of interaction and the output largely determined by the nature of the software we choose to run on it. Programming languages are in turn determined by their input, the context in which they are run (the host operating system) and their output, the side effects they produce.

The physical input devices of the system are, for better or worse, subject to the  well-established principles of a market economy, and comprehensive and paradigmatic change is largely driven by the whims of capital-instensive market players. In part due to this, the keyboard interface, the QWERTY layout, has changed amazingly little the past 100 years (its internals, however, have -- see the [Selectric](http://en.wikipedia.org/wiki/IBM_Selectric_typewriter) for an enlightening video of one of the first A/D conversions for terminals). Today, in the age of smartphones, we see Apple driving the change to touch-, and increasingly speech-based interfaces. Going back further, we saw Apple drive widespread adoption of the mouse with the introduction of the Apple II, and the bitmap display with the introduction of the Macintosh. We also see Microsoft promoting the Kinect as a novel input mechanism for its entertainment hardware, the XBox 360.

Changes in the hardware and software development environment were largely in accordance with industry demand. In order to keep focus on the effects this had on human-computer interfaces, we'll focus on a few key paradigms. The first, the electric typewriter, or teletype interface, was created in order to replace the then-aging notion of printing punch cards via telegraphy. These didn't contain processors themselves, and instead would typically interface with an external mainframe. Many of these teletypewriters would serve double duty, enabling printed output as well as serving as providing an interface to an external machine. This environment was the birthplace of the B and C languages, [leading to the development of Unix](http://cm.bell-labs.com/who/dmr/chist.html). In fact, the initial Unix file system and its user utilities were developed using GECOS assembly (the base language / operating system for the GE-635, the host for the Multics environment at Bell Labs), and [transliterating to the PDP-7 using paper tape](http://cm.bell-labs.com/cm/cs/who/dmr/hist.html).

As time went on, more complex TTYs added support for [ASCII escape sequences](http://en.wikipedia.org/wiki/Computer_terminal) along with video display terminals replacing paper output, allowing for the repositioning of a cursor and allowing for characters to be edited in a line buffer before being sent to the mainframe. A popular terminal of the day was the [DEC VT100](http://en.wikipedia.org/wiki/VT100), whose implementation defined the predominant mode of terminal emulation, the command-*line* interface, used to this day. This transformation was to the "cooked" mode of terminal input, an advancement from the now-retronymed character-at-a-time "raw" mode. This interface for communication with one's computer has in some circles changed little since more than 30 years ago.

Around the same time, in a research setting in Palo Alto, the graphic user interface was being pioneered, thanks in large part to the development of the bitmapped interface. Xerox PARC's Alto, the prototypical personal computer, pioneered the use of object-oriented software (via Smalltalk), a virtual machine runtime, Ethernet, bitmapped displays, and using the mouse as an input device. However, until the release of Microsoft Windows (and the Apple Macintosh, to a lesser extent), most computer users had not experienced GUIs as an everyday computing experience, much less as a software development environment. 

Along with advances in our hardware, advancements in the nature of software development environments came accordingly, in many cases because of the necessity to make up for [discrepencies between developing and editing programming languages on punch-card paper vs. "on-line" mode](http://en.wikipedia.org/wiki/Text_Editor_and_Corrector). A survey of the state of the art in ["on-line" text editing circa 1971](http://dl.acm.org/citation.cfm?id=356591) illustrates the dominance of QED, TECO, a Selectric teletypewriter, and interestingly, a tablet-based editor by folks at CMU. Notably absent is `ed`, a Ken Thompson creation went on to be the dominant line editor for Unix, and was notable for being an interactive editor.  Thompson's `ed`, in turn, was a reworking of the original [QED](http://cm.bell-labs.com/cm/cs/who/dmr/qedman.pdf) which introduced interactive text editing concepts like text buffers, rather than working directly on a file, and regular expressions as a means of pattern matching input and output text to transform. These pieces of software could and often were run in non-interactive mode, due in part to the fact that often times programming was persisted using paper tape. Over time, individual commands for ed became more specialized: grep for the matching portion, sed for substituting text, and the like, more in accordance with the Unix principles of being a collection of small composable tools.

TECO was an MIT project started in 1962, was more arcane in that it had a smaller set of non-interactive instructions, but was Turing-complete and was used successfully to develop higher-level macros on top of its base system, extending its lifetime far past what would be expected of decades-old software in a university setting. 1964, TECO was altered to contain a "real-time" mode which provided an interactive display. After more than 10 years, in 1976, TECO had been sufficienty extended so as to produce the [first versions of Emacs](http://www.jwz.org/doc/emacs-timeline.html), which was  called as such due to its reputation for being a set of "Editor MACros" for the TECO editor. 

Innumerable forces then shaped the design of our programming languages, and advances in the lower layers of our dependency hierarchy undoubtedly played a role in their success. Fortran was developed during the age of punch cards, and arguably one of the first examples of a higher-level programming language. One might assume its structed form led to easier comprehension by less-experienced programmers, and this was the main factor in its success, but this may be taking a simplified view: one of the main benefits of higher-level abstractions initially was its ability to decrease the number of punch cards required to implement an equivalent program [by a factor of 20](http://en.wikipedia.org/wiki/Fortran) in many cases. 

## Optimizing

Consider perhaps, like a simulated annealing problem near the end of its iterations, the dominant paradigm of command-line driven development we're accustomed to is successful due in large part to its network effects despite [attempts to improve the design by many of its original authors](http://catb.org/~esr/writings/taoup/html/plan9.html). As software becomes more widely known, we are disincentived to question the foundation on which we've based so much of our collective wisdom as a community. In many cases this is completely justified in order to mitigate short-term risk.

User error is the name given to the communication breakdown between the creator and the user of computational languages. Learning, in this way, is the process by which we adapt an existing set of thoughts to our own experience, adapting what was once a breakdown onto a new conceptual understanding of our world (Heidegger). This practice involves the difficult process of second-order thinking (Kahneman), and presumably because of this difficulty, we see a severe limit in the ability for those already adept in a field to recognize or attempt a 

The adoption of software development tools are caused by a number of agents of change, with many only partly intrinsic to the tool itself. Causes include: the ease by which software can be adapted or transliterated from existing sources (see "[Worse is better](http://www.dreamsongs.com/WorseIsBetter.html)"); the evolutionary nature of programming languages, themselves guiding our ability to write even better abstractions than those before us; the changing platforms and input devices upon which software is used; and, most importantly, the culture and people using the tools. These all represent a *context* in which software is used, and defines and constrains the success of any tool to be created.

An important consideration when judging any utility is to try to not historicize its capabilities in comparison to our modern day expectations. Modal (separate print / edit) text editing (see `ed`), were developed in shared resource and research environments where teletypewriters were the norm (e.g., Bell Labs in the early 60's). Quite literally, this was a typewriter hooked up to a machine, where one could output text from a remote machine. (Picture) In order to iterate on a programming problem, one would study his output on said printed paper and manually prepare his changes for the next session. The read, eval, print loop in this case was measured on the order of *days*, so the ability to edit documents visually on screen in those days already had presupposed a number of freedoms found nowhere in the vicinity of `ed`'s development history. We shouldn't seek to judge the efficacy tools of yesterday against the problems of today, unless with a thought to replace them with something more appropriate.

Tools for software development have changed rapidly in comparison to other technological advances in other fields. (find references to book-making and publishing... find references to electronic instruments in popular music). However, this pace of change in relation to the rest of the advances in software has often lagged compared to advancements in computing power. Why is this? I suspect it may have something to do with the fact that user interfaces are subject to a different set of constraints than computing in general -- human factors.

In systems software, innovations in API *implementation* (as opposed to interface) are ideally invisible to the end user. This encapsulation is all in the service of preventing the end user from having to re-adapt his cognitive model of the workings of a system (see Donald Norman, design of everyday things), and maintaing this separation remains one of the cornerstones of reusable software. On the other hand, user interface design, and software editors specifically, are primarily concerned with creating a subjective "best" experience for a given context (references?). In these cases, conscious or unconscious changes in interfaces can and are made regularly with the hope of optimizing for an ideal user experience across a variety of users. The struggle between design familiar and at-hand versus novel, and hopefully more optimal, solutions is a perennial problem. The crux of the issue is novel interfaces necessitate cognitive load on the end user (Donald norman, NUI), and when existing metaphors are not to be found, either the user learns how to use his new environment or experiences what in many cases can only be described as profound digust (uncanny valley effect). We can see the pain this causes any time our favorite web site or application changes its layout (just saw this with Reeder); users complain publicly about the interface being confusing or worse yet, *slightly different*, even though after some time people may find they adapt to these changes. 

This knowledge gap — the time delay in individuals learning a new tool — is the main barrier to be overcome in creating new types of tools. Software tools are no exception, as although they are software, *their* interface is one between a user and the machine, not one between other software processes. (see diSessa, Understanding computers and cognition)

This trend of delayed interface development is seen consistently with the advent of many other technologies; the printing press changed the nature of publishing, but did little to alter the dominant means of writing manuscripts; authors were still using pen and paper exclusively to formulate thoughts into words up until the advent of the typewriter, hundreds of years in the future. Automobiles have gone through thousands of implementation changes, increasing horsepower, safety features, and the like, but have changed relatively little regarding their basic interface — a wheel, pedals, and possibly a clutch and gear shifter. Changes are typically simplifications of the existing interface interface rather than introducing new paradigms -- automatic transmission removes the need to shift manually (and in the next 20 years, self-driving cars will remove the need to steer!). As an illustrative example, a driver today transported back to the 1920's could still likely drive a vehicle with little issue. The trend then seems to be that changes in the means of publishing are not necessarily aligned with a change in the means of creation, and in turn basing assumptions of creational capacity being aligned with productive capacity leads us to estimate our output in unrealistic ways (see the Mythical man-month for scaling output linearly wrt engineer count. The traditional answer for this logarithmic curve are traditionally explained away by instrinsic communication overhead, which I would argue is true, but this is given without thought that this overhead is in turn partially dictated by the lack of communication built into the tools that one already uses). 

If this discrepency in computation
Mouse and keyboard interfaces, the dominant user interface paradigms of software tools today, have existed as the dominant forms for, have only become With children raised on iPads starting

It's interesting to note that evolutions tools that people use can often extend the lifetime of an existing interface, turning what would seem like an intractable form into something easily digestable. An admittedly biased example here would be in developing software using Java; many expert developers today have come to rely on integrated IDEs like Eclipse to aid the developer in understanding how a piece of software works together. How successful would the ASCII notation for programming languages has existed since the 50's with the introduction of Backus-Normal-Form, and has changed relatively little since the 

 to the myriad contexts in which software is
created. From large corporations seeking to homogenize tooling to benefit from
scaling effects, to individuals looking to hack together scripts to get a remote
server working, to scientific workers transliterating an algorithm for their
next paper, software developers require tools to do their job efficiently. 

This efficiency can be quantified in a number of ways; an intuitive measure is
one of essential and incidental complexity, wherein a given tool would seek to
minimize the amount of incidental complexity inherent in using the tool. 
to  depending on the
problem to be solved and the domain in which it exists.

Programmers depend on text editors. Often times, the only thing that stands in
the way from pure thought stuff (Mythical Man month) and implementation is the
deceptively simple practice of inputting a sequence of characters into a text
editor, line after line.

As much as the output of our programs has increased, the mode in which we create
them has been (perhaps) suprisingly conservative. There's probably an argument
to be made about old dogs and new tricks here somewhere.

It's interesting to note that the text editors many use today have roots going
back almost 50 years. The words `tty`, teletypewriter come from a long history
of electronic typewriters. The reason we have support for both a line feed and a
carriage return in our ASCII table from from this lineage.

The state of the art in the 70's, at Bell Labs, where Ken Thompson 
was translating BCPL code to B to C on teletype machines.
http://cm.bell-labs.com/who/dmr/chist.html
This line-oriented programming style is still largely the norm today in many
circles; leading hackers to this day often rely on command-line editors and a
combination of shell scripts for their environment as their state-of-the-art
tool. I believe that these tools being so widely portable, consistent, and
well-tested naturally leads these to dominate the space, and leads one down
a much less stress-laden path. However, programming is primarily a social and
trade-based activity, meaning that learning from others the tools to use is
still a primary motivator (citations?), which would also imply some
conservatism. See the use of painting techniques, film, etc., for more
background on trends in means of production.

The absolutely essential quality of programming in a text-based language in this
day and age would imply there being movements to improve the state of the
art. An interesting question to ask in this sense is in what measurable ways one can
improve their editor. One easy way to determine the essentials of a text editor
is to first use an extremeley basic one: coding in Notepad.

The first thing we should understand is the coupling between text editors and
the programming environment they were created for; Alan Kay understood this
quite well when he combined the editor and the runtime environment of Smalltalk
into one and the same piece of software. This trend continues today in the form of 
Squeak and Scratch, two IDEs utilizing the "image" concept.



Vim was a successor to `vi`, a successor to `ed`. Reading the `vi` wiki page is very enlightening
, and serves to show that vi was actually an amalgam of an improvement to the
original `ed` called `em`(http://www.eecs.qmul.ac.uk/~gc/history/index.html),
adding line-at-a-time raw mode editing, allowing people to see the effect of
editing in real time, some Rob Pike's `ed` editor experiments, and `bravo`. 
Vim being modal is still a defining characteristic to this day, as many designers argue for the removal of such a system from most user interface decisions (No modes guy).

Text editor and corrector (TECO) was a precursor to Emacs, and also an editor
with quite a lifespan of its own http://en.wikipedia.org/wiki/Text_Editor_and_Corrector.
The fact that searching operations for characters was motivated by the fact that
there were no line numbers for the code itself internally. The idea of making a
correction tape was made in order to more efficiently utilize the
school's PDP-1 computer for time-sharing purposes, so that someone wouldn't have
to sit at the computer continually to work. When running TECO, one could simply
write out the appropriate commands to the machine at the appropriate time in
high speed. Later on, debugging this became an issue and online editing became
the norm, although much like `ed` there had to be an explicit command to print
lines out. By 1964, there was a screen display for the TECO system to support an
editing buffer for the next command. Note that, in this case, although the means
of visualizing code had changed, the input method lagged.
Videos here: http://c2.com/cgi/wiki?TecoEditor

Emacs came from land of Lisp: MIT, with Guy Steele and Richard Greenblatt
providing the immediate predecessor. http://www.jwz.org/doc/emacs-timeline.html
Emacs came along shortly afterwards, with Richard Stallman being the primary
author. Emacs was first developed in the AI lab at MIT around 1977
(http://www.multicians.org/mepap.html#secii). Originally, Emacs was composed of two separate 
screens, where one would be typed editor commands and the other the region of
the text being edited. This would largely be considered in the realm of
quasi-mode operation; only with the second version was there "real-time"
editing, where, for instance, deleting a character (`Ctl-D`) would actually lead
to the removal of the character as it happened. This, arguably, could have only
resulted from a world where display terminals were being the norm, as Nicholas
Negroponte writes: http://dspace.mit.edu/bitstream/handle/1721.1/16038/07332831.pdf?sequence=1

A fork by Dan Weinreb was made soon after for the Lisp Machine exclusively,
  making EINE the first Lisp-based Emacs implementation.
  http://www.heeltoe.com/retro/mit/mit_cadr_lmss.html

Eclipse was created by IBM after, perhaps unsuprisingly, patterned after a
commercialized version of a Smalltalk development environment called VisualAge.

Over time, different people have spent large quantities of time in trying
to provide language-specific or environment-specific features to editors in
order to aid the creational process, assuming that with a more context-aware
programming environment one can increase the productivity of one's work. This is
also the premise behind a number of increasing movement for a more dynamic text
editing environment ala LiveTable / Bret Victor's code editor. 

It's worth exploring reasons why people would feel opposed to increasing the
complexity of their tools. A number of well known and well-respected programming authors
refuse to use ID

Intentional programming

