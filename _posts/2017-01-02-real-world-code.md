---
title: Real-world code
---
One under-levereged form of learning programming is through reading others’ code, and especially full working applications. Unlike tutorials or examples projects, these include all the imperfections actual working software has. They serve as a fully-formed implementation of an architectural idea (or often lack of one).

Investigating “popular” application architectures on iOS, I came across a sea of acronyms describing, fundamentally, different ways to manage state on iOS. The most promising technique I’ve found is in separating out the view-model dependency explicitly by means of a “view model.” How complex this object is is essentially not defined (some advocate it remains unidirectional — from model-to-view).

Kickstarter recently open-sourced their entire application. This is a great practice (but unfortunately only works when community, not technology, is a company’s competitive advantage.)

The application is interesting for its strong adherence to the MVVM style — in this case using a bidirectional view model, 1:1 with each view controller. The view model was specified as the sum of input and output protocols, both modeled as  `Signal`s in [*ReactiveSwift*](https://github.com/ReactiveCocoa/ReactiveSwift). The end effect is that this view model can be unit tested easily, and its dependencies on external state are clearly specified.

Overall, the project is an artifact of a concious effort to [make iOS UI programming more “functional”](https://www.youtube.com/watch?v=A0VaIKK2ijM) — which I now understand is not just to make data flow more comprehensible, but also to add power to existing constructs; namely, their use of “lenses,” which wrap UIKit appearance methods, allowing style inheritence, among other niceties. 

So, how to read it? Since code is non-linear in its execution, having a code browser / editor is a must. GitHub works well for this too. [Dive in](https://github.com/kickstarter/ios-oss).