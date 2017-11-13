---
title: Dependencies
---

As a sole contributor to a project, you need to be able to rely on your abstractions. Often the key abstractions in an app come from third parties. This may present a knowledge problem, because you likely don’t understand how that abstraction really works, which means when it breaks, you’re on the hook to investigate and hopefully come up with a fix.

I’ve shifted over the years on my feelings towards adopting dependencies. Dependencies can be good or bad. It's difficult to know when one is worth adopting.

One heuristic is whether the dependency aims to be a library or a framework.

The distinction is not perfectly clear, but in general I see frameworks as “one big idea”-minded software that promises great benefits, but only after wholly committing to a worldview. Think Christianity, not Buddhism.

In object-oriented programming, subclassing to get behavior is a very typical form. 

Frameworks will also often abstract the main run loop in your software, adopting a Hollywood-style "I'll call you" form.

Libraries, on the other hand, wait for a caller to interact with them. They often seek to minimize side-effects, and typically only enforce a worldview insofar as it dictates how you call the API.

When diving into Swift development, I sought out opportunities to improve my understanding of what it could mean to organize logic in an iOS application. I read and watched substantial amounts of material on protocol-oriented programming, and / or using `NSOperation` as a fundamental unit of procedure abstraction (à la the *Advanced Operations* talk at WWDC 2015).

Both of these approaches now seem more framework-minded than library-minded.

Rob Napier’s fundamental argument at *try! Swift NYC* was that Swift is not a functional programming language. I agree. Using the type system to define behaviors can lead to substantial verbosity. It can also take your mind out of the problem space and replaces it with pleasing the compiler, which is exactly the kind of effort you avoid when attempting a flow state.

Using `NSOperation` subclasses for all asynchronous operations also impose too many limits on how my application is built. The `NSOperation` state machine is relatively complex, and coming up with a reasonable error-handling model on top of it is not straightforward. Often times there is no one-size-fits-all solution to error handling.

*(Case in point: even `CloudKit`, which based much of their API on `NSOperation` subclasses, had to make their own completion handlers, separate from the built-in `completionBlock`, as `NSOperation` can't guarantee completion blocks are called on a specific thread, and therefore return before dependent operations are started. `NSOperation` was designed in a pre-GCD world, and at present I wouldn't use it as more than a class-based `dispatch_async`.)*

Using framework-minded third party code in Swift also means real pains in ensuring all your dependencies continue working when the next breaking syntax change happens.

When using third party software, **you give up less control with libraries instead of frameworks**. Swift can actually shine in a library context: The semantics of the language can express useful constraints that help define and scope API boundaries.

Being conservative in adopting dependencies seems undervalued.

Flexibility & understandability are two major concerns for any developer, and you should be loathe to give them up without good reason.
