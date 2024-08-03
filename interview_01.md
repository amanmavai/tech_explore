> # problem solving
```js
// solve from leetcode
// 1768. Merge Strings Alternately
// 1431. Kids With the Greatest Number of Candies
```

> # architecture


```js
// The Critical Rendering Path is the sequence of steps the browser goes through to convert the HTML, CSS, and JavaScript into pixels on the screen.

// explain MPA vs SPA vs SSR vs SSG architectures prevalent in the industry today

// state management, how would you organize state management overall
```

> # react
```jsx
  // 1
  export default function Counter() {
    const [number, setNumber] = useState(0);

    return (
      <>
       <h1>{number}</h1>
       <button onClick={() => {
          setNumber(number + 1);
          setNumber(number + 1);
          setNumber(number + 1);
       }}>+3</button>
      </>
    )
  }

 // 2
 // pending and completed counter (after 3 seconds, pending should go to 0 and completed should reach n)

// 3
// mini assignment

// 4
// few react js questions (useEffect, useLayoutEffect)
// state management, how would you organize state in your application
// you have to create a financial dashboard kind of app, which has few charts at the home page, and then invoices and customers, how would you design this app
// Performance
// Security

// 5

```

> # Javascript
```js
    // js
    // promises why are they useful what are their advantages
    


    // 1
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

  // 2
  // sorted array
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

  // 3
  // write a delay function using promises
  function delay(duration) {
      return new Promise(resolve => {
          setTimeout(resolve, duration);
      });
  }
```

> # CSS

```js
// give some assignments to create

// create a layout like
-- Home Icon---------------------------------------- FLink--SLink--

// box layout

// others
```
