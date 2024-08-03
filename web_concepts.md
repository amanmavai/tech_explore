> # Critical Rendering Path

The Critical Rendering Path is the sequence of steps the browser goes through to convert the HTML, CSS, and JavaScript into pixels on the screen. Optimizing the critical render path improves render performance. The critical rendering path includes the Document Object Model (DOM), CSS Object Model (CSSOM), render tree and layout.

> The document object model is created as the HTML is parsed.   
> The HTML may request JavaScript, which may, in turn, alter the DOM.   
> The HTML includes or makes requests for styles, which in turn builds the CSS object model.   
> The browser engine combines the two to create the Render Tree.   
> Layout determines the size and location of everything on the page.   
> Once layout is determined, pixels are painted to the screen.

A request for a web page or app starts with an HTML request. The server returns the HTML - response headers and data.   
The browser then begins parsing the HTML, converting the received bytes to the DOM tree.    
The browser initiates requests every time it finds links to external resources, be it stylesheets, scripts, or embedded image references.   
Some requests are blocking, which means the parsing of the rest of the HTML is halted until the imported asset is handled.   
The browser continues to parse the HTML making requests and building the DOM, until it gets to the end,   
at which point it constructs the CSS object model. With the DOM and CSSOM complete,    
the browser builds the render tree, computing the styles for all the visible content.    
After the render tree is complete, layout occurs, defining the location and size of all the render tree elements.   
Once complete, the page is rendered, or 'painted' on the screen.

> # DOM
The DOM tree describes the content of the document. The `<html>` element is the first element and root node of the document tree. The tree reflects the relationships and hierarchies between different elements. Elements nested within other elements are child nodes. The greater the number of DOM nodes, the longer it takes to construct the DOM tree.

> # CSSOM
The CSSOM contains all the information on how to style the DOM.

> # Render Tree
The render tree captures both the content and the styles: the DOM and CSSOM trees are combined into the render tree. To construct the render tree, the browser checks every node, starting from root of the DOM tree, and determines which CSS rules are attached.

The render tree only captures visible content. The head section (generally) doesn't contain any visible information, and is therefore not included in the render tree. If there's a display: none; set on an element, neither it, nor any of its descendants, are in the render tree.


> # Layout
Once the render tree is built, layout becomes possible. Layout is dependent on the size of screen. The layout step determines where and how the elements are positioned on the page, determining the width and height of each element, and where they are in relation to each other.

The first time the size and position of each node is determined is called **layout**. Subsequent recalculations of are called **reflows**

> # Paint
The last step is painting the pixels to the screen. Once the render tree is created and layout occurs, the pixels can be painted to the screen. On load, the entire screen is painted. After that, only impacted areas of the screen will be repainted, as browsers are optimized to repaint the minimum area required.

The last step in the critical rendering path is **painting the individual nodes to the screen**, the first occurrence of which is called the **first meaningful paint**.

> # Compositing
When sections of the document are drawn in different layers, overlapping each other, compositing is necessary to ensure they are drawn to the screen in the right order and the content is rendered correctly.

> # Javascript Compilation
> JavaScript is parsed, compiled, and interpreted. The scripts are parsed into **abstract syntax trees**. Some browser engines take the abstract syntax trees and pass them into a compiler, outputting **bytecode**. This is known as JavaScript compilation. Most of the code is interpreted on the main thread, but there are exceptions such as code run in web workers.

> # CSS and Images
No, downloading CSS or images from the server does **not block HTML parsing** in the same way that JavaScript does, but they do have their own implications on the rendering process:

1. **CSS Downloading**: 
   - **Impact on Parsing**: CSS itself does not block HTML parsing. The browser can continue to parse HTML while CSS files are being downloaded.
   - **Impact on Rendering**: However, CSS does block rendering. The browser won't render the page until it has processed the CSS that applies to the content up to that point. This is because the visual layout of the page depends on the CSS.
   - **CSSOM Construction**: The browser needs to construct the CSSOM (CSS Object Model) to understand how to render the page, and it can't do this until all CSS is downloaded and parsed.

2. **Image Downloading**:
   - **Impact on Parsing and Rendering**: Images do not block HTML parsing. The browser can continue parsing the HTML even as images are being downloaded. However, the final rendering of the page might be delayed until images are fully loaded, especially if image dimensions are not specified in the HTML, as this affects layout calculations.
   - **Asynchronous Loading**: Unlike JavaScript and CSS, images are loaded asynchronously by default. The browser can continue with parsing and other operations while images are being fetched.

