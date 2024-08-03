> # JS Concepts

```js
// promises

async await vs promise

// Q1: 
setTimeout(() => {
  console.log("event-loop cycle: Promise (fulfilled)", promise);
}, 0);

const promise = new Promise((resolve, reject) => {
  console.log("Promise callback");
  resolve();
}).then((result) => {
  console.log("Promise callback (.then)");
});

console.log("Promise (pending)", promise);

// Q2:

console.log('Script start');

setTimeout(() => {
    console.log('T 1');
}, 0);

Promise.resolve().then(() => {
    console.log('M 1');
});

Promise.resolve().then(() => {
    console.log('M 2');
});

Promise.resolve().then(() => {
    console.log('M 3');
});

Promise.resolve().then(() => {
    console.log('M 4');
});

console.log('Script end');

// objects
Object.defineProperty(anotherObj,"fave",{
    value: 42,
    enumerable: true,     // default if omitted
    writable: true,       // default if omitted
    configurable: true    // default if omitted
});

// prototype
draw the protptype chain of
const array = [1, 2, 3];
Object.getPrototypeOf(array) === Array.prototype; // true

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

> # CSS Layout
4 layouts for the price of 1
link: https://www.joshwcomeau.com/css/interactive-guide-to-flexbox/
```html
<style>
  form {
    display: flex;
    align-items: flex-end;
    flex-wrap: wrap;
    gap: 8px;
  }
  .name {
    flex-grow: 1;
    flex-basis: 120px;
  }
  .email {
    flex-grow: 3;
    flex-basis: 170px;
  }
  button {
    flex-grow: 1;
    flex-basis: 70px;
  }
</style>

<form>
  <label class="name" for="name-field">
    Name:
    <input id="name-field" />
  </label>
  <label class="email" for="email-field">
    Email:
    <input id="email-field" type="email" />
  </label>
  <button>
    Submit
  </button>
</form>

// qn

// create this layout using css grid or flex-box

sidebar  header  

sidebar  main;


// create this layout

   - ---
  -- --
 --- -

// create three squares touching corners
<div class="bg-black h-[50vh] w-[50vh] grid grid-rows-3">
  <div class="bg-yellow-400 w-1/3 justify-self-end"></div>
  <div class="bg-yellow-400 w-1/3 justify-self-center"></div>
  <div class="bg-yellow-400 w-1/3"></div>
</div>
----
    1
  2
3
----
// create a chess board
https://play.tailwindcss.com/NntUFbqrpX
```



> # Problem Solving
```js
function isAnagram(s, t){
    const source = "abcdefghijklmnopqrstuvwxyz";
    let firstCount = 0;
    let secondCount = 0;
    
    for(let i = 0; i < source.length; i++){
        const regex = new RegExp(source[i], "g");

        firstCount = s.match(regex) === null ? 0 : s.match(regex).length;
        secondCount = t.match(regex) === null ? 0 : t.match(regex).length;
        
        if(firstCount !== secondCount){
            return false;
        }
    }
    return true;
}

isAnagram("abcde", "edcba");

function isAnagram(s, t) {
    if (s.length !== t.length) return false;

    const charCount = new Map();
    
    for (let i = 0; i < s.length; i++) {
        charCount.set(s[i], (charCount.get(s[i]) || 0) + 1);
    }

    for (let i = 0; i < t.length; i++) {
        if (!charCount.has(t[i]) || charCount.get(t[i]) === 0) return false;
        charCount.set(t[i], charCount.get(t[i]) - 1);
    }

    return true;
}

isAnagram("abcde", "edcba");


function binarySearch(arr, target) {
    let left = 0;
    let right = arr.length - 1;

    while (left <= right) {
        const mid = Math.floor((left + right) / 2);
        const midValue = arr[mid];

        if (midValue === target) {
            return mid;
        } else if (midValue < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }

    return -1; // Target not found
}
const myArray = [1, 3, 5, 7, 9, 11, 13, 15];
const target = 7;
binarySearch(myArray, target);

/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[][]}
 */
const levelOrder = (root) => {
    if (!root) return []

    const results = [];
    
    const queue = [root];
    while (queue.length > 0) {
        const currentLength = queue.length;
        const currentLevel = [];

        for (let i = 0; i < currentLength; i++) {
            const node = queue.shift();
            if (node.left) {
                queue.push(node.left);
            }
            if (node.right) {
                queue.push(node.right);
            }
            currentLevel.push(node.val);
        }

        results.push(currentLevel);
    }

    return results;
}

// Input: root = [3,9,20,null,null,15,7]
// Output: [[3],[9,20],[15,7]]

```

> # React Mini Assignment

```jsx
  ref vs regular state

  useEffect vs useLayoutEffect

  thoughts on state management in react (any libraries)

  how do you start any project
```

```jsx

import React, {useState, useEffect} from "react";

// Custom hook for debouncing
const useDebounce = (value, delay) => {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    /**
     * This cleanup function is important. It ensures that if the value or delay changes before the timeout is completed,
     * the previous timeout is cleared. that means last value didn't get updated
     * This means if the input value (value) changes before the delay period elapses,
     * the previous timeout (waiting to update debouncedValue) is cancelled. A new timeout will start based on the most recent change.
     */
    return () => clearTimeout(handler);
  }, [value, delay]);

  /**
   * Finally, the hook returns the debouncedValue.
   * This value reflects the input value but delayed by the specified delay time.
   * It only updates after the user has stopped changing the value for at least the duration of delay.
   */
  return debouncedValue;
};

// Component for rendering user results
const UserResult = ({user}) => (
  <div key={user.id} className="py-2 text-green-600 flex items-center gap-4">
    <img src={user.avatar_url} alt={user.login} className="h-12 w-12 rounded-full" />
    <div>{user.login}</div>
  </div>
);

/**
 * Practical Example
 * Imagine a user is typing in a search box:
 *
 * The user types "Hello", then stops typing for more than 500 milliseconds.
 * The debouncedValue will be updated to "Hello" after 500 milliseconds of inactivity.
 * If the user types another letter, say "H", before 500 milliseconds have elapsed, the timeout is reset,
 * and the debouncedValue will not update yet.
 * The debouncedValue only updates once there's a pause in typing for the duration of the delay.
 */

export const Search = () => {
  const [value, setValue] = useState("");
  const [results, setResults] = useState([]);
  const [isSearching, setIsSearching] = useState(false);
  const [error, setError] = useState(null);

  const debouncedValue = useDebounce(value, 500);

  useEffect(() => {
    if (!debouncedValue) {
      setResults([]);
      return;
    }

    const fetchUsers = async () => {
      setIsSearching(true);
      setError(null);

      try {
        const response = await fetch(`https://api.github.com/search/users?q=${encodeURIComponent(debouncedValue)}`);
        const data = await response.json();
        setResults(data.items ?? []);
      } catch (error) {
        setError("Failed to fetch users");
        console.error("Error:", error);
      } finally {
        setIsSearching(false);
      }
    };

    fetchUsers();
  }, [debouncedValue]);

  return (
    <div className="container mx-auto p-4">
      <input
        type="text"
        className="w-full p-2 border border-gray-300 rounded-md"
        placeholder="Search GitHub Users..."
        value={value}
        onChange={(e) => setValue(e.target.value)}
      />
      <div className="mt-4">
        {isSearching && <div>Searching...</div>}
        {error && <div>Error: {error}</div>}
        {results.map((user) => (
          <UserResult key={user.id} user={user} />
        ))}
      </div>
    </div>
  );
};


```
