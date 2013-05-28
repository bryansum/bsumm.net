# Bacon.js source
[back-bacon](https://github.com/latentflip/back-bacon) puts Bacon in Backbone views. [This is a Bacon.js demo of a spreadsheet](https://github.com/latentflip/bacon_js_spreadsheet). This is a normal [Bacon demo](https://github.com/latentflip/bacon_js_spreadsheet). Here's a talk on [Bacon.js](http://latentflip.com/bacon-talk-realtimeconfeu/) for the RealTimeConf EU. 

## High-level

adds *asEventStream* to query library. Creates a new EventStream object.

Creating a custom EventStream will take in a function returning an unsubscribe function. The subscriber can return *Bacon.more* or *.noMore*. 

### Events

*.Next* takes in a literal or a function (lazy eval). It's used in the event stream to signal that the event is continuing.

*.End* signals that the event stream is done; likewise for *.Error*. *.Initial* is sent only after a subscription to a Property, and is the initial or current value of that property at the time of subscription. 

*value* (hasValue) is set on events that have them (Next, Initial); *is{Type}* predicates are available for all events for types.

*Error events* are always passed through. *onError* callbacks will eat them, as well as *errors*, which is a stream of only Error events. *endOnError* returns a new stream that terminates on error; normally this doesn't happen. This is different than RxJs, [and arguably better](https://github.com/raimohanska/bacon.js/blob/master/README.md). Error events only occur from external IO, not from Bacon itself. 

To stop listening to upstream, *Bacon.noMore* can be called, or call the *dispose* function returned by subscribe. This typically doesn't need to happen in client code.

Defines our module in a number of ways; the first is for Node and other [CommonJS-style requires, the second for AMD](http://addyosmani.com/writing-modular-js/). 

```coffeescript
if module?
  module.exports = Bacon = {} # for Bacon = require 'baconjs'
  Bacon.Bacon = Bacon # for {Bacon} = require 'baconjs'
else
  if typeof require is 'function' and require.amd?
      define 'bacon', [], -> Bacon
  @Bacon = Bacon = {} # otherwise for execution context
```

