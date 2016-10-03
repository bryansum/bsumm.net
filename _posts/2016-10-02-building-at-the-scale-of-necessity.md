---
title: Building at the scale of necessity
---

Brent Simmons spoke about it once — the need to know parts of your application in such a way that you can make decisions on them without concern, or overwhelming concern, that something else may change out from under you.

Dependencies are to be avoided in all cases where they are unnecessary.

That’s a purposeful tautology.

Dependencies can be good or bad. It's difficult to know it becomes either.

Where possible, choose libraries rather than frameworks.

Frameworks box you into a worldview throughout your application. 

Protocol-oriented programming in Swift increasingly feels like we want it as a framework.

"Functional programming" in Swift is still largely one. (Rob Napier spoke at "try! Swift NYC" earlier last month, wherein he spent a lot of time initially working against, and later gave into, a world that really likes closures.)

Using NSOperation as the basis for your entire application is another one. (Not everything in your application is a state machine. Also let us not forget: NSOperation was introduced before GCD.

Make sure the abstraction you use is valuable in the future.

Coming back to a project after six months to a year, especially in the world of Swift, often means some real pains in ensuring all your dependencies have working versions of their software.

Maybe one thought is that Swift maybe works best in a library world. The semantics of the language can express useful constraints where necessary, but these are useful only so far as they help define and scope the API boundaries of the external library.

A framework would work the opposite fashion: enforcing conformance on vended system.

Often, a framework world fundamentally is coming from an object-oriented, subclass-dominated, world. 

