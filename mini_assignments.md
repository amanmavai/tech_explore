> # Todos
- [ ] https://www.youtube.com/watch?v=szXMBGKGTdE

> # Github Rate Limit

GitHub has rate limits on its API to prevent abuse. If you're not using authentication, the rate limit for unauthenticated requests
is quite low (e.g., 60 requests per hour).    

> **Solution**: To increase the rate limit, authenticate your requests using a personal access token or OAuth. This can significantly increase
your rate limit (e.g., up to 5000 requests per hour).



> # TreeView

```jsx
import React from 'react';

const TreeView = ({ data, level = 0 }) => {
  return (
    <div
      style={{ marginLeft: `${level * 40}px` }}
      className={`list-none ${level > 0 ? 'ml-4' : ''}`}
    >
      {data.map((item, index) => {
        return <TreeNode key={index} node={item} level={level} />;
      })}
    </div>
  );
};

const TreeNode = ({ node, level }) => {
  const [isOpen, setIsOpen] = React.useState(false);
  const hasChildren = node.children && node.children.length > 0;
  return (
    <div>
      <div
        onClick={() => setIsOpen(!isOpen)}
        className={`cursor-pointer ${
          level === 0 ? 'font-bold' : 'font-normal'
        } hover:text-blue-600`}
      >
        {hasChildren && (
          <span className="inline-block w-4 text-center">
            {isOpen ? '-' : '+'}
          </span>
        )}
        {node.title}
      </div>
      {hasChildren && isOpen && (
        <TreeView data={node.children} level={level + 1} />
      )}
    </div>
  );
};

export default TreeView;

```

> # Todo_List
```jsx
import React from "react";
function getUniqueId() {
  return Date.now();
}

export function TodosList() {
 const [todos, setTodos] = React.useState([
    { id: 1, text: 'Learn React', completed: false },
    { id: 2, text: 'Read about ViteJS', completed: false },
  ]);
  function onDelete(id) {
    setTodos((prevTodos) => prevTodos.filter((todo) => todo.id !== id));
  }
  function onUpdate({id, text, completed}) {
    setTodos((prevTodos) => {
      return prevTodos.map((todo) => {
        if (todo.id === id) {
          return {...todo, text, completed};
        }
        return todo;
      });
    });
  }
  function onAdd(text) {
    const newTodo = {id: getUniqueId(), text, completed: false};
    setTodos((prevTodos) => [...prevTodos, newTodo]);
  }

  function handleSubmit(e) {
    e.preventDefault();
    const inputValue = e.target.todoItem.value;
    if (inputValue.trim() !== "") {
      onAdd(inputValue);
      e.target.reset();
    }
  }
  return (
    <div>
      <div>
        <form onSubmit={handleSubmit}>
          <input type="text" name="todoItem" className="p-2 border border-gray-300 text-2xl" />
        </form>
      </div>
      <ul className="flex flex-col gap-2 mt-8">
        {todos.map((todo) => (
          <TodoItem key={todo.id} todo={todo} onUpdate={onUpdate} onDelete={onDelete} />
        ))}
      </ul>
    </div>
  );
}

function TodoItem({todo, onUpdate, onDelete}) {
  const [isEditing, setIsEditing] = React.useState(false);
  const editInputRef = React.useRef(null);

  React.useEffect(() => {
    if (isEditing) {
      editInputRef.current.focus();
    }
  }, [isEditing]);

  return (
    <li className="flex items-center gap-4">
      <input
        type="checkbox"
        className="form-checkbox h-8 w-8 rounded-md text-blue-600 cursor-pointer"
        onChange={(e) => onUpdate({id: todo.id, completed: e.target.checked, text: todo.text})}
      />
      {isEditing && (
        <input
          type="text"
          ref={editInputRef}
          defaultValue={todo.text}
          className="h-8 border border-gray-200 p-2"
          onKeyDown={(e) => (e.key === "Enter" ? setIsEditing(false) : undefined)}
          onBlur={() => {
            setIsEditing(false);
          }}
          onChange={(e) => {
            if (e.target.value.trim() !== "") {
              onUpdate({id: todo.id, text: e.target.value, completed: todo.completed});
            } else {
              onUpdate({id: todo.id, text: todo.text, completed: todo.completed});
            }
          }}
        />
      )}
      {!isEditing && (
        <span
          className="text-lg font-semibold h-8 cursor-pointer"
          style={{textDecorationLine: todo.completed ? "line-through" : ""}}
          onClick={() => {
            setIsEditing(true);
          }}
        >
          {todo.text}
        </span>
      )}
      <button className="px-2 py-1 rounded-md bg-red-400 text-white ml-auto" onClick={() => onDelete(todo.id)}>
        X
      </button>
    </li>
  );
}

```

