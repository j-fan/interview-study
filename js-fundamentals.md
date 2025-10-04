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
