---
title: Understandable software, pt. 1
description: "What problem is it solving?"
---

Have you ever put together a LEGO set? Especially one with a lot of instructions? Or nailed a complicated recipe? Or put together complex IKEA furniture?

Looking broadly at the task, it can seem daunting. This is why there are detailed instructions for one to follow. After observing each discrete step, and then working through it, the task becomes doable. At some point you realize you've completed it!

This is the value of well-written instructions. It gives us a framework to break our thinking down into manageable chunks, form mental models about how something works, and overall helps us to complete a task.

**Understandable software has the same properties.**

When we don't understand how something works, the risk of failure increases.

Ultimately code survives based on its ability to be understood. This happens internally, at an API level (if it is a library), and through documentation.

In reality, good documentation is often scarce, and the author is not available to talk to.

When this happens, you must turn to reading code.

Like that complex recipe, reading new code might seem overwhelming at first glance, but can usually be broken down in a way that makes it understandable, even if the code does not do us any favors.

First: what problem is the code solving? A clearly defined problem is critical, because it is the only way we can hope to understand the solution the code provides.

Next to look at are high-level concepts: the programming model, how the code is organized, how data flows through the system, and important data types; does the code follow a set of principles?

At some point, it should be possible to form a mental model of what's going on at a functional level. 

Ideally abstractions or groupings are related to the problem domain, so we begin to map out the space of the software in our heads.

As needed, we should be able to jump to implementation, and understand at least, its purpose; how it is implemented; its affect on code external to it; and how it is affected by any code external to it.

Overall, our ability to understand code is proportional to its focus, and purpose. The reader can effectively reason about individuals components and their interactions because we made it understandable.

What we typically call software architecture is nothing more than an adherence to a given set of principles applied to a problem.

When someone understands the architecture, it makes it easier to build on it and make changes. It saves time, and money.

We have increasingly good tools available to help us to make our software more understandable. I would like take time in a series of upcoming posts to explore just what that means.
