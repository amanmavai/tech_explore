> # Performance Aspects

1. **lazy-loading / code-splitting**: lazy load components and images that are not immediately needed with React.lazy and Suspense. use dynamic imports to split your code into chunks. load on demand
   ```jsx
    // Assume we have a component named HeavyComponent in a separate file
    const HeavyComponent = React.lazy(() => import('./HeavyComponent'));

   // next wrap the lazily loaded component with React.Suspense and provide a fallback UI to render while the component is loading
   function App(){
      return <div>
            <h1>Welcome to my App</h1>
            <React.Suspense fallback={<div>Loading...</div>}>
               <HeavyComponent />
            </React.Suspense>
        </div>
   }

   // HeavyComponent will only be loaded when the App component is **_rendered_**
   ```
2. **memoization**: React.memo for components, useMemo for expensive calculations, useCallback to avoid unnecessary rerenders.
4. **optimize-rerenders**: carefully manage your component's rerenders. use the profiler in react-dev-tools to find unnecessary rerenders.
5. **state-management**: organize state properly, server side state vs client side state, colocate state, useReducer + context
6. **list-virtualization**: to improve perf for tables and lists
7. **avoid-memory-leaks**: cleanup event listeners and intervals on component unmount to prevent memory leaks   
8. **core-web-vitals**:     
   **_LCP(largest contentful paint)_**: The amount of time to render the largest content element visible in the viewport, from when the user requests the URL. The largest element is typically an image or video, or perhaps a large block-level text element. This metric is important because it indicates how quickly a visitor sees that the URL is actually loading.      
   **_INP (interaction to next paint)_**: A metric that assesses a page's overall responsiveness to user interactions by observing the time that it takes for the page to respond to all click, tap, and keyboard interactions that occur throughout the lifespan of a user's visit to a page.     
   **_CLS (Cumulative Layout Shift)_**: CLS measures the sum total of all individual layout shift scores for every unexpected layout shift that occurs during the entire lifespan of the page. The score is zero to any positive number, where zero means no shifting and the larger the number, the more layout shift on the page.    

```js
        Good         Need improvement   Poor
 LCP    <=2.5s       <=4s                >4s
 INP    <=200ms      <=500ms             >500ms
 CLS    <=0.1        <=0.25	           >0.25
```

9. **lazy-load-images**: You can use the loading attribute to completely defer the loading of offscreen images:
```html
<!-- visible in the viewport -->
<img src="product-1.jpg" alt="..." width="200" height="200">
<img src="product-2.jpg" alt="..." width="200" height="200">
<img src="product-3.jpg" alt="..." width="200" height="200">

<!-- offscreen images -->
<img src="product-4.jpg" loading="lazy" alt="..." width="200" height="200">
<img src="product-5.jpg" loading="lazy" alt="..." width="200" height="200">
<img src="product-6.jpg" loading="lazy" alt="..." width="200" height="200">
```

> # Security Aspects
1. use HTTPS for Secure connection
2. Sanitize user inputs to prevent XSS attacks. sanitize user inputs from dangerouslySetInnerHTML
3. implement CSP headers
4. Use dependable libraries
5. Secure against CSRF if you are using httpOnly cookies, use sameSite: lax or strict
6. Use env variables for sensitive information
7. Proper Error Handling

> # React Best Practices
1. **solid-project-structure**: organize around routes/features
2. **single-responsibility-principle**: each component should have a single responsibility
3. **custom-hooks**: use custom hooks for complex reusable(stateful) logic
4. Treat all state in React as immutable. To reduce repetitive copying code, use Immer.

Here’s a distilled list of best practices specifically tailored for React.js development:

1. **Opt for Functional Components and Hooks**: Emphasize using functional components and Hooks for cleaner and more concise code, which aligns with the current React advancements.

2. **Immutable State Management**: Always treat state as immutable. Utilize setters or libraries like Redux to manage state updates without directly modifying the state object.
3. **Key Prop**: when iterating over lists

4. **Component Reusability**: Break down complex components into smaller, reusable ones to enhance code maintainability and reduce redundancy.

5. **Prop Types for Typechecking**: Utilize `PropTypes` to ensure components receive properly typed props, which can prevent bugs.

6. **Folder Structure and Naming Conventions**: Maintain a logical project structure and consistent naming conventions to keep the codebase scalable and navigable.

7. **Performance Optimization**: Use techniques like memoization, lazy loading, and conditional rendering to optimize app performance and minimize unnecessary re-renders.

8. **Accessibility (a11y)**: Ensure your app is accessible by using semantic HTML, managing keyboard focus, and employing ARIA roles where necessary.

9. **Unit and Integration Testing**: Regularly write and update tests for your components using tools like Jest and React Testing Library to ensure stability and reliability.

10. **Error Handling with Error Boundaries**: Use error boundaries to catch and handle errors in component trees gracefully, preventing the entire application from crashing.

11. **Prettier Eslint & Typescript**: Use prettier for code formatting, eslint and typescript for catching errors at compile time.
12. **Complex state**: instead of useState use useReducer