In summary, while CSS and images don't block HTML parsing like synchronous JavaScript does, they play a crucial role in the overall rendering process. CSS must be processed to correctly render the page, and images can affect layout and rendering times.

---

> # http1.1 vs http2

Links:
https://stackoverflow.com/questions/36517829/what-does-multiplexing-mean-in-http-2  

https://freecontent.manning.com/animation-http-1-1-vs-http-2-vs-http-2-with-push/   

https://freecontent.manning.com/mental-model-graphic-how-is-http-1-1-different-from-http-2/

Certainly, let's delve into the major differences between HTTP/1.1 and HTTP/2, outlined in five key points with examples for a clearer understanding:

But in 2015, a reimagined version called **HTTP/2** came into use

1. **Textual vs Binary Protocol**:
   - **HTTP/1.1**: It is a textual protocol, meaning requests and responses are in human-readable text format. 
     - *Example*: Request headers and methods like `GET /index.html HTTP/1.1` are readable text.
   - **HTTP/2**: It is a binary protocol, which makes it more efficient for computers to parse but less human-readable.
     - *Example*: The same request in HTTP/2 is encoded into a binary frame, not directly readable.

2. **Multiplexing**:
   - **HTTP/1.1**: Does not support multiplexing; multiple requests must queue, leading to head-of-line blocking.
     - *Example*: If a browser requests multiple resources (like images, CSS, JS), each request waits for the previous one to finish.
   - **HTTP/2**: Supports multiplexing; multiple requests and responses can be interleaved on the same connection.
     - *Example*: A browser can request multiple resources simultaneously over a single connection, reducing load times.

3. **Header Compression**:
   - **HTTP/1.1**: Headers are sent in full text each time, which can become repetitive and increase load.
     - *Example*: Every request carries full headers, including cookies and user-agent strings, even if they are the same as the previous request.
   - **HTTP/2**: Uses HPACK compression for headers, reducing redundant header information.
     - *Example*: After the first request, only changed or new headers are transmitted, reducing the amount of data sent.

4. **Server Push**:
   - **HTTP/1.1**: Lacks server push capabilities; the server can only respond to explicit requests from the client.
     - *Example*: A client requests an HTML file, then after parsing, it realizes it needs CSS and JS files, initiating more requests.
   - **HTTP/2**: Includes server push, where the server can send resources before they are explicitly requested by the client.
     - *Example*: The server sends the HTML file and proactively pushes associated CSS and JS files, anticipating the client's needs.

5. **Connection Reuse**:
   - **HTTP/1.1**: Supports persistent connections but often underutilizes them, leading to more TCP connections.
     - *Example*: A browser may open multiple TCP connections to the same server to download resources in parallel.
   - **HTTP/2**: More effectively reuses a single connection, reducing the overhead of multiple TCP connections.
     - *Example*: A single TCP connection is used to send multiple parallel requests, reducing latency and resource usage.

In summary, HTTP/2 provides significant improvements in efficiency, speed, and performance over HTTP/1.1 through binary protocols, multiplexing, header compression, server push, and better connection reuse.

> # defer vs async
Understanding the `defer` and `async` attributes in script tags is crucial for optimizing the loading and execution of JavaScript, particularly in the context of modern web frameworks. Let's delve into each attribute in detail and explore how popular frameworks utilize them.

### 1. The `defer` Attribute
The `defer` attribute tells the browser to download the script while parsing the HTML, but to execute it only after the HTML parsing is complete. This does not block the HTML parsing, leading to faster page load times.

- **Real-World Example**: 
  - Imagine a webpage using a framework like Vue.js. The Vue.js library could be loaded using a `defer` attribute to ensure that it does not block the parsing of the HTML:
    ```html
    <script src="vue.js" defer></script>
    ```
  - This allows the browser to build the DOM while Vue.js is being downloaded. Once the DOM is fully constructed, the Vue.js script executes, initializing the Vue components or the app.

- **Use Case**: 
  - Best for scripts that need the full DOM but do not need to be executed immediately.
  - Often used for scripts that enhance elements on the page (like sliders, widgets).

### 2. The `async` Attribute
The `async` attribute, like `defer`, allows the script to be downloaded asynchronously. However, as soon as the script is downloaded, it's executed, regardless of the state of the HTML parsing.

