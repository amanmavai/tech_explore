> # Links
https://github.com/lydiahallie/javascript-questions   
https://dev.to/lydiahallie/series/3341

> # DeepClone

```js
function deepClone(obj){
    // handle null , undefined and non-object types
    if(obj === null || typeof obj !== "object"){
        return obj;
    }

    // handle Date
    if(obj instanceof Date){
        return new Date(obj.getTime());
    }

    // handle Array
    if(Array.isArray(obj)){
        const arrayCopy = [];
        obj.forEach((v, i) => {
            arrayCopy[i] = deepClone(v);
        });
        return arrayCopy;
    }

    // handle object
    if(obj instanceof Object){
        const objCopy = {};
        Object.entries(obj).forEach(([key, value]) => {
            objCopy[key] = deepClone(value);
        })
        return objCopy;
    }

    // handle other cases also here;

    throw new Error("Unable to copy object, Its type isn't supported.");
}

let original = {a: 1, b: 2, c: [1, 2, 3]};
let copiedObj = deepClone(original);
copiedObj.c.push(4);
console.log("Test", {original, copiedObj});

```

> # remove elements

```js
function remove_elements(arr, n = -1) {
    let res = [];

    if (n < 0) {
        let start = Math.max(arr.length + n, 0);
        for (let i = start; i < arr.length; i++) {
            res.push(arr[i]);
        }
    } else {
        let end = Math.min(n, arr.length - 1);
        for (let i = 0; i <= end; i++) {
            res.push(arr[i]);
        }
    }

    return res;
}

remove_elemements([1, 2, 3]);
// remove_elemements([1, 2, 3], -1);
// remove_elemements([1, 2, 3], -2); // [2, 3]
// remove_elemements([1, 2, 3], -4);
```

> # scope

```js
function test1() {
  //  Arrow functions do not have their own this context; instead, they inherit this from
  // the surrounding lexical context. In this case, the arrow function inherits this from
  //  the test1 function, which, due to the use of .call(), is the object {name: "Aman"}.
    setTimeout(() => {
        console.log(`My name is ${this.name}`);
    }, []);
}

test1.call({name: "Aman"}) // My name is Aman
```

> # Flatten Array

```js
var flat = function (arr, n) {
    if (n === 0) return arr;

    let res = [];

    for (let i = 0; i < arr.length; i++) {
        if (Array.isArray(arr[i])) {
            res.push(...flat(arr[i], n - 1));
        } else {
            res.push(arr[i]);
        }
    }

    return res;
};
```

> # Compact Object
Given an object or array obj, return a compact object. A compact object is the same as the original object, except with keys containing falsy values removed. This operation applies to the object and any nested objects. Arrays are considered objects where the indices are keys. A value is considered falsy when Boolean(value) returns false.
```js
var compactObject = function(obj) {
    // These three if statements deal with when obj is not an iterable object
    // Steps 1-3 as described above
    if (obj === null) return null;
    if (typeof obj !== "object") return obj;
    if (Array.isArray(obj)) return obj.filter(Boolean).map(compactObject);

    // This for loop deals with when obj is an iterable object
    // Steps 4-5 as described above
    const compacted = {};
    for (const key in obj) {
        let value = compactObject(obj[key]);
        if (value) compacted[key] = value;
    }

    return compacted;
};
```

> # Join Two Arrays by ID
Given two arrays arr1 and arr2, return a new array joinedArray. All the objects in each of the two inputs arrays will contain an id field that has an integer value. joinedArray is an array formed by merging arr1 and arr2 based on their id key. The length of joinedArray should be the length of unique values of id. The returned array should be sorted in ascending order based on the id key.

If a given id exists in one array but not the other, the single object with that id should be included in the result array without modification.

If two objects share an id, their properties should be merged into a single object:

If a key only exists in one object, that single key-value pair should be included in the object.
If a key is included in both objects, the value in the object from arr2 should override the value from arr1.

```js
var join = function(arr1, arr2) {
    let map = {};
    arr1.forEach(obj => map[obj.id] = obj);
    arr2.forEach(obj => {
        if(map[obj.id]){
            map[obj.id] = {...map[obj.id], ...obj};
        } else {
            map[obj.id] = obj;
        }
    });

    return Object.values(map);
};
```
> # Execute Async Functions in Parallel PromiseAll

```js
var promiseAll = function (functions) {
    return new Promise((resolve, reject) => {
        let results = [];
        let count = 0;
        functions.forEach((fn, i) => {
            fn().then(val => {
                results[i] = val;
                count++;
                if (count === functions.length) resolve(results);
            }).catch(reason => reject(reason));
        })
    })
};

/**
 * const promise = promiseAll([() => new Promise(res => res(42))])
 * promise.then(console.log); // [42]
 */
```

> # Debounce

```js
/**
 * @param {Function} fn
 * @param {number} t milliseconds
 * @return {Function}
 */
var debounce = function (fn, t) {
    let h;
    return function (...args) {
        clearTimeout(h);
        h = setTimeout(() => {
            fn.apply(this, args) // fn(...args);
        }, t);
    }
};

/**
 * const log = debounce(console.log, 100);
 * log('Hello'); // cancelled
 * log('Hello'); // cancelled
 * log('Hello'); // Logged at t=100ms
 */
```

> # Remove Duplicates

```js
// Step 1: Extend the String prototype.
String.prototype.removeDuplicates = function() {
  // Step 2: Split the string into words.
  const words = this.split(' ');

  // Step 3: Use a Set to track unique words.
  const uniqueWords = new Set(words);

  // Step 4: Convert the Set back to an array, then join into a string.
  return Array.from(uniqueWords).join(' ');
};

const originalString = "hello world hello universe";
const stringWithoutDuplicates = originalString.removeDuplicates();
console.log(stringWithoutDuplicates); // Outputs: "hello world universe"

```

> # isPrime

```js
function isPrime(num) {
  if (num <= 1) return false;
  for (let i = 2; i <= Math.sqrt(num); i++) {
    if (num % i === 0) return false;
  }
  return true;
}

```

> # removeVowels

```js
function removeVowels(str) {
  return str.replace(/[aeiou]/gi, '');
}

```
> # call vs apply vs bind

```js
const person = {
    name: 'Dave',
    age: 30,
};

function updateAgeAndCountry(newAge, country) {
    this.age = newAge;
    this.country = country;
}

updateAgeAndCountry.call(person, 35, 'Canada');
console.log(applyDetails.apply(person, []));

function applyDetails() {
    return `${this.name} is ${this.age} and lives in ${this.country}.`;
}

// What will the output be?
// Dave is 35 and lives in Canada.

```


```js
function saySomething(additional) {
    console.log(`Hello, ${this.primary} and ${this.secondary}${additional}`);
}

const primaryContext = {
    primary: 'Earth',
    secondary: 'Mars',
};

const boundFunction = saySomething.bind(primaryContext, '!!!');

boundFunction.call({ primary: 'Venus', secondary: 'Jupiter' }, '?');

// What will the output be?
// Hello, Earth and Mars!!!
// This shows that once bind is used to set the this context, subsequent calls to call or apply cannot change it.
```

> # problem chaining
```js
class Calc {
    constructor() {
        this.sum = 0;
    }

    lacs(n) {
        this.sum += n * 100000;
        return this;
    }
    crore(n) {
        this.sum += n * 10000000;
        return this;
    }
    thousand(n) {
        this.sum += n * 1000;
        return this;
    }
    value(){
        return this.sum;
    }
}

function computeAmount() {
    return new Calc();
}

console.log(computeAmount().lacs(15).crore(5).crore(2).lacs(20).thousand(45).crore(7).value())

```
