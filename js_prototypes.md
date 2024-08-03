> # Links
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain#constructors

> # Inheritance and Prototype Chain

The [[Prototype]] internal slot can be accessed and modified with the **Object.getPrototypeOf()** and **Object.setPrototypeOf()** functions respectively. 

It should not be confused with the **func.prototype** property of functions, which instead specifies the [[Prototype]] to be assigned to all instances of objects created by the given function when used as a constructor. We will discuss the prototype property of constructor functions in a later section.

In an object literal like `{ a: 1, b: 2, __proto__: c }`, the value c (which has to be either null or another object) will become the [[Prototype]] of the object represented by the literal, while the other keys like a and b will become the own properties of the object. 

**This syntax reads very naturally, since `[[Prototype]]` is just an "internal property" of the object.**

```js
// It's worth noting that the { __proto__: ... } syntax is different from the obj.__proto__ accessor:
// the former is standard and not deprecated

const o = {
  a: 1,
  b: 2,
  // __proto__ sets the [[Prototype]]. It's specified here
  // as another object literal.
  __proto__: {
    b: 3,
    c: 4,
  },
};

// o.[[Prototype]] has properties b and c.
// o.[[Prototype]].[[Prototype]] is Object.prototype (we will explain
// what that means later).
// Finally, o.[[Prototype]].[[Prototype]].[[Prototype]] is null.
// This is the end of the prototype chain, as null,
// by definition, has no [[Prototype]].
// Thus, the full prototype chain looks like:
// { a: 1, b: 2 } ---> { b: 3, c: 4 } ---> Object.prototype ---> null

console.log(o.a); // 1
// Is there an 'a' own property on o? Yes, and its value is 1.

console.log(o.b); // 2
// Is there a 'b' own property on o? Yes, and its value is 2.
// The prototype also has a 'b' property, but it's not visited.
// This is called Property Shadowing

console.log(o.c); // 4
// Is there a 'c' own property on o? No, check its prototype.
// Is there a 'c' own property on o.[[Prototype]]? Yes, its value is 4.

console.log(o.d); // undefined
// Is there a 'd' own property on o? No, check its prototype.
// Is there a 'd' own property on o.[[Prototype]]? No, check its prototype.
// o.[[Prototype]].[[Prototype]] is Object.prototype and
// there is no 'd' property by default, check its prototype.
// o.[[Prototype]].[[Prototype]].[[Prototype]] is null, stop searching,
// no property found, return undefined.


```

Similarly, you can create longer prototype chains, and a property will be sought on all of them.

```js

const o = {
  a: 1,
  b: 2,
  // __proto__ sets the [[Prototype]]. It's specified here
  // as another object literal.
  __proto__: {
    b: 3,
    c: 4,
    __proto__: {
      d: 5,
    },
  },
};

// { a: 1, b: 2 } ---> { b: 3, c: 4 } ---> { d: 5 } ---> Object.prototype ---> null

console.log(o.d); // 5

```
When an inherited function is executed, the value of this points to the inheriting object, not to the prototype object where the function is an own property.

```js
const parent = {
  value: 2,
  method() {
    return this.value + 1;
  },
};

console.log(parent.method()); // 3
// When calling parent.method in this case, 'this' refers to parent

// child is an object that inherits from parent
const child = {
  __proto__: parent,
};
console.log(child.method()); // 3
// When child.method is called, 'this' refers to child.
// So when child inherits the method of parent,
// The property 'value' is sought on child. However, since child
// doesn't have an own property called 'value', the property is
// found on the [[Prototype]], which is parent.value.

child.value = 4; // assign the value 4 to the property 'value' on child.
// This shadows the 'value' property on parent.
// The child object now looks like:
// { value: 4, __proto__: { value: 2, method: [Function] } }
console.log(child.method()); // 5
// Since child now has the 'value' property, 'this.value' means
// child.value instead

```

> # Constructors