- **Real-World Example**:
  - Consider a tracking script like Google Analytics. This script can be loaded asynchronously since its operation is independent of the page content:
    ```html
    <script src="https://www.google-analytics.com/analytics.js" async></script>
    ```
  - Google Analytics starts gathering data as soon as it loads and executes, which does not need to wait for the entire page to load.

- **Use Case**:
  - Ideal for scripts that do not depend on the DOM and do not have dependencies on other scripts.
  - Commonly used for analytics, ads, or other scripts where timing is not tied to page content.

### Defer vs Async in Frameworks
Modern JavaScript frameworks often handle script loading internally, especially in single-page applications (SPAs). Frameworks like React, Angular, and Vue.js have build tools (like Webpack, Vite) that bundle and optimize scripts.

- **React (Create React App)**:
  - The build process generates a bundled JavaScript file typically loaded with a `defer` attribute to ensure it executes after the HTML is parsed.
  - Example:
    ```html
    <script src="static/js/main.chunk.js" defer></script>
    ```

- **Angular**:
  - Angular's CLI also bundles and minifies scripts. The main bundle is often loaded with `defer` for the same reasons as React.
  - Example:
    ```html
    <script src="main-es2015.js" type="module" defer></script>
    ```

- **Vue.js**:
  - Vue CLI similarly generates a production build where the main JavaScript file is deferred.
  - Example:
    ```html
    <script src="js/app.js" defer></script>
    ```

### Conclusion
- `defer` is generally used by frameworks for the main JavaScript bundle to ensure that the script runs after the HTML is fully parsed.
- `async` is more suited for independent scripts like analytics or third-party widgets that should run independently of the page's loading state.

Frameworks typically abstract these details away from the developer, but understanding their underlying behavior is valuable, especially when optimizing page load and script execution.

> Scripts with type="module" are implicitly deferred. They do not block the parsing of the document   
>  and are executed only after the document has been fully parsed. This behavior is similar to scripts   
> with the defer attribute, but with additional capabilities related to module features.

> # Todo List App in Vanilla Js
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <script src="https://cdn.tailwindcss.com"></script>
  </head>
  <body>
    <div id="root" class="container mx-auto flex flex-col items-center w-96">
      <input
        class="mt-8 border border-green-300 p-2 rounded w-96"
        id="search-task"
        type="text"
        placeholder="Search tasks"
      />
      <form class="mt-8">
        <input
          type="text"
          class="w-96 border border-gray-400 p-2 rounded"
          placeholder="Add Todo..."
          name="todo_input"
        />
      </form>
      <ul id="todo_list" class="flex flex-col gap-2 mt-8 w-full">
        <!-- Tasks will be added here -->
      </ul>
    </div>
    <script>
      const formElement = document.querySelector("form");
      formElement.addEventListener("submit", (event) => {
        event.preventDefault();

        const inputValue = event.target["todo_input"].value;
        if (inputValue.trim() !== "") {
          addTodo(inputValue);
          event.target.reset();
        }
      });

      function addTodo(text) {
        const li = document.createElement("li");
        li.className = "flex items-center gap-4";

        li.innerHTML = `
          <input type="checkbox" class="w-8 h-8" />
          <span class="p-2" contenteditable="true">${text}</span>
          <button class="ml-auto bg-red-300 text-white px-2 py-1 rounded-md">X</button>
        `;

        const span = li.querySelector("span");
        span.addEventListener("blur", () => {
          if (span.textContent.trim() === "") {
            li.remove();
          }
        });

        const checkboxElem = li.querySelector("input[type='checkbox']");
        checkboxElem.addEventListener("click", () => {
          span.classList.toggle("line-through");
        });

        const deleteBtn = li.querySelector("button");
        deleteBtn.addEventListener("click", () => {
          li.remove();
        });

        const todoList = document.querySelector("ul");
        todoList.appendChild(li);
      }

      document
        .getElementById("search-task")
        .addEventListener("input", function (event) {
          const searchText = event.target.value.toLowerCase();
          const tasks = document.querySelectorAll("#todo_list li");

          tasks.forEach((task) => {
            const taskText = task
              .querySelector("span")
              .textContent.toLowerCase();
            task.style.display = taskText.includes(searchText) ? "" : "none";
          });
        });
    </script>
  </body>
</html>

```

> Script at the end of body: No need for DOMContentLoaded as the HTML elements are already loaded.   
> Script in the head or before HTML elements: Use DOMContentLoaded to wait until the HTML elements are loaded.