> # Search_Debouncing
```jsx
import React, { useState, useEffect } from 'react';

const useDebounce = (value, delay) => {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
};

export const SearchComponent = ({ onSearch }) => {
  const [searchTerm, setSearchTerm] = useState('');
  const debouncedSearchTerm = useDebounce(searchTerm, 500);

  useEffect(() => {
    if (debouncedSearchTerm != null) {
      onSearch(debouncedSearchTerm);
    }
  }, [debouncedSearchTerm, onSearch]);

  return (
    <input
      type="text"
      value={searchTerm}
      onChange={(e) => setSearchTerm(e.target.value)}
      placeholder="Search..."
    />
  );
};

// Usage example (you need to define the onSearch function)
// <SearchComponent onSearch={searchQuery => console.log(searchQuery)} />
```

> # Traffic Light
```jsx

import React, { useState, useEffect } from 'react';

const TrafficLight = () => {
  const [light, setLight] = useState('red');

  useEffect(() => {
    const timer = setInterval(() => {
      setLight((currentLight) => {
        switch (currentLight) {
          case 'red':
            return 'green';
          case 'green':
            return 'yellow';
          case 'yellow':
            return 'red';
          default:
            return 'red';
        }
      });
    }, 3000); // Change the light every 3 seconds

    return () => clearInterval(timer);
  }, []);

  return (
    <div className="traffic-light flexCenter">
      <div className={`light ${light === 'red' ? 'redLight' : 'baseLight'}`} />
      <div
        className={`light ${light === 'green' ? 'greenLight' : 'baseLight'}`}
      />
      <div
        className={`light ${light === 'yellow' ? 'yellowLight' : 'baseLight'}`}
      />
    </div>
  );
};

export default TrafficLight;

```

> # Github User Search with Debounce (Typeahead)
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

> # Infinite Scrolling

