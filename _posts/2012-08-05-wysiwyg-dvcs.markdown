---
title: WYSIWYG and merging
layout: post
---

I just read [Joel Spolsky's take](http://www.joelonsoftware.com/items/2010/03/17.html) on distributed version control. He states the most important thing about Git is that it forces people to think in terms of changes, not versions. As in, bring in Mary's change, bring in his change, as opposed to everyone working towards some base version. GitHub takes this idea and turns it into a service.

However positive this leap may be, it tends to cause [usability issues](http://news.ycombinator.com/item?id=4340047) for those using it. When dealing with merging, there's the essential complexity of merging -- that is, merging in changes from multiple sources into working code, and trying to suss out how to deal with the semantics of two changes to a piece of software. This is separate from the accidental complexity of the CLI, documentation, the bevy of tools, the mental model of the Git runtime, etc., which people tend to find so difficult, especially for single-person use cases.

I suspect GitHub's desktop apps were created to alleviate this usability issue. In simple cases, much of this merging complexity can be transparent to users, and the system could do much of the merging automatically. When dealing with programming / markup languages, this doesn't in pratice work very well, as the strictness of the compiler and the language's syntax tries to *remove*  all ambiguity. Git, being the "stupid" versioning system it is, doesn't get in the way of this process. Manual merges are the human-powered mechanism for ensuring that whatever is written down is exactly is what is intended by the author.

In other media, though, ambiguity in merges isn't a big deal, especially when dealing with non-procedural languages. When merging graphics layers in Photoshop, there's basically no possibility of conflict in global state, so people are able to copy paste pretty readily. (In practice, I think this might not be entirely true, as there's probably some dependencies on global settings like bit depth, gamut, etc.)

Tracking document changes in Word is another example of VCS that happens to be quite usable for its problem domain. Seeing the main DVCS princple (changes, not versions!) applied to a WYSIWYG editor, visual or textual, would be interesting. This is basically where Google Wave was heading.

For certain domains, I would like to see an abstracted editor, using Git internally, that makes it totally opaque to the user when commits are occuring. This would be basically equivalent to an undo / redo command history, but with the additional ability for you to bring in others' history in with yours. 

Again, this all entails a medium receptive to ambiguous changes, so systems language programming would probably be a bad fit for this. I would expect this would be most useful in the context of GUI programming and front-end development, where merges can be as simple as dragging and dropping. The traditional difficulty of forming abstractions with a visual medium (at least compared to textual programming languages) fortunately makes it that much easier to adapt to a new model of merging changes.
