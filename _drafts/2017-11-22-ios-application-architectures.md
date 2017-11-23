---
title: Making sense of iOS application architectures
---

Ultimately code survives based on its ability to be understood. Well, what attributes does understandable code have? I took a look at Chris Eidhof's forthcoming [Incremental library](https://github.com/chriseidhof/laufpark-stechlin), and found myself infatuated with the design, although I couldn't really explain why at first. There are times when individual ideas come together to form something more than the sum of their parts, and I think that is what we're seeing in this library. Ultimately, though, I think the library is focused on making code more understandable.

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

## Cohesion

When people speak about application architectures, they also emphasize a separation of concerns. What does this achieve? **The goal of separating concerns is to reduce coupling & increase cohesion**. **Coupling is the degree to each program module relies on each of the other modules** (or, roughly, how much another program breaks when another part changes); **cohesion is the degree to which elements of a module belong next to each other.** One example of low cohesion: how many methods in an object touch a single, or a few, properties of that object?

"View models," or objects that vend data to display, are inherently tightly coupled to the view. This is not necessarily problematic, as they also have high coherence. In front-end web dev, display logic (JS) and markup (HTML) are inevitably tightly coupled (how do you find the DOM nodes?) and also highly cohesive (they both show the UI). So them being together in code is not as problematic as it might first seem.

Pete Hunt talks about this in his foundational talk about React, [*Rethinking Best Practices*](https://www.slideshare.net/floydophone/react-preso-v2). React is a Javascript framework born in 2014. This framework attempts to improve cohesiveness through its fundamental unit, the component, which is a self-contained unit of code composed of local state, display logic, and markup. At the same time, these components work as a unit of composition — they can nest inside each other, or be moved around, and thus are relatively loosely coupled in terms of each other. This is also the tack taken by Elm, but in a static functional language paradigm.

React’s decision here is in contrast to traditional MVC models. The React team realized that HTML in JS libraries was often produced using a separate template language, and deliberately underpowered (with only basic constructs) to enforce the HTML (view) / JS (controller) boundary. This in practice achieved a separation of technologies, not concerns. Pete emphasized **that any library or framework can’t know how to separate your domain concerns for you**, but it can only provide powerful, expressive tools for the user to do it correctly.

React makes a strong statement that code with a common purpose belongs together. The underlying thinking here is that keeping related code in the same place helps to describe the possible states and behaviors an object could be in.

Dijkstra makes this point in GOTO considered harmful:

> “Our intellectual powers are rather geared to master static relations and that our powers to visualize processes evolving in time are relatively poorly developed. For that reason we should do (as wise programmers aware of our limitations) our utmost to shorten the conceptual gap between the static program and the dynamic process, to make the correspondence between the program (spread out in text space) and the process (spread out in time) as trivial as possible.

# Data flow

Tightly controlling data flow helps us understand how a program executes, which helps us to understand how a program works, and in turn help understand how side-effects happen in the system, which is crucial to ensure correct and consistent results.

[John Carmack argued this](http://number-none.com/blow/john_carmack_on_inlined_code.html) when asked to explain why sometimes he preferred to inline complex procedural logic with many state changes, vs. separating it all out within arbitrary functions with side-effects. This technique, overall, makes code flow more closely match program / data flow, and this helps keep the possible state of a system in mind to see (He also mentions how programming in a pure functional style achieves this more readily and completely than even this technique).

If we choose to isolate state at the root of our data flow hierarchy, such that all program behaviors flow from state changes, then we also get side benefits: it makes it trivial to do cross-cutting, atomic state changes, and we can add time-traveling features and or undo / redo without re-architecting our code.

In Redux / Elm, strict data flow is achieved by creating a data pipeline from app state all the way to display. This resembles game engines, like the Doom 3 source code, in some respects:

![Doom 3 render pipeline, http://fabiensanglard.net/doom3/]({{ "/images/doom3-renderer.png" | absolute_url }})

# Diffing

In a well-organized procedural style (include pure functional), code flow can data flow closely. This works especially well when views are immediately rendered. However, on iOS, when using UIKit, we work with retained view objects in a hierarchy.

In React / Elm, since components are key enablers of this data flow model, but ultimately affect retained views objects (the DOM), there must be some kind of diffing engine.

React achieves through first creating a virtual DOM, which is generated per "render" pass, and then diffed against the real DOM to determine what needs to change.

Chris Eidhof, the creator of the library I mentioned earlier, [first experimented with a version of the Elm architecture for iOS](https://talk.objc.io/episodes/S01E66-the-elm-architecture-part-1), which uses view descriptions, in concept very similar to the virtual DOM. In this style, view descriptions must be created for each view object type. More problematic is that writing diffing logic to mutate the UIKit view hierarchy from arbitrary states is error-prone, and can become a complexity bottleneck in the application, again preventing the code from being more understandable.

[Chris mentions](https://www.youtube.com/watch?v=dcqWlEaw58M) that a reason why **writing diff logic is difficult is that we tend to throw away information about what operations are happening**. Say, for instance, we have a list of items and decide to remove one. In Elm, we simply re-generate the view description without this list element, and rely on Elm’s engine to figure out that, yes, there was in fact an element removed. This is unnecessary when we could’ve just told it that we want to remove an element, at this index, in the first place.

In order to counter this, Chris built up a primitive: *ArrayWithHistory*, which is more or less a linked list of changes, the reduced result of being an array. The important feature here is that clients use the same API that readers consume (.add, .remove), so we don’t spend time deriving these actions over.

# Observation 
Not discussed yet is how changes are actually propagated to these view objects. In an Elm-style architecture, a benefit to building view descriptions is that there is no explicit "binding" at all to the view hierarchy; instead, the diffing and application logic handle this for us.

If we instead opt for a design that interacts directly with our view objects, we need to create a construct that binds our model to the view properties itself. This is typically achieved through the use of observers. In its simplest form, it is a callback which is bound to certain method of a view, mutating it when triggered. Numerous iOS libraries add combinators to the basic observer, which enable various ways of composing them.

The naive implementation of observer combinators, however, [can lead to serious problems unless addressed](https://talk.objc.io/episodes/S01E76-understanding-reactive-glitches). For instance, after setting up certain kinds of chains of observers, there can be intermediate results seen at leaf nodes. This may sound innocuous, as correct data eventually quiesces, but can be particularly problematic when dealing with certain kinds of side-effects from these observations, like animations or persisting to data stores that expect always consistent data.

These can be avoided in an ad-hoc fashion by artfully applying the correct combinators, but the structural problem remains. **Where reasonable, we should seek out solutions that prevent certain kinds of behaviors from happening in the first place**.

After exploring existing research, Chris found a team at Jane Street building complex UI for an Excel-like application. Chris took a look at the research and realized that by rewriting his observer implementation to maintain heights of observers, and then traversing through his observers using those heights (a topological traversal), he could solve the intermediate value problem.

# Incremental
Chris combined a better observation library, a strict data flow model, array primitives that track state mutations that remove the need for diffing logic, a focus on cohesive display logic to create Incremental.

This library takes good ideas about cohesion and data flow from other systems on the web and iOS today, while also using safer, Swift state constructs, while also not reinventing the wheel by using UIKit APIs directly where possible.