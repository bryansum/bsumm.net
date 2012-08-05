---
title: WYSIWYG and DVCS
layout: post
---

I just read [Joel Spolsky's take](http://www.joelonsoftware.com/items/2010/03/17.html) on distributed version control. It's interesting that the most important thing about a DVCS like Git is that it would inspire someone to think in terms of changes, not versions. I bring this up in the [context of usability problems](http://news.ycombinator.com/item?id=4340047) people offer about Git. First, there's the intrinsic complexity of the issue -- that is, merging in changes from multiple sources into working code. This is separate from the extrinsic complexity of the CLI, documentation, the bevy of tools, the mental model of the Git runtime, etc., which people tend to find so difficult.

I suspect people are correct in saying there's a large usability gap to be fixed when dealing with merges, and GitHub apps for the desktop are in the service of this. Ideally much of this could be transparent to users. When dealing with programming languages, this doesn't in pratice work very well, as the very syntax of the language, and the strictness of the compiler, makes it very difficult by design to deal with ambiguity of intent. Manual merges as the human-powered mechanism for ensuring this works.

In other media, though, ambiguity in merges isn't a big deal. When merging graphics layers in Photoshop, there's basically no possibility of conflict in global state, so people are able to copy paste in and out of files. (In practice, I think this might not be entirely true, as there's probably some dependencies on global settings like bit depth, etc., that need to be reconciled.)

Tracking document changes in Word is another example of VCS that happens to be quite usable for its problem domain. Seeing the main DVCS princple (changes, not versions!) applied to a WYSIWYG editor, visual or textual, would be interesting. This is basically where Google Wave was heading.

I would like to see an abstracted editor, using Git internally, that makes it totally transparent to the user when commits are occuring. This would be basically equivalent to an undo / redo command history, but with the additional ability for you to bring in others' history in with yours. 

Again, this all entails a medium receptive to ambiguous changes, so systems language programming might be a bad fit for this. I would expect this would be most useful in the context of GUI programming and front-end development. 

