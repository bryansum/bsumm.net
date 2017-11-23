---
title: Value Semantics
description: How to make understandable software, pt. 1
---

Have you ever had that feeling when reading a book, when, after a few pages, you just knew it was going to be great? Or perhaps it was like listening to some great new music; you feel like the writer made it just for you. It captures those details that you don't see in other work; you feel like this person *gets you*.

As crazy as it sounds, I feel like that can happen with software too. 

What makes for an instatAlthough it doesn't happen all the time, these works of art do occur from time to time. Although it's a mistake to call well-designed software art, sometimes great software can have similar qualities.

Ultimately code survives based on its ability to be understood. What attributes does understandable code have? I took a look at Chris Eidhof's forthcoming [Incremental library](https://github.com/chriseidhof/laufpark-stechlin), and found myself infatuated with the design, although I couldn't really explain why at first. There are times when individual ideas come together to form something more than the sum of their parts, and I think that is what we're seeing in this library. Ultimately, though, I think the library is focused on making code more understandable.

<!-- The emphasis on using safer Swift concepts for data modeling, locality / cohesiveness of code, and a strict data flow model, and not re-inventing the wheel where UIKit works well already are all reasons why I think this concept is worth promoting.
 -->
# Value semantics

Since we’re in 2017, object-oriented programming paradigms are no longer the predominant programming “worldview”; shared mutable state is rightly seen as a source of serious bugs in software. One goal of the object-oriented style is to achieve encapsulation, or information hiding. This decreases coupling, and is frequently discussed as a means of “hiding the details of execution.” It does not fully achieve this:

> "I think that large objected-oriented programs struggle with increasing complexity as you build this large object graph of mutable objects. You know, trying to understand and keep in your mind what will happen when you call a method and what will the side effects be." - [Rich Hickey, 2010](http://www.yegor256.com/2016/08/15/what-is-wrong-object-oriented-programming.html)

Most (all?) object-oriented languages have some form of references, and almost all make it trivially easy to share references between objects. **If you share a reference to an object, and two objects may mutate that object, you’ve created the potential for a data race**. [Encapsulation doesn’t prevent us from having to think about this](https://www.youtube.com/watch?v=QM1iUe6IofM). Even when objects are immutable, it is extremely tempting to convert them to mutable versions at some point, and few languages prevent this at the language level.

When we speak of understandability, we are often talking about our ability to reason about the system as a whole. When we introduce shared mutable references, we make it harder for future readers to do that.

Over the last four or five years, languages are increasingly drawing from functional programming concepts — Swift’s through value semantics and its type system.

Swift was designed from the ground up as a safer language than C/C++. Swift structs have value semantics. These are always conceptually pass by value, which means they have a single owner, preventing shared mutable references. This prevents, by design, a whole set of bugs that wouldn’t exist otherwise. **iOS architectures should use Swift structs as the basis for its data model if at all possible.**

(Note that this is not typical in iOS today. ORMs are still popular, and almost all predate the introduction of Swift, which rule out their being designed using structs.)