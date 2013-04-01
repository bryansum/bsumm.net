---
title: Analyzing mbostock's <i>queue.js</i>
layout: post
---

Even with the proliferation of open source software on the web, relatively few developers take the time to study the source code of the software they might use everyday. I believe this can largely be traced back to a lack of culture around code reading, which I will hopefully talk about in a later post. To combat this for now, however, the answer is simple: we just need to practice looking at and studying the works of others, similar to the way painters [studied (and copied) the great masters](http://www.paulgraham.com/hp.html). 

Beginning today, I'm beginning a series of "deep dives." First is a look into a tiny JavaScript asynchronous helper library called *queue*, written by Mike Bostock (of [d3.js fame](http://d3js.org/)). One of the most identifiable trends in JS today is the desire to prevent "callback spaghetti:" highly nested callbacks that need to execute in some order. Many solutions have been implemented, from [promises](http://promises-aplus.github.com/promises-spec/) to [generators](https://developer.mozilla.org/en-US/docs/JavaScript/Guide/Iterators_and_Generators) to a [combination of both](http://taskjs.org/). Mike takes a simple approach: the API comprises one method, *defer*, which takes in a task (a simple function), along with optional arguments. To read them back, *await* and *awaitAll*. These differ only in how they return their results array. Mike mentions the entire source can be compressed down to around 500 bytes, so this journey shouldn't take too long.

I'd recommend looking the [source from GitHub](https://github.com/mbostock/queue) and [taking a quick peek at *queue.js*](https://raw.github.com/mbostock/queue/master/queue.js) in its entirety before reading this to get a quick sense of the overall code layout.

## *queue.js*

Let's begin. We start with an [anonymous self-invoking function](http://stackoverflow.com/questions/5815757/what-exactly-is-the-point-of-this-function-construct-why-is-it-needed). This is designated by the convention of using a right-paren before the function definition. This parenthesis is optional, but helps clarify intent to the reader that this function will be executed immediately at the end of this definition.

```javascript
(function() {
```

Next, we check for require.js/Node.js-type export commands; if these exist, add our queue function to our module exports. Otherwise add it to *self*. Assigning to *self* was new to me, as I typically expect modules to assign to the *window* global for browsers; however, it turns out modern browsers resolve *self* to *window* in the browser, and to the Web Worker context [in a Web Worker](http://blog.vjeux.com/2011/javascript/javascript-one-line-global-export.html). Thus, assigning to *self* covers both usage contexts in one go. Note it may also seem that *queue* is seemingly undefined at this point; however, because JavaScript hoists variable names to the top of functions, the function *queue* from below will be immediately available.

```javascript
  if (typeof module === "undefined") self.queue = queue;
  else module.exports = queue;

  queue.version = "1.0.2";
```

This is a shorthand for *Array.prototype.slice*.

```javascript
  var slice = [].slice;

  function queue(parallelism) {
```

*queue* holds our to-be-returned object to the client. This initially seems confusing, as it shadows the name of the function we're exporting as a library, but this makes more sense in this context from a naming perspective than naming it something else. Most other variables are already explained:

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

Setting a default parallelism is important later, as this will always make our *while* check for throttling active calls work.

```javascript
    if (!parallelism) parallelism = Infinity;
```

Next, *defer* takes in the arguments for the call, which is an array-like object. *queue*'s API specifies the first argument is the task function, and the rest are optional arguments. Note that we also refuse to execute *defer* if we've seen an error with any previous tasks.

```javascript
    queue.defer = function() {
      if (!error) {
        var node = arguments;
```

While deferring a task, *queue* first adds a new *undefined* result in the results array. This reserves space for the result, firstly, and also since *Array.push* returns the *.length* of the array, in this case, *node.i* now gets set to the index of where the result should be in the *results* array. Then there's some logic for adding to the linked list of queue tasks, and incrementing the remaining tasks count for later. Finally, we execute our *pop()* method, where most of the the magic happens, but we'll look at this in a second. Note that our *defer* method executes our tasks immediately. So ends our *defer* method.

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

The *await* method and *awaitAll* methods are similar; they simply set the variables we were talking about, and optionally, if there weren't any remaining tasks at this point, immediately notify our callback of our status. These methods also return their parent object, *queue*, which enables a nice [builder pattern-like](http://en.wikipedia.org/wiki/Builder_pattern) API. See the [examples in the documentation](https://github.com/mbostock/queue).

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

*Pop* is the internal method responsible for popping remaining tasks off the queue and executing them, *n* at a time, where *n* is our *parallelism* value. The *popping* variable here is used to signal to the task's internal callback function, which we'll see below, whether or not we're still popping tasks off our queue via our *while* loop. This comes into play in the case that a task completes immediately (i.e., not on the next event loop) so that we know not to fire the *pop* call again, since in that case we're already still executing this function.

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

Otherwise, if our task executed successfully, store the results for this callback in the results array. Check if there are any remaining tasks; if there are and we're still in our *popping* phase (this can occur when our deferred function returns immediately from the function *apply*-cation below), do nothing, because the while loop will continue to add tasks on its next iteration. However, if we're not still *popping* tasks (this will happen if the task doesn't return immediately, i.e., most tasks), fire the loop again. Finally, if there's no remaining tasks, notify the caller that we're done.

```javascript
          } else {
            results[i] = r;
            if (--remaining) popping || pop();
            else notify();
          }
        });
```			

Execute the function with a null context and the arguments, including our callback, from above. Note that *this* as context would be inappropriate here, as the called tasks shouldn't have any knowledge of the *queue* functions.

```javascript
        f.apply(null, a);
      }
    }
```

Finally *notify*. This function executes the callback function given by the client in *await* or *awaitAll*. If there was a problem, report is as the first argument; otherwise call the callback either with a list of results or results as function arguments, depending on whether the API called was *awaitAll* or *await*, respectively.

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
That wasn't so bad, huh? It's nice to see a library with such high utility and so little code. Hopefully there was at least something to learn from in the source. If you're interested in reading more async libraries for JavaScript, check out the source to [kriskowal's *q*](https://github.com/kriskowal/q), [Tom and Yehuda's *rsvp.js*](https://github.com/tildeio/rsvp.js), or even [Mozilla's experimental *task.js*](https://github.com/mozilla/task.js) as well. 

Please [feel free to comment](mailto:bsummersett@gmail.com) on feedback, style, etc., as I'm looking to create these posts on a somewhat regular cadence.