Constructor function, which automatically sets the [[Prototype]] for every object manufactured. Constructors are functions called with new.
```js
// A constructor function
function Box(value) {
  this.value = value;
}

// Properties all boxes created from the Box() constructor
// will have
Box.prototype.getValue = function () {
  return this.value;
};

const boxes = [new Box(1), new Box(2), new Box(3)];

// Every instance created from a constructor function will automatically have the constructor's prototype property as its [[Prototype]]
Object.getPrototypeOf(new Box()) === Box.prototype // true


// Constructor.prototype by default has one own property: constructor, which references the constructor function itself
Box.prototype.constructor === Box // This allows one to access the original constructor from any instance

const box1 = new Box(1);

Object.getPrototypeOf(box1).constructor === Box // true
box1.constructor === Box.prototype.constructor // true
box1.constructor === Box;

// The above constructor function can be rewritten in classes as:

class Box {
  constructor(value) {
    this.value = value;
  }

  // Methods are created on Box.prototype
  getValue() {
    return this.value;
  }
}

// Classes are syntax sugar over constructor functions, which means you can still manipulate
// Box.prototype to change the behavior of all instances

// Because Box.prototype references the same object as the [[Prototype]] of all instances,
// we can change the behavior of all instances by mutating Box.prototype.

function Box(value) {
  this.value = value;
}
Box.prototype.getValue = function () {
  return this.value;
};
const box = new Box(1);

// Mutate Box.prototype after an instance has already been created
Box.prototype.getValue = function () {
  return this.value + 1;
};
box.getValue(); // 2

```

> Constructor.prototype is only useful when constructing instances. It has nothing to do with Constructor.[[Prototype]], which is the constructor function's own prototype, which is Function.prototype — that is, Object.getPrototypeOf(Constructor) === Function.prototype.


A corollary is, re-assigning Constructor.prototype (Constructor.prototype = ...) is a bad idea for two reasons:

The [[Prototype]] of instances created before the reassignment is now referencing a different object from the [[Prototype]] of instances created after the reassignment — mutating one's [[Prototype]] no longer mutates the other.   

Unless you manually re-set the constructor property, the constructor function can no longer be traced from instance.constructor, which may break user expectation. Some built-in operations will read the constructor property as well, and if it is not set, they may not work as expected.

```js
function MyConstructor() {}

const instance1 = new MyConstructor();

// Reassign the prototype
MyConstructor.prototype = {
  newMethod: function() {}
};

const instance2 = new MyConstructor();

// instance1 doesn't have newMethod, but instance2 does.
// Also, instance1.constructor points to the original MyConstructor,
// but instance2.constructor doesn't point to MyConstructor unless explicitly set.


```

> # Implicit constructors of literals   

Some literal syntaxes in JavaScript create instances that implicitly set the [[Prototype]]. For example:
```js
// Object literals (without the `__proto__` key) automatically
// have `Object.prototype` as their `[[Prototype]]`
const object = { a: 1 };
Object.getPrototypeOf(object) === Object.prototype; // true

// Array literals automatically have `Array.prototype` as their `[[Prototype]]`
const array = [1, 2, 3];
Object.getPrototypeOf(array) === Array.prototype; // true

// RegExp literals automatically have `RegExp.prototype` as their `[[Prototype]]`
const regexp = /abc/;
Object.getPrototypeOf(regexp) === RegExp.prototype; // true

// We can "de-sugar" them into their constructor form.
const array = new Array(1, 2, 3);
const regexp = new RegExp("abc");
// For example, "array methods" like map() are simply methods defined on Array.prototype,
// which is why they are automatically available on all array instances.

```

> # Building longer inheritance chains

The `Constructor.prototype` property will become the `[[Prototype]]` of the constructor's instances

```js
// Therefore, a typical constructor will build the following prototype chain:

function Constructor() {}

const obj = new Constructor();
// obj ---> Constructor.prototype ---> Object.prototype ---> null
// Object.getPrototypeOf(Object.prototype) === null
```

