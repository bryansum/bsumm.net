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

Let's begin. We start with an [anonymous self-invoking function](http://stackoverflow.com/questions/5815757/what-exactly-is-the-point-of-this-function-construct-why-is-it-needed). The surrounding parenthesis is optional, but helps clarify intent to the reader that this function will be executed immediately.

```javascript
(function() {
```

Next, we check for Node.js-style modules; if these exist, add *queue* as a module. Otherwise add it to the *self* object. <span class=ref>Assigning to *self* was new to me, as I typically expect modules to assign to the *window* global for browsers; however, it turns out most browsers resolve *self* to *window* in the global context, and to the Web Worker context [in a Web Worker](http://blog.vjeux.com/2011/javascript/javascript-one-line-global-export.html). Thus, assigning to *self* covers both contexts.</span> Note it may also seem that *queue* is undefined at this point; however, because JavaScript [hoists variable names](http://stackoverflow.com/questions/7506844/javascript-function-scoping-and-hoisting) to the top of functions, the function *queue* from below is actually available.

```javascript
  if (typeof module === "undefined") self.queue = queue;
  else module.exports = queue;

  queue.version = "1.0.2";
```

This is a shorthand for *Array.prototype.slice*, and now we enter into our main function definition.

```javascript
  var slice = [].slice;

  function queue(parallelism) {
```

*queue* is the object we return to the client at the end of this constructor. This initially seems confusing, as it shadows the name of the function immediately above, but this allows code below to read more naturally. Most other variables are already explained:

```javascript
    var queue = {},
        active = 0, // number of in-flight deferrals
        remaining = 0, // number of deferrals remaining
        head, tail, // singly-linked list of deferrals
        error = null,
        results = [],
```

However, *await* and *awaitAll* are interesting. In the context of the internals of *queue*, the *await* variable is a placeholder for the final callback function. *awaitAll* here is a boolean that determines how the results should be handed back: either as separate arguments, or as an array. [Read the documentation](https://github.com/mbostock/queue) for more information.

```javascript
        await = noop,
        awaitAll;
```

Setting default parallelism is important for later on, as this variable is checked when popping tasks off our queue to ensure we're not executing more than *parallelism* tasks at a time.

```javascript
    if (!parallelism) parallelism = Infinity;
```

Next we have *defer*, our first public function. *defer* slurps in its arguments and stores them in a node variable. *defer*'s API specifies its first argument is our task function, and the rest are optional arguments for that function.

```javascript
    queue.defer = function() {
      if (!error) {
        var node = arguments;
```

When deferring a task, *queue* adds a new undefined result to the results array. This inserts a placeholder object in the space for the result, and since *Array.push* returns the length of the array, *node.i* gets set to the index of that result in the results array. Next, there's some logic for adding to the linked list of queue tasks, and incrementing the remaining tasks count for later. Finally, we execute our *pop()* method, where most of the the magic happens. We'll look at this in a second. <span class=ref>Interesting detail: *defer* tries to executes our tasks immediately, rather than waiting for *await*, for instance. This means that we can call *defer* after we *await* and it should call *await* again for us.</span> So ends our *defer* method.

```javascript
        node.i = results.push(undefined) - 1;
        if (tail) tail._ = node, tail = tail._;
        else head = tail = node;
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
      awaitAll = false;
      if (!remaining) notify();
      return queue;
    };

    queue.awaitAll = function(f) {
      await = f;
      awaitAll = true;
      if (!remaining) notify();
      return queue;
    };
```

*Pop* is the internal method responsible for popping remaining tasks off the queue and executing them, *n* at a time, where *n* is our *parallelism* value. <span class=ref>The *popping* variable here is used to signal to the task's internal callback function, which we'll see below, whether or not we're still popping tasks off our queue via our *while* loop. This comes into play in the case when a task completes immediately (i.e., not on the next event loop) so that we know not to fire the *pop* call again.</span>

```javascript
    function pop() {
      var popping;
```

Now we get to the main *while* loop for executing our tasks. We continue to spawn new tasks until we run out of them, or we reach the max parallelism  of tasks for a given unit of time.

```javascript
      while (popping = head && active < parallelism) {
```

Now, within the loop, and for a given node, we assign our head node to it, extract the function to be executed, and then extract its arguments from the input using *slice(..., 1)*, which essentially removes the task function call from the arguments list. Then, keep track of our index value for the results array. Remember that from before?

```javascript
        var node = head,
            f = node[0],
            a = slice.call(node, 1),
            i = node.i;
```

If this is the last element, make sure we clear the list. Otherwise, pop the next oldest item as the new head. Increment our active tasks count.

```javascript
        if (head === tail) head = tail = null;
        else head = head._;
        ++active;
```

Now we create an internal function callback for the task we're about to execute. Due to *queue*'s design, we assume that the last argument to these called functions should always be a callback that takes in first, an optional error, and then a result value, which is a Node.JS convention.

```javascript
        a.push(function(e, r) {
          --active;
```

Now we're inside of this internal callback function. If we have errors, clear all of our state values and thus exit our *while* loop early. This also has the other side effects mentioned. We also should immediately notify the caller that we can an issue.

```javascript
          if (error != null) return;
          if (e != null) {
            // clearing remaining cancels subsequent callbacks
            // clearing head stops queued tasks from being executed
            // setting error ignores subsequent calls to defer
            error = e;
            remaining = results = head = tail = null;
            notify();
```

Otherwise, if our task executed successfully, store the results for this callback in the results array. Check if there are any remaining tasks; if there are and we're still popping tasks in our *while* loop, do nothing, because the loop is dequeueing tasks already. Otherwise, start popping again. <span class=ref>Immediately returning / synchronous tasks don't normally occur for tasks that involve I/O or ones that call *setTimeout*, because these will typically trigger their callback on the next event loop tick.</span> Finally, if there's no remaining tasks, notify the client that we're done.

```javascript
          } else {
            results[i] = r;
            if (--remaining) popping || pop();
            else notify();
          }
        });
```			

Execute the function with a null context and the arguments, including our callback, from above. Note that *this* as context would be inappropriate here, as the called tasks shouldn't have any knowledge of the *queue* internals.

```javascript
        f.apply(null, a);
      }
    }
```

Finally *notify*. This function executes the callback function given by the client in *await* or *awaitAll*. If there was a problem, report this as the first argument; otherwise call the callback either with a list of results or results as function arguments, depending on whether the API called was *awaitAll* or *await*, respectively.

```javascript
    function notify() {
      if (error != null) await(error);
      else if (awaitAll) await(null, results);
      else await.apply(null, [null].concat(results));
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
