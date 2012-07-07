---
layout: post
title: Learning Perl
---

As there's a decent amount of legacy code at work, I'm starting to learn Perl. The language has had a longer history than some of the popular scripting languages today, so determining best practices involves good ol' investigative work. A good starting point, I've been repeatedly told, is the mammoth (e.g. [Programming Perl](http://www.amazon.com/Programming-Perl-3rd-Larry-Wall/dp/0596000278)), which represents the learn-everything-at-once, disassociated approach, and favors those who would love nothing more than to curl up next to an O'Reilly book for an entire weekend. [Modern Perl](http://www.onyxneon.com/books/modern_perl/index.html),  a more recent beginner's book, which takes an opinionated view in promoting more recent changes to the language, starting with Perl 5.12.

Here are a few observations I've noticed:

* Ruby borrows Perl's syntax more than I realized (e.g., postfix conditionals)
* The breadth of libraries is the most comprehensive of any language I've used. People really seem to be good about writing good documentation for their code, which is helpful. The CPAN model has since seen rapid adoption by other scripting languages with much success; see Ruby's `gem` and node.js' `npm`.
* [cpanminus](http://cpanmin.us/) is a simpler CPAN; it works exactly how I'd like a package management system to work, without the bevy of features you need to configure when using the `perl -MCPAN -eshell` CLI.
* [Moose](http://moose.perl.org/) is an impressive object system, and includes many more meta-class features than one would typically come to expect in an OO language. Traits are another welcomed feature. Unfortuatnely, adding Moose as a dependency also tends to limits its usage in legacy contexts.
* Pervasive context-awareness is something that took me some time to understand, but now it seems natural. In addition, dealing with references adds another layer of complexity that can be intimidating to new users having to deal with both at the same time. This is unlike most other scripting languages, which err on the side of simplicity.
* Topic variables (`@_` and `$_`) seem like a bad idea for readability, but really cut down on the tedium of dealing with variable names in cases where it's obvious. It's great for short programs, probably not so much for modules.
* Perl's argument passing reminds me of Javascript, in that you do most of your work explicitly pushing or popping arguments off of your `@_` / `arguments` stack.
* Perl's efforts to be backwards-compatible has left it with a lot of vestigial syntax; in a way it suffers from the same problems C++ does in trying to please everyone. The solution for a lot of teams using C++ is to use a well-defined subset of the features, and I believe this parallels making sure to use the latest features of Perl as well.


