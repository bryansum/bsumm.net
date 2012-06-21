---
layout: default
title: iJam
---

iJam is a mobile application for the iPhone which facilitates networked, collaborative music making via musical "sessions." Users can play either play an instrument by themselves or "share" playing an instrument. Either way, this is then heard by all members of the session. It features multiple virtual instruments playable via multi-touch and gesture-based input mechanisms (accelerometer, etc). iJam is designed to make it effortless to quickly collaborate with others.


To start, a player can either create a new session or join an existing one. Sessions can be configured to be Wifi-based or Bluetooth-based, with both using Apple's Bonjour service to broadcast their presence.

<img style="float: left;" src="/images/ij-main.png" alt="Figure 1: iJam main application screen" />

From here, the user can select from (currently) a few instruments, including `drumelectro`, `rhodeybass`, and `metronome`. These are synthesized using <a href="http://crca.ucsd.edu/~msp/software.html">Pure Data</a> software. iJam uses this extensively for its audio back end, and Pure Data's port to the iPhone is available as the open-sourced <a href="/projects/pdlib">pdlib</a> on this site.</p>

<img src="/images/ij-drums.png" style="float: right;" alt="Figure 2: iJam simplistic 'drumelectro' interface. Drums are movable. Note the timeline above." />

iJam can currently be used in two different "modes": <em>sequenced</em> or <em>free-form</em>.
The two modes can be toggled by selecting the red record button on the
options pane for the instrument. When in <em>sequenced</em> mode, all notes
played are recorded on the timeline and played back on all future iterations
of the loop unless the timeline is cleared.

Currently iJam requires a server with Ross Bencina's
<a href="http://www.audiomulch.com/~rossb/code/oscgroups/">oscgroups</a>
server software to be running on a local machine, but this will be integrated
into the host iPhone in the future (this allows for completely serverless
session creation).
Once connected, clients use UDP multicasting to send <a href="http://opensoundcontrol.org/">OSC</a>
messages directly to other session participants.

I worked on this as part of a senior design class at the University of Michigan.