To build longer prototype chains, we can set the [[Prototype]] of `Constructor.prototype` via the `Object.setPrototypeOf()` function.

```js
function Base() {}
function Derived() {}
// Set the `[[Prototype]]` of `Derived.prototype`
// to `Base.prototype`
Object.setPrototypeOf(Derived.prototype, Base.prototype);

const obj = new Derived();
// obj ---> Derived.prototype ---> Base.prototype ---> Object.prototype ---> null

// In class terms, this is equivalent to using the extends syntax.
class Base {}
class Derived extends Base {}

const obj = new Derived();
// obj ---> Derived.prototype ---> Base.prototype ---> Object.prototype ---> null


```

You may also see some legacy code using Object.create() to build the inheritance chain. However, because this reassigns the prototype property and removes the constructor property, it can be more error-prone, while performance gains may not be apparent if the constructors haven't created any instances yet.

```js
function Base() {}
function Derived() {}
// Re-assigns `Derived.prototype` to a new object
// with `Base.prototype` as its `[[Prototype]]`
// DON'T DO THIS — use Object.setPrototypeOf to mutate it instead
Derived.prototype = Object.create(Base.prototype);

```

> Inspecting Prototypes
```js
function doSomething() {}
console.log(doSomething.prototype);
// It does not matter how you declare the function; a
// function in JavaScript will always have a default
// prototype property — with one exception: an arrow
// function doesn't have a default prototype property:
const doSomethingFromArrowFunction = () => {};
console.log(doSomethingFromArrowFunction.prototype);

// inspecting prototypes
function doSomething() {}
doSomething.prototype.foo = "bar";
const doSomeInstancing = new doSomething();
doSomeInstancing.prop = "some value";
console.log("doSomeInstancing.prop:     ", doSomeInstancing.prop);       // some value
console.log("doSomeInstancing.foo:      ", doSomeInstancing.foo);        //  bar
console.log("doSomething.prop:          ", doSomething.prop);            // undefined
console.log("doSomething.foo:           ", doSomething.foo);             // undefined
console.log("doSomething.prototype.prop:", doSomething.prototype.prop);  // undefined
console.log("doSomething.prototype.foo: ", doSomething.prototype.foo);   // bar

```

> # Different ways of creating and mutating prototype chains

`__proto__` in object literal initializers is standardized and optimized, and can even be more performant than `Object.create`.   

Declaring extra own properties on the object at creation is more ergonomic than Object.create.

```js
const o = { a: 1 };
// The newly created object o has Object.prototype as its [[Prototype]]
// Object.prototype has null as its prototype.
// o ---> Object.prototype ---> null

const b = ["yo", "whadup", "?"];
// Arrays inherit from Array.prototype
// (which has methods indexOf, forEach, etc.)
// The prototype chain looks like:
// b ---> Array.prototype ---> Object.prototype ---> null

function f() {
  return 2;
}
// Functions inherit from Function.prototype
// (which has methods call, bind, etc.)
// f ---> Function.prototype ---> Object.prototype ---> null

const p = { b: 2, __proto__: o };
// It is possible to point the newly created object's [[Prototype]] to
// another object via the __proto__ literal property. (Not to be confused
// with Object.prototype.__proto__ accessors)
// p ---> o ---> Object.prototype ---> null


```

> ## With constructor functions
```js
function Graph() {
  this.vertices = [];
  this.edges = [];
}

Graph.prototype.addVertex = function (v) {
  this.vertices.push(v);
};

const g = new Graph();
// g is an object with own properties 'vertices' and 'edges'.
// g.[[Prototype]] is the value of Graph.prototype when new Graph() is executed.

```

