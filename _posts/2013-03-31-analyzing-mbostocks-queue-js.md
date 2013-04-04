---
title: Analyzing mbostock's <i>queue.js</i>
layout: post
---

Even with the proliferation of open source software on the web, relatively few developers take the time to study the source code of the software they might use everyday. I believe this can largely be traced back to a lack of culture around code reading, which I will hopefully talk about in a later post. To combat this for now, however, the answer is simple: we just need to practice looking at and studying the works of others, similar to the way painters [studied (and copied) the great masters](http://www.paulgraham.com/hp.html). 

Beginning today, I'm beginning a series of "deep dives." First is a look into a tiny JavaScript asynchronous helper library called *queue*, written by Mike Bostock (of [d3.js fame](http://d3js.org/)). Many libraries have been written prevent "callback spaghetti:" highly nested callbacks that need to be executed in some order. Many solutions have been implemented, from [promises](http://promises-aplus.github.com/promises-spec/) to [generators](https://developer.mozilla.org/en-US/docs/JavaScript/Guide/Iterators_and_Generators) to a [combination of both](http://taskjs.org/). Mike takes a simple approach: the API comprises one method, *defer*, which takes in a task (a Node.JS-style function, whose last argument must be a completion callback), along with optional arguments. To get the results back, there's *await* and *awaitAll*. These differ only in how they return their results array. Mike mentions the entire source can be compressed down to around 500 bytes, so this walkthrough shouldn't take too long.

## Example

First, though, I'd recommend looking at the [repo on GitHub](https://github.com/mbostock/queue), or [taking a quick peek at *queue.js*](https://raw.github.com/mbostock/queue/master/queue.js) directly before reading this to get a quick sense of the overall code structure. Here's an example use of the API from the docs, reading two files in parallel and awaiting the combined results:

```javascript
queue()
    .defer(fs.stat, __dirname + "/../Makefile")
    .defer(fs.stat, __dirname + "/../package.json")
    .await(function(error, file1, file2) {
    console.log(file1, file2);
  });
```

## *queue.js*

Let's begin. We start with an [anonymous self-invoking function](http://stackoverflow.com/questions/5815757/what-exactly-is-the-point-of-this-function-construct-why-is-it-needed). <span class=ref>Note that [this parenthesis isn't necessarily required](http://stackoverflow.com/questions/1634268/explain-javascripts-encapsulated-anonymous-function-syntax); the JS parser just needs to treat this as a *function expression* rather than a *function declaration*, so a *+* or *!* would work as well.</span>The surrounding parenthesis helps clarify to the reader that this function will be executed immediately.

```javascript
(function() {
```

Next, we check for Node.js-style modules; if these exist, add *queue* as a module. Otherwise add it to the *self* object. <span class=ref>Assigning to *self* was new to me, as I typically expect modules to assign to the *window* global for browsers; however, it turns out most browsers resolve *self* to *window* in the global context, and to the Web Worker context [in a Web Worker](http://blog.vjeux.com/2011/javascript/javascript-one-line-global-export.html). Thus, assigning to *self* covers both contexts.</span> Note it may also seem that *queue* is undefined at this point; however, because JavaScript [hoists variable names](http://stackoverflow.com/questions/7506844/javascript-function-scoping-and-hoisting) to the top of functions, the function *queue* from below is actually available.

```javascript
  if (typeof module === "undefined") self.queue = queue;
  else module.exports = queue;
  queue.version = "1.0.3";

```

This is a shorthand for *Array.prototype.slice*, and now we enter into our main function definition.

```javascript
  var slice = [].slice;

  function queue(parallelism) {
```

*queue* is the object we return to the client at the end of this constructor. This initially seems confusing, as it shadows the name of the function immediately above, but this allows code below to read more naturally. Most other variables are already explained:

```javascript
    var queue = {},
        tasks = [],
        started = 0, // number of tasks that have been started (and perhaps finished)
        active = 0, // number of tasks currently being executed (started but not finished)
        remaining = 0, // number of tasks not yet finished
        popping, // inside a synchronous task callback?
        error = null,
```

However, *await* and *all* are interesting. In the context of the internals of *queue*, the *await* variable is a placeholder for the final callback function. *all* here is a boolean that determines how the results should be handed back: either as separate arguments, or as an array. [Read the documentation](https://github.com/mbostock/queue) for more information.

```javascript
        await = noop,
        all;
```

Setting default parallelism is important for later on, as this variable is checked when popping tasks off our queue to ensure we're not executing more than *parallelism* tasks at a time.

```javascript
    if (!parallelism) parallelism = Infinity;
```

Next we have *defer*, our first public function. *defer* slurps in its arguments and stores them to our current tasks. *defer*'s API specifies its first argument is our task function, and the rest are optional arguments for that function.

```javascript
    queue.defer = function() {
      if (!error) {
        tasks.push(arguments);
```

Next, we increment our count of remaining tasks, and then execute our *pop()* method, where most of the the magic happens. We'll look at this in a second. <span class=ref>Interesting detail: *defer* tries to executes our tasks immediately, rather than waiting for *await*, for instance. This means that we can call *defer* after we *await* and it should call *await* again for us.</span> So ends our *defer* method.

```javascript
        ++remaining;
        pop();
      }
      return queue;
    };
```

The *await* method and *awaitAll* methods are similar; they simply set the variables we were talking about, and optionally, if there weren't any remaining tasks at this point, immediately notify our callback of our status. These methods also return their parent object, *queue*, which enables a nice [builder pattern-like](http://en.wikipedia.org/wiki/Builder_pattern) API. See the [examples in the documentation](https://github.com/mbostock/queue) for how this is used.

```javascript
    queue.await = function(f) {
      await = f;
      all = false;
      if (!remaining) notify();
      return queue;
    };

    queue.awaitAll = function(f) {
      await = f;
      all = true;
      if (!remaining) notify();
      return queue;
    };
```

*Pop* is the internal method responsible for popping remaining tasks off the queue and executing them, *n* at a time, where *n* is our *parallelism* value. <span class=ref>The *popping* variable here is used to signal to the task's internal callback function whether or not we're still actively popping tasks off our queue. This comes into play in the case when a task completes synchronously / immediately (i.e., not on the next event loop) so that we know not to fire the *pop* call again.</span> Now we get to the main *while* loop for executing our tasks. We continue to spawn new tasks (via *started*) until we run out of them, or we reach the max parallelism of tasks at the given time.

```javascript
    function pop() {
      while (popping = started < tasks.length && active < parallelism) {
```

Now, within the loop, our index is the next started task. We find its arguments, *t*, then extract its arguments from the input using *slice(..., 1)*, which essentially removes the task function call from the arguments list. 

```javascript
        var i = started++,
            t = tasks[i],
            a = slice.call(t, 1);
```

Here, *callback* returns the callback for a given task index, and pushes this as the new last argument; it's a Node.JS convention to have the last argument as the callback. We then increase our active count and execute our task function with a null context and its arguments. Note that *this* as context would be inappropriate here, as the called tasks shouldn't have any knowledge of the *queue* internals. So ends our while loop.

```javascript
        a.push(callback(i));
        ++active;
        t[0].apply(null, a);
      }
    }
```

Next is the *callback* constructor implementation. Why do we need this function? Putting the callback directly in the while loop wouldn't work, [since JavaScript has *function scope*](http://stackoverflow.com/questions/750486/javascript-closure-inside-loops-simple-practical-example), so our task index *i* would contain the last written index value. Now we see that this constructor serves to close over the task index for the callback function. The returned callback takes in an optional error, and then a result value, which is a Node.JS convention.

```javascript
    function callback(i) {
      return function(e, r) {
```

Now we're inside the internal callback function. Decrementing the active count means we can potentially start another task. If we have errors, set our error variable, which will implicitly dequeue all other tasks; we also notify our client immediately that an error has occurred. 

```javascript
        --active;
        if (error != null) return;
        if (e != null) {
          error = e; // ignore new tasks and squelch active callbacks
          started = remaining = NaN; // stop queued tasks from starting
          notify();
```

Otherwise, if our task executed successfully, store the result for this callback in the original task array. Check if there are any remaining tasks; if there are and we're still popping tasks in our *while* loop, do nothing, because the loop is dequeueing tasks already. Otherwise, start popping again. <span class=ref>Immediately returning / synchronous tasks don't normally occur for tasks that involve I/O or ones that call *setTimeout*, because these will typically trigger their callback on the next event loop tick.</span> Finally, if there's no remaining tasks, notify the client that we're done.

```javascript
        } else {
          tasks[i] = r;
          if (--remaining) popping || pop();
          else notify();
        }
      };
    }
```     

Finally *notify*. This function executes the callback function given by the client in *await* or *awaitAll*. If there was a problem, report this as the first argument; otherwise call the callback either with a list of results or results as function arguments, depending on whether the API called was *awaitAll* or *await*, respectively.

```javascript
    function notify() {
      if (error != null) await(error);
      else if (all) await(error, tasks);
      else await.apply(null, [error].concat(tasks));
    }

    return queue;
  }
```   

*noop* is used as a default value for the *await* callback. This is an example of the [null object pattern](http://en.wikipedia.org/wiki/Null_Object_pattern) and simplifies the implementation in *notify*, so we don't have to special case our logic if the *await* callback was null.

```javascript
  function noop() {}
})();

```

## Conclusion
That wasn't so bad, huh? It's nice to see a library with that much utility in that little code. Hopefully there was at least something you learned from reading the source. If you're interested in reading more async libraries for JavaScript, check out the source to [kriskowal's *q*](https://github.com/kriskowal/q), [Tom and Yehuda's *rsvp.js*](https://github.com/tildeio/rsvp.js), or even [Mozilla's experimental *task.js*](https://github.com/mozilla/task.js) as well. 

Please [feel free to comment](mailto:bsummersett@gmail.com) on feedback, style, etc., as I'm looking to create these posts on a somewhat regular cadence.