```jsx

import React, {useState, useEffect, useRef, useCallback} from "react";

const useInfiniteScroll = (scrollableRef, loadMore) => {
  const isInitialLoad = useRef(true);

  useEffect(() => {
    const handleScroll = () => {
      if (!scrollableRef.current) return;

      /**
      scrollTop: The number of pixels the content of the element is scrolled vertically.
      clientHeight: The inner height of the element, not including the scroll bar.
      scrollHeight: The total height of the element's content, including the content not visible on the screen due to overflow.

      The function then checks if the user has scrolled to the bottom of the element. This is done by checking if the sum of scrollTop
      and clientHeight is greater than or equal to scrollHeight - 5. The - 5 creates a 5px threshold before the actual bottom, making
      the experience smoother as it preloads the data slightly before reaching the bottom.
      */
      const {scrollTop, scrollHeight, clientHeight} = scrollableRef.current;
      if (scrollTop + clientHeight >= scrollHeight - 5) {
        loadMore();
      }
    };

    const scrollableElement = scrollableRef.current;

    if (scrollableElement) {
      scrollableElement.addEventListener("scroll", handleScroll);

      // Perform initial data load if necessary
      if (isInitialLoad.current) {
        loadMore();
        isInitialLoad.current = false;
      }
    }

    return () => {
      if (scrollableElement) {
        scrollableElement.removeEventListener("scroll", handleScroll);
      }
    };
  }, [loadMore]);
};

const InfiniteScrollComponent = () => {
  const [items, setItems] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [lastUserId, setLastUserId] = useState(0);
  const scrollableRef = useRef(null);

  const fetchData = useCallback(async () => {
    if (loading) return; // Prevent multiple fetches when already loading
    try {
      setLoading(true);
      // const response = await fetch(`https://api.github.com/users?since=${lastUserId}&per_page=15`); use this for unauthenticated requests
      const accessToken = import.meta.env.VITE_GITHUB_TOKEN; // Access the token
      const response = await fetch(`https://api.github.com/users?since=${lastUserId}&per_page=15`, {
        headers: {
          Authorization: `Bearer ${accessToken}`,
        },
      });
      if (!response.ok) throw new Error("Network response was not ok");
      const data = await response.json();

      setItems((prevItems) => [...prevItems, ...data]);
      if (data.length > 0) {
        setLastUserId(data[data.length - 1].id);
      }
    } catch (err) {
      setError(err);
    } finally {
      setLoading(false);
    }
  }, [lastUserId, loading]);

  useInfiniteScroll(scrollableRef, fetchData);

  return (
    <div
      ref={scrollableRef}
      className="scrollable-container overflow-auto h-96 mx-auto p-4 px-20 flex flex-col gap-4 border border-gray-300 border-solid"
    >
      {items.map((user) => (
        <div key={user.id} className="item flex gap-4">
          <img src={user.avatar_url} alt={user.login} className="w-10 h-10 rounded-full" />
          <h4 className="text-green-500 text-lg">{user.login}</h4>
        </div>
      ))}
      {loading && <p>Loading...</p>}
      {error && <p>Error: {error.message}</p>}
    </div>
  );
};

export default InfiniteScrollComponent;

```

> # Timer

```jsx
import React from "react";

export function Component() {
  const [count, setCount] = React.useState(10);
  const [status, setStatus] = React.useState("off"); // Default status is 'off'
  const ref = React.useRef(null);

  // Start or stop the timer based on status
  React.useEffect(() => {
    if (status === "on") {
      ref.current = setInterval(() => {
        setCount((prevCount) => {
          if (prevCount <= 1) {
            clearInterval(ref.current);
            return 0;
          }
          return prevCount - 1;
        });
      }, 10);
    } else {
      clearInterval(ref.current);
    }

    // Cleanup on unmount or status change
    return () => clearInterval(ref.current);
  }, [status]);

  React.useEffect(() => {
    if (count === 0) {
      setStatus("off");
    }
  }, [count]);

  // Update count and ensure it's valid
  const handleCountChange = (e) => {
    const newValue = parseInt(e.target.value, 10);
    if (!isNaN(newValue) && newValue >= 0) {
      setCount(newValue);
    } else {
      setCount(0); // Reset to 0 if input is invalid
    }
  };

  // Simplify button handlers by directly setting status
  const handlePlayClick = () => setStatus("on");
  const handlePauseClick = () => setStatus("off");
  const handleStopClick = () => {
    setStatus("off");
    setCount(100); // Reset to initial value or desired reset value
  };

  return (
    <div className="mx-auto flex max-w-md flex-col gap-8">
      <div className="flex flex-col items-center gap-4">
        <label className="text-3xl font-semibold text-green-500">{count}</label>
        <input
          type="number" // Ensure numeric input
          value={count}
          onChange={handleCountChange}
          className="w-48 border border-gray-200 p-2"
        />
      </div>
      <div className="flex items-center justify-center gap-4">
        <button onClick={handlePlayClick} className="rounded-md bg-gray-300 px-2 py-3">
          Play
        </button>
        <button onClick={handlePauseClick} className="rounded-md bg-gray-300 px-2 py-3">
          Pause
        </button>
        <button onClick={handleStopClick} className="rounded-md bg-gray-300 px-2 py-3">
          Stop
        </button>
      </div>
    </div>
  );
}

```
