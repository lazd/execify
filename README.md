[![Build Status](https://secure.travis-ci.org/robrich/execify.png?branch=master)](https://travis-ci.org/robrich/execify)
[![Dependency Status](https://david-dm.org/robrich/execify.png)](https://david-dm.org/robrich/execify)

Exec-ify
========

A module for shimmimg between execution modes: streams, promises, and callbacks

About
-----

Have you ever used a library that worked in callbacks when you wanted to work in promises?  Or did it want promises
and you just wanted a simple callback?  This library shims between callbacks, promises, and event streams.
Pass it a function that does any of the 3, and it'll return the results the way you wanted.

Usage
-----

```javascript
var execify = require('execify');

var functionToRun = function () {
  // this function could do anything:
  // - take a callback
  // - return a promise
  // - return a stream
  // - execute synchronously
};

// Return the results as a callback
execify.asCallback(functionToRun, ['optional','array','of','args'], function (err, results) {
  //...
});

// Return the results as a promise
var p = execify.asPromise(functionToRun, ['optional','array','of','args']);
p.then(function (results) {
  // success
}, function (err) {
  // fail
});

// Return the results as an event stream
var s = execify.asStream(functionToRun);
s.on('error', function (err) {
  // fail
});
s.on('end', function () {
  // done: good or bad
});
s.write(['optional','array','of','args']);
s.end();
```

Theory
------

How does it know what the `functionToRun` will do?

- It always appends a callback to the args you gave it
- It examines the results returned from the function
- Does the results have a `then` function? assume it's a promise, see [Promises/A+](http://promises-aplus.github.io/promises-spec/)
- Does the results have a `pipe` function, an `on` function, a `once` function, and an `end` function? assume it's a stream
- Does the function take in more parameters than you passed or was the callback called already? assume it's a callback
- If all else fails, assume synchronous

Does it change `this`?  No.

What if my `functionToRun` doesn't work this way? [let us know](https://github.com/robrich/execify/issues)

Fine print
----------

FRAGILE: Execify catches exceptions on sync runs to pass to your callback
but doesn't hook to process.uncaughtException so it can't pass those exceptions
to your callback

LICENSE
-------

(MIT License)

Copyright (c) 2013 [Richardson & Sons, LLC](http://richardsonandsons.com/)

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