> ## With Object.create
Calling Object.create() creates a new object. The [[Prototype]] of this object is the first argument of the function:
```js
const a = { a: 1 };
// a ---> Object.prototype ---> null

const b = Object.create(a);
// b ---> a ---> Object.prototype ---> null
console.log(b.a); // 1 (inherited)

const c = Object.create(b);
// c ---> b ---> a ---> Object.prototype ---> null

const d = Object.create(null);
// d ---> null (d is an object that has null directly as its prototype)
console.log(d.hasOwnProperty);
// undefined, because d doesn't inherit from Object.prototype


```

> ## With Classes
```js
class Rectangle {
  constructor(height, width) {
    this.name = "Rectangle";
    this.height = height;
    this.width = width;
  }
}

class FilledRectangle extends Rectangle {
  constructor(height, width, color) {
    super(height, width);
    this.name = "Filled rectangle";
    this.color = color;
  }
}

const filledRectangle = new FilledRectangle(5, 10, "blue");
// filledRectangle ---> FilledRectangle.prototype ---> Rectangle.prototype ---> Object.prototype ---> null

```

> ## With Object.setPrototypeOf()
While all methods above will set the prototype chain at object creation time, `Object.setPrototypeOf()` allows mutating the `[[Prototype]]` internal property of an existing object.
```js
const obj = { a: 1 };
const anotherObj = { b: 2 };
Object.setPrototypeOf(obj, anotherObj);
// obj ---> anotherObj ---> Object.prototype ---> null

```

> ## With the __proto__ accessor
All objects inherit the `Object.prototype.__proto__` setter, which can be used to set the `[[Prototype]]` of an existing object (if the `__proto__` key is not overridden on the object).
Warning: `Object.prototype.__proto__` accessors are non-standard and deprecated. You should almost always use Object.setPrototypeOf instead.
```js
const obj = {};
// DON'T USE THIS: for example only.
obj.__proto__ = { barProp: "bar val" };
obj.__proto__.__proto__ = { fooProp: "foo val" };
console.log(obj.fooProp);
console.log(obj.barProp);

```

> # Prototype General
```js

const myObject = {
  city: "Madrid",
  greet() {
    console.log(`Greetings from ${this.city}`)
  }
}

// in practice all browsers use __proto__ for the property of an object that points to its prototype
// The standard way to access an object's prototype is the Object.getPrototypeOf() method.

myObject.__proto__
Object.getPrototypeOf(myObject)


// ** So when we call myObject.toString(), the browser: **
// 1. looks for toString in myObject
// 2. can't find it there, so looks in the prototype object of myObject for toString
// 3. finds it there, and calls it.

Object.getPrototypeOf(myObject) === Object.prototype
// returns true

// The prototype of Object.prototype is null, so it's at the end of the prototype chain
Object.getPrototypeOf(Object.prototype) // null

myObject.__proto__.__proto__ // null


// The prototype of an object is not always Object.prototype
const myDate = new Date();
let object = myDate;
// is to go through entire prototype chain
do {
  object = Object.getPrototypeOf(object);
  console.log(object);
} while (object);

// Date.prototype
// Object { }
// null


// Setting a prototype
//1. The Object.create() method creates a new object and allows you to specify an object that will be used as the new object's prototype.
const personPrototype = {
  greet() {
    console.log("hello!");
  },
};

const carl = Object.create(personPrototype);
carl.greet(); // hello!

//2. constructor
const personPrototype = {
  greet() {
    console.log(`hello, my name is ${this.name}!`);
  },
};

// In JavaScript, all **functions** have a property named prototype. When you call a function as a constructor,
// this property is set as the prototype of the newly constructed object (by convention, in the property named __proto__).
function Person(name) {
  this.name = name;
}

Object.assign(Person.prototype, personPrototype);
// or
// Person.prototype.greet = personPrototype.greet;

// After this code, objects created using Person() will get Person.prototype as their prototype, which automatically contains the greet method.
const reuben = new Person("Reuben");
reuben.greet(); // hello, my name is Reuben!

reuben.__proto__ === Person.prototype // true


```
