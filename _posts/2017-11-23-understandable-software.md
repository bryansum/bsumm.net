---
title: Understandable software, pt. 1
description: "What problem is it solving?"
---

Have you ever completed a LEGO set with lots of instructions? Or orchestrated a particularly complex recipe? Or even put together a piece of IKEA furniture?

Looking at the big picture, it can seem daunting. This is why there are detailed instructions for one to follow. After studying each step, and then working through it, the task becomes doable.

This is the value of well-written instructions. It gives us a framework to break concepts into manageable chunks, form mental models about how something works, and overall helps us to complete a task.

**Understandable software has the same properties.**

When we don't understand how something works, the risk of failure increases.

Ultimately code survives based on its ability to be understood. Understanding code can happen at the implementation level, at an API level (if it is a library), or externally, through tests and documentation.

In reality, good documentation is often scarce, and the author is not available. At this point you must turn to reading the code.

Reading new code might seem initially overwhelming, but can usually be understood with enough time and motivation.

It's important to determine: what problem is the code solving? This guides our understanding of the big picture.

Next are high-level attributes: the programming model, how the code is organized, or how data flows through the system. Does the code follow a set of principles? Is it consistent?

At some point, it should be possible to form a functional model of what's going on. This is where the boxes and arrows are drawn.

Ideally names of abstractions / groups of code related to the problem domain appear, so we begin mapping them to our new mental models.

We can study key data structures and how they're read and written.

We can study a component's implementation to understand its purpose, its responsibilities, or how executes. We can also predict how it affects external code, and how external code affects it.

After you understand both the big picture *and* details, you're then able to build on it or make changes with confidence.

Making it easy for the reader to understand your software ultimately saves time, and money. Often, this reader includes a near-future version of yourself.

We have increasingly many tools to help us make our software more understandable. This includes the language's type system and its data modeling primitives. We can also follow certain programming practices which aid in our comprehension as well.

In a series of upcoming posts, I'd like to explore these topics in more detail.
