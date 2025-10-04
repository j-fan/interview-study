## Hoisting

JS moves declarations (not initializations) to the top of their scope during the compilation phase. Function declarations and var variable declarations are hoisted. let and const are also hoisted, but they are not initialized.

Why it's useful: You can structure your logic first (e.g. main()) and define the helper functions later. Variable hoisting with var is a legacy feature.

## Immediately invoked function

```js
(function () {
  console.log("Runs immediately!");
})();
```

## Closure

A closure is when a function “remembers” and can access variables from the scope in which it was created — even after that scope has finished executing.

```js
function outer() {
  let count = 0; // local to outer

  function inner() {
    count++;
    console.log(count);
  }

  return inner;
}

const counter = outer();
counter(); // 1
counter(); // 2
```

✅ Used for:

- Data privacy (simulate private variables)
- Function factories
- Maintaining state between function calls

### Modules

`import, export` - ES modules, standard

`require(“./thing”)` - CommonJS, old node default

## “This” keyword

The value of this depends on how a function is called, not where it’s defined.

Example with a normal functon

```js
function showThis() {
  console.log(this);
}
```

```js
// 1. Called in global context
showThis();
// In strict mode: undefined
// Non-strict: global object (window in browser)
```

```js
// 2. As a method of an object
const obj = { name: "Jane", show: showThis };
obj.show();
// Logs: obj (so this = { name: "Jane", show: [Function] })
```

```js
// 3. With explicit binding
showThis.call({ custom: "value" });
// Logs: { custom: "value" }
```

```js
// 4. As a constructor
function Person(name) {
  this.name = name;
}
const p = new Person("Jane");
console.log(p.name);
// "Jane" (this = new object instance)
```

Arrow functions do not bind their own "this" keyword. However, they will get it from its lexical scope.

```js
const obj = {
  name: "Jane",
  regularMethod() {
    setTimeout(function () {
      console.log("Regular fn:", this.name);
    }, 100);

    setTimeout(() => {
      console.log("Arrow fn:", this.name);
    }, 100);
  },
};
```

```
Regular: undefined   // `this` is global, no "name" there so undefined
Arrow: Jane          // arrow inherited `this` from regularMethod's scope
```

## Event loop

JavaScript runs on a single thread (one call stack). Executes one thing at a time — synchronous code first.

The event loop is a mechanism that coordinates between:

- Call stack (where JS code runs)
- Task queues (things waiting to run later)

It monitors the call stack of the main thread. When the stack is empty, pick the next callback from the task queue and push it onto the call stack so the main thread can run it.

Macro/Task queue

- setTimeout, setInterval, DOM events, I/O callbacks.

Microtask queue

- Promise callbacks (.then, catch, finally), async/await continuations, queueMicrotask().

The microtask queue runs before the task queue at the end of each turn of the event loop.

## Async/await

JS is single threaded. The async job leaves the main thread and goes to the browser's Web APIs. When it is done, Web API puts a callback on the microtask queue. Event loop pulls it off the queue. Main thread resumes async function.

## Webworkers

Web Workers allow you to run JavaScript in a background thread, separate from the main thread.
They’re useful for CPU-intensive tasks (e.g., calculations, image processing) so the UI doesn’t freeze.

Each worker has its own thread, call stack, and event loop, completely independent of the main thread.

Communication happens via message passing (postMessage / onmessage) — no shared memory. Communication is asynchronous — messages from a worker go into the main thread’s task queue, handled by the main thread’s event loop.

## Async/await vs web workers

- Use async/await for I/O-bound tasks (network requests, reading files, database calls).
- Use web workers for CPU-bound tasks (heavy calculations, image processing, data crunching).

## Event bubbling/propagation

In the DOM, when an event happens on an element, it first executes the handler on that element. It goes up each parent, executing all the handlers on the way until it reaches the document root. It will bubble up like this unless stopped with stopPropagation.

## Observers

### 1. MutationObserver

Watches for DOM changes: nodes added/removed, attributes changed, text content changed.

Useful for reacting to dynamic content without polling.

Example:

```js
const target = document.getElementById("myDiv");

const observer = new MutationObserver((mutations) => {
  mutations.forEach((mutation) => {
    console.log("Mutation detected:", mutation);
  });
});

observer.observe(target, { childList: true, attributes: true, subtree: true });

// Example change
target.textContent = "Hello!";
```

- childList: monitor added/removed nodes
- attributes: monitor attribute changes
- subtree: include all descendants

### 2. IntersectionObserver

Watches for visibility changes of elements relative to a container or viewport.

Useful for lazy-loading images, triggering animations, or attaching listeners only when elements are visible.

Example:

```js
const observer = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      console.log("Element is visible:", entry.target);
    }
  });
});

const target = document.querySelector(".item");
observer.observe(target, { root, rootMargin, threshold });
```

- root: container to observe relative to (default = viewport)
- rootMargin: margin around root
- threshold: 0–1, percentage of element visible before callback fires

### 3. ResizeObserver

Watches for element size changes. Useful for responsive layouts or dynamically adjusting content.

Example:

```js
const observer = new ResizeObserver((entries) => {
  entries.forEach((entry) => {
    console.log(
      "Size changed:",
      entry.contentRect.width,
      entry.contentRect.height
    );
  });
});

const box = document.querySelector(".box");
observer.observe(box);
```
