> # Closure
A closure is the combination of a function bundled together (enclosed) with **references to its surrounding state** (the lexical environment). In other words, a closure gives you access to an outer function's scope from an inner function. In JavaScript, closures are created every time a function is created, at function creation time.

In essence, blocks are finally treated as scopes in ES6, but only if you declare variables with let or const. In addition, ES6 introduced modules, which introduced another kind of scope. Closures are able to capture variables in all these scopes, which we will introduce later.

```js
function makeFunc() {
  const name = "Mozilla";
  function displayName() {
    console.log(name);
  }
  return displayName;
}

const myFunc = makeFunc();
myFunc();

// Here's a slightly more interesting example—a makeAdder function:
function makeAdder(x) {
  return function (y) {
    return x + y;
  };
}

const add5 = makeAdder(5);
const add10 = makeAdder(10);

console.log(add5(2)); // 7
console.log(add10(2)); // 12

// add5 and add10 both form closures. They share the same function body definition,
// but store different lexical environments. In add5's lexical environment, x is 5,
// while in the lexical environment for add10, x is 10.

```
> # Practical closures
Closures are useful because they let you associate data (the lexical environment) with a function that operates on that data. This has obvious parallels to object-oriented programming, where objects allow you to associate data (the object's properties) with one or more methods.
```js
function makeSizer(size) {
  return function () {
    document.body.style.fontSize = `${size}px`;
  };
}

const size12 = makeSizer(12);
const size14 = makeSizer(14);
const size16 = makeSizer(16);

// size12, size14, and size16 are now functions that resize the body text to 12, 14, and 16 pixels, respectively.
// You can attach them to buttons as demonstrated in the following code example.
document.getElementById("size-12").onclick = size12;
document.getElementById("size-14").onclick = size14;
document.getElementById("size-16").onclick = size16;

// html
<button id="size-12">12</button>
<button id="size-14">14</button>
<button id="size-16">16</button>

```

> # Emulating private methods with closures

JavaScript, prior to classes, didn't have a native way of declaring private methods, but it was possible to emulate private methods using closures. Private methods aren't just useful for restricting access to code. They also provide a powerful way of managing your global namespace.

The following code illustrates how to use closures to define public functions that can access private functions and variables. Note that these closures follow the Module Design Pattern

```js
const counter = (function () {
  let privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }

  return {
    increment() {
      changeBy(1);
    },

    decrement() {
      changeBy(-1);
    },

    value() {
      return privateCounter;
    },
  };
})();

console.log(counter.value()); // 0.

counter.increment();
counter.increment();
console.log(counter.value()); // 2.

counter.decrement();
console.log(counter.value()); // 1.

// The shared lexical environment is created in the body of an anonymous function, which is executed
// as soon as it has been defined (also known as an IIFE). The lexical environment contains two private items:
// a variable called privateCounter, and a function called changeBy. You can't access either of these private members
// from outside the anonymous function. Instead, you can access them using the three public functions
// that are returned from the anonymous wrapper.

const makeCounter = function () {
  let privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }
  return {
    increment() {
      changeBy(1);
    },

    decrement() {
      changeBy(-1);
    },

    value() {
      return privateCounter;
    },
  };
};

const counter1 = makeCounter();
const counter2 = makeCounter();

console.log(counter1.value()); // 0.

counter1.increment();
counter1.increment();
console.log(counter1.value()); // 2.

counter1.decrement();
console.log(counter1.value()); // 1.
console.log(counter2.value()); // 0.

// Notice how the two counters maintain their independence from one another. Each closure references a different version
// of the privateCounter variable through its own closure. Each time one of the counters is called, its lexical environment
// changes by changing the value of this variable. Changes to the variable value in one closure don't affect the value in the other closure.

// Note: Using closures in this way provides benefits that are normally associated with object-oriented programming.
// In particular, data hiding and encapsulation.


```

> # Closure Scope Chain

Closures can capture variables in block scopes and module scopes as well. For example, the following creates a closure over the block-scoped variable y:

```js
function outer() {
  let getY;
  {
    const y = 6;
    getY = () => y;
  }
  console.log(typeof y); // undefined
  console.log(getY()); // 6
}

outer();


// Closures over modules can be more interesting.
// myModule.js
let x = 5;
export const getX = () => x;
export const setX = (val) => {
  x = val;
};

// Here, the module exports a pair of getter-setter functions, which close over the module-scoped variable x.
// Even when x is not directly accessible from other modules, it can be read and written with the functions.

import { getX, setX } from "./myModule.js";

console.log(getX()); // 5
setX(6);
console.log(getX()); // 6

```

> # Creating closures in loops: A common mistake

```js
// html
<p id="help">Helpful notes will appear here</p>
<p>Email: <input type="text" id="email" name="email" /></p>
<p>Name: <input type="text" id="name" name="name" /></p>
<p>Age: <input type="text" id="age" name="age" /></p>


// js

function showHelp(help) {
  document.getElementById("help").textContent = help;
}

function setupHelp() {
  var helpText = [
    { id: "email", help: "Your email address" },
    { id: "name", help: "Your full name" },
    { id: "age", help: "Your age (you must be over 16)" },
  ];

  for (var i = 0; i < helpText.length; i++) {
    // Culprit is the use of `var` on this line
    var item = helpText[i];
    document.getElementById(item.id).onfocus = function () {
      showHelp(item.help);
    };
  }
}

setupHelp();

//The reason for this is that the functions assigned to onfocus form closures;
// they consist of the function definition and the captured environment from the setupHelp function's scope.
// Three closures have been created by the loop, but each one shares the same single lexical environment,
// which has a variable with changing values (item). This is because the variable item is declared with var
// and thus has function scope due to hoisting. The value of item.help is determined when the onfocus callbacks are executed.
//  Because the loop has already run its course by that time, the item variable object (shared by all three closures)
// has been left pointing to the last entry in the helpText list.


// One solution in this case is to use more closures: in particular, to use a function factory as described earlier:
function showHelp(help) {
  document.getElementById("help").textContent = help;
}

function makeHelpCallback(help) {
  return function () {
    showHelp(help);
  };
}

function setupHelp() {
  var helpText = [
    { id: "email", help: "Your email address" },
    { id: "name", help: "Your full name" },
    { id: "age", help: "Your age (you must be over 16)" },
  ];

  for (var i = 0; i < helpText.length; i++) {
    var item = helpText[i];
    document.getElementById(item.id).onfocus = makeHelpCallback(item.help);
  }
}

setupHelp();

// This works as expected. Rather than the callbacks all sharing a single lexical environment,
// the makeHelpCallback function creates a new lexical environment for each callback,
// in which help refers to the corresponding string from the helpText array.

// If you don't want to use more closures, you can use the let or const keyword:
function showHelp(help) {
  document.getElementById("help").textContent = help;
}

function setupHelp() {
  const helpText = [
    { id: "email", help: "Your email address" },
    { id: "name", help: "Your full name" },
    { id: "age", help: "Your age (you must be over 16)" },
  ];

  for (let i = 0; i < helpText.length; i++) {
    const item = helpText[i];
    document.getElementById(item.id).onfocus = () => {
      showHelp(item.help);
    };
  }
}

setupHelp();

// This example uses const instead of var, so every closure binds the block-scoped variable,
// meaning that no additional closures are required.

```











