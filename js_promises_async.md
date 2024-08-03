# talks
> https://www.youtube.com/watch?v=cCOL7MC4Pl0 (Jake Archibald on the web browser event loop, setTimeout, micro tasks, requestAnimationFrame)   
> https://www.youtube.com/watch?v=8aGhZQkoFbQ&t=597s (What the heck is the event loop anyway? | Philip Roberts | JSConf EU)
> https://res.cloudinary.com/practicaldev/image/fetch/s--IouCFfQW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bhqfok6galmgm8w6xjmg.png (download and use this image later)

# promises
>  A Promise is an object representing the eventual completion or failure of an asynchronous operation

The Promise() constructor creates Promise objects. It is primarily **used to wrap callback-based APIs** that do not already support promises.

> new Promise(executor) (The executor is custom code that ties an outcome in a callback to a promise. You, the programmer, write the executor.)
```js
function executor(resolveFunc, rejectFunc) {
  // Typically, some asynchronous operation that accepts a callback,
  // like the `readFile` function above
}
```

> [!NOTE]
> If your task is already promise-based, you likely do not need the Promise() constructor.

> For example, the callback-based readFile API above can be transformed into a promise-based one.
```js
const readFilePromise = (path) =>
  new Promise((resolve, reject) => {
    readFile(path, (error, result) => {
      if (error) {
        reject(error);
      } else {
        resolve(result);
      }
    });
  });

readFilePromise("./data.txt")
  .then((result) => console.log(result))
  .catch((error) => console.error("Failed to read data"));

```
> # promise rejection

> Calling rejectFunc obviously causes the promise to reject.  
> However, there are also two ways to cause the promise to instantly become rejected even when the resolveFunc callback is called.

JS

```js
// 1. Resolving with the promise itself
const rejectedResolved1 = new Promise((resolve) => {
  // Note: resolve has to be called asynchronously,
  // so that the rejectedResolved1 variable is initialized
  setTimeout(() => resolve(rejectedResolved1)); // TypeError: Chaining cycle detected for promise #<Promise>
});

// 2. Resolving with an object which throws when accessing the `then` property
const rejectedResolved2 = new Promise((resolve) => {
  resolve({
    get then() {
      throw new Error("Can't get then property");
    },
  });
});
```

> # promise_execution
```js

setTimeout(() => {
  console.log("event-loop cycle: Promise (fulfilled)", promise);
}, 0);

// The new Promise constructor takes a function as an argument. This function is called the executor.
// The executor function is executed immediately by the Promise implementation, passing resolve and reject functions (the so-called "control" functions).
const promise = new Promise((resolve, reject) => {
  console.log("Promise callback");
  resolve();
}).then((result) => {
  console.log("Promise callback (.then)");
});

console.log("Promise (pending)", promise);

// "Promise callback" (immediately when the Promise executor runs)
// "Promise (pending)" (just after the promise is created but not yet resolved)
// "Promise callback (.then)" (after the promise is resolved)
// "event-loop cycle: Promise (fulfilled)" (after the current event loop cycle)

```
> # async_await example
```js

async function fetchData() {
    console.log('Start fetching');
    const data = await new Promise(resolve=>setTimeout(()=>resolve("Data fetched"), 1000));
    console.log("data", data);
    console.log('Finished fetching');

    const data2 = await new Promise(resolve=>setTimeout(()=>resolve("Data fetched2"), 1000));
    console.log("data2", data2);
}

console.log('Before async call');
fetchData();
console.log('After async call');
console.log('After async call2');

```

> # micro_tasks example
```js

console.log('Script start');

setTimeout(() => {
    console.log('Task 1');
}, 0);

Promise.resolve().then(() => {
    console.log('Microtask 1');
});

Promise.resolve().then(() => {
    console.log('Microtask 2');
});

Promise.resolve().then(() => {
    console.log('Microtask 3');
});

Promise.resolve().then(() => {
    console.log('Microtask 4');
});

console.log('Script end');

```

> # queueMicrotask

> The queueMicrotask() method, which is exposed on the **Window** or **Worker** interface, queues a microtask to be executed   
> at a safe time prior to control returning to the browser's event loop.
```js
queueMicrotask(() => {
  // function contents here
});
```
> JavaScript **promises** and the **Mutation Observer API** both use the microtask queue to run their callbacks

> # tasks vs microtasks

The event loop driving your code handles these tasks one after another, in the order in which they were enqueued. 
The oldest runnable task in the task queue will be executed during a single iteration of the event loop. 
After that, microtasks will be executed until the microtask queue is empty, and then the browser may choose to update rendering. 
Then the browser moves on to the next iteration of event loop.

