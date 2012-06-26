---
layout: post
title: Learning Perl
---

After what was probably too long trying to avoid it, I'm finally diving head-first into Perl. I feel learning Perl takes more investigative work than some languages, as what's best practice is somewhat subjective and has changed since a few of the more popular books have been published (e.g. [Programming Perl](http://www.amazon.com/Programming-Perl-3rd-Larry-Wall/dp/0596000278)). I've made a few observations:

* Ruby borrows Perl's syntax more than I realized (e.g., postfix conditionals)
* The breadth of libraries available is amazing -- by far the best of any language I've used -- and people really seem to be good about writing good docs for their code.
* [cpanminus](http://cpanmin.us/) is by far the best way to use CPAN; it works exactly how I would expect a package management system would in 2010, unlike the bevy of features you need to configure when using the `perl -MCPAN -eshell` CLI.
* [Moose](http://moose.perl.org/) is an impressive object system, much more feature-rich and competitive with today's OO languages than Perl's default.
* Pervasive context-awareness is something that took me awhile to understand, but now it seems natural to me. In addition, dealing with references adds another layer of complexity that can be intimidating to new users having to deal with both at the same time.
* Perl's argument passing reminds me of Javascript; or should that be the other way around?
* Topic variables (`@_` and `$_`) seem like a bad idea for readability, but really cut down on the tedium of dealing with variable names in cases where it's obvious. It's great for short programs, probably not so much for modules.
* Perl's efforts to be backwards-compatible has left it with a lot of vestigial syntax; in a way it suffers from the same problems C++ does in trying to please everyone. The solution for a lot of teams using C++ is to use a well-defined subset of the features, and I believe this parallels making sure to use the latest features of Perl as well.

