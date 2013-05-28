# (Worzone) Wizard of Wor
The source for the game is [available on GitHub](https://github.com/raimohanska/worzone/blob/master/worzone.js). (permalink?) Again, I recommend viewing only the source to get a high level view of what's going on, skimming and getting a sense of the overall structure of the program.

My hope in reviewing this code is that it becomes clearer how event streams can simplify event handling. The primary benefit in my eyes is that it more cleanly separates control flow from data flow in asynchronous programs in a very similar fashion to pipes in UNIX. ...

First, we start with an anonymous invoked function. <span class=ref>jQuery will call this for us so we don't have to use the `(function())()` syntax.</span>*Rectangle* is defined below, it creates an object with x,y,w,h parameters. This library uses [Raphael.js](...) for drawing text, circles, and images on the canvas.

```javascript
$(function() {
  var bounds = Rectangle(0, 0, 500, 450)
  var r = Raphael(20, 20, bounds.width, bounds.height);
```

*messageQueue* is the bus for all events; all of our game objects (players, monsters, sounds, targets, and score display) use this to either consume or emit events. All of the events in Worzone follow a simple convention: they are objects, and each of them has a type signifier *message*. *ofType* is used here to filter messages whose *message* property of the type specified. 

```javascript
  var messageQueue = new Bacon.Bus()
  messageQueue.ofType = function(type) {
    return messageQueue.filter(function(message) {
      return message.message == type
    })
  }
```

Targets will be explored in detail later on, but for now just know that this class maintains a list of targets that can be modified by remove / create events from the message queue. It also has a few public methods for determining whether any targets are in range. This is essentially a constructor, but without the *new* keyword. Why is this? [Juha prefers *new*-less constructors](http://nullzzz.blogspot.com/2012/11/daddy-what-does-new-keyword-do.html) because they can give odd behavior at runtime if the client doesn't instantiate them properly.

Monsters is a constructor that lazily creates monsters based on level events. You could say this like an event-driven factory. Players functions similarly, it creates two Player objects, which also contain all of their relevant UI constructors. More on this later. 

```javascript
  var targets = Targets(messageQueue)

  Monsters(messageQueue, targets, r)
  Players(messageQueue, targets, r)
```

Audio wraps functions for loading and playing sound effects via the *audio* tag, lazily loading them into the DOM. GameSounds captures all relevant events from the event stream and associates audio files with types. The next line toggles sound based on the user's preference. Finally, we set up Levels, which we'll talk about in a second. So ends our main loop. Everything else is now event driven!

```javascript
  var audio = Audio()
  GameSounds(messageQueue, audio)

  $('#sound').click(function() { audio.toggle() })

  Levels(messageQueue, targets, r)
  
  $('#sound').click(function() { audio.toggle() })

  Levels(messageQueue, targets, r)
})
```

Levels sets up event handlers and emitters for the game start and end.
 
```javascript
function Levels(messageQueue, targets, r) {
```
gameOver intercepts gameover events. TODO test this. This skips over the first event because otherwise, we would emit the initial event, which would cause our splash screens below to appear. StartGame creates a Keyboard event handler and waits for any one key. The start screen instantiates a 
```javascript
  var gameOver = messageQueue.ofType("gameover").skip(1)
  var levelFinished = messageQueue.ofType("level-finished")
  var startGame = Keyboard().anyKey.take(1)
  var startScreen = AsciiGraphic(startScreenData(), 7, 7, Point(50, 150)).render(r).attr({ fill : "#F00"})
  startGame.onValue(removeElements(startScreen))

  var levelStarting = levelFinished
    .merge(startGame)
    .scan(0, function(prev, _) { return prev + 1 })
    .map(function(level) { return { message : "level-starting", level : level} })
    .changes()
  var levels = levelStarting
    .delay(4000)
    .map(function(level) {
      var levelEnd = levelFinished.merge(gameOver)
      return { message : "level-started", level : level.level, maze : Maze(level.level), levelEnd : levelEnd }
    })
  levelStarting.onValue(function() {
    var getReady = AsciiGraphic(getReadyData(), 7, 7, Point(30, 80)).render(r)
    setTimeout(function() {
      var go = AsciiGraphic(goData(), 7, 7, Point(200, 170)).render(r)
      setTimeout(removeElements([getReady, go]), 2000)
    }, 2000)
  })