First, each time a task exits, the event loop checks to see if the task is returning control to other JavaScript code.   
If not, it runs all of the microtasks in the microtask queue. The microtask queue is, then, processed multiple times   
per iteration of the event loop, including after handling events and other callbacks.   

Second, if a microtask adds more microtasks to the queue by calling queueMicrotask(), 
those newly-added microtasks execute before the next task is run. That's because the event loop will keep calling microtasks  
until there are none left in the queue, even if more keep getting added.

```js
let callback = () => log("Regular timeout callback has run");

let urgentCallback = () => log("*** Oh noes! An urgent callback has run!");

log("Main program started");
setTimeout(callback, 0);
queueMicrotask(urgentCallback);
log("Main program exiting");

// Result
// Main program started
// Main program exiting
// *** Oh noes! An urgent callback has run!
// Regular timeout callback has run
```

```js

let callback = () => console.log("Regular timeout callback has run");

let urgentCallback = () => console.log("*** Oh noes! An urgent callback has run!");

let doWork = () => {
  let result = 1;

  queueMicrotask(urgentCallback);

  for (let i = 2; i <= 10; i++) {
    result *= i;
  }
  return result;
};

console.log("Main program started");
setTimeout(callback, 0);
console.log(`10! equals ${doWork()}`);
console.log("Main program exiting");

// result
// Main program started
// 10! equals 3628800
// Main program exiting
// *** Oh noes! An urgent callback has run!
// Regular timeout callback has run
```

```js
const button = document.createElement('button');
button.textContent = 'Click me';
document.body.appendChild(button);

button.addEventListener("click", () => {
  Promise.resolve().then(() => console.log("Microtask 1"));
  console.log("Listener1");
})

button.addEventListener("click", () => {
  Promise.resolve().then(() => console.log("Microtask 2"));
  console.log("Listener2");
})


// if user clicks the button
// Listener1, Microtask1, Listener2, Microtask2

// if javscript clicks the button
// button.click()
// Listener1, Listener2, Microtask1, Microtask2

```

> # microtasks_block_rendering

> Microtasks on the other hand, they are processed to completion, including any additionally queued items
> so if you were adding items to the queue, as quickly as you are processing them, you are processing   
> microtasks forever
> The event loop cannot continue until that queue has completely emptied. And that is why it blocks rendering.

```js
function loop(){
  Promise.resolve().then(loop);
}
loop();
```

> # sleep

```js
function sleep(duration) {
    return new Promise(resolve => {
        setTimeout(resolve, duration);
    });
}
```

```js
setTimeout(resolve, duration);
```
In this scenario, resolve simply changes the state of the promise from "pending" to "fulfilled" but does not associate any value with the fulfillment. This is typical for a sleep-like function where the primary purpose is to introduce a delay rather than to process or return specific data.

However, it's worth noting that the resolve function can accept an argument if needed. For instance, if you were creating a promise that resolves with a specific value after a delay, you could modify the function to pass a value to resolve. But in the standard use of a sleep function, resolve is called without any arguments.

```js
function sleep(duration, value) {
    return new Promise(resolve => {
        setTimeout(() => resolve(value), duration);
    });
}
```
# mn_utils

```js
function processInChunks(array, chunkSize) {
  return new Promise((resolve) => {
    let index = 0;
    
    function nextChunk() {
      const end = Math.min(index + chunkSize, array.length);
      for (; index < end; index++) {
        // Process array[index]
      }

    // If there are more elements, we use setTimeout(nextChunk, 0) to schedule the next call to nextChunk. 
    // This effectively yields control back to the browser, allowing it to update the UI or handle other events.
      if (index < array.length) {
        // Schedule the next chunk
        setTimeout(nextChunk, 0);
      } else {
        // All chunks processed, resolve the Promise
        resolve();
      }
    }

    nextChunk();
  });
}

const largeArray = [/* large array data */];
processInChunks(largeArray, 1000)
  .then(() => console.log('Processing complete!'));

```

> Batching operations (https://developer.mozilla.org/en-US/docs/Web/API/HTML_DOM_API/Microtask_guide)
```js
const messageQueue = [];

let sendMessage = (message) => {
  messageQueue.push(message);

  if (messageQueue.length === 1) {
    queueMicrotask(() => {
      const json = JSON.stringify(messageQueue);
      messageQueue.length = 0;
      fetch("url-of-receiver", json);
    });
  }
};


```
