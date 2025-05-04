# React Interview Questions for 3 Years Experience

## Core React Concepts

### Question: What is React and what are its key features?

**Answer:** React is a JavaScript library for building user interfaces, particularly single-page applications. It was developed by Facebook and is maintained by Facebook and a community of developers.

Key features include:

- **Component-Based Architecture**: UI is built from encapsulated components that manage their own state
- **Virtual DOM**: Efficient rendering through a lightweight representation of the actual DOM
- **Unidirectional Data Flow**: Data flows in one direction, making the code more predictable and easier to debug
- **JSX**: Syntax extension that allows writing HTML-like code in JavaScript
- **React Native**: Ability to use React for mobile app development

**Example:**

```jsx
// A simple React component
import React from "react";

function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

export default Welcome;
```

## Life Cycle Methods

### Question: Explain React component lifecycle methods and their use cases.

**Answer:** React class components go through a lifecycle of events:

1. **Mounting Phase:**

   - `constructor()`: Initialize state and bind methods
   - `static getDerivedStateFromProps()`: Update state based on props
   - `render()`: Required method that returns JSX
   - `componentDidMount()`: Run after component is mounted to the DOM

2. **Updating Phase:**

   - `static getDerivedStateFromProps()`
   - `shouldComponentUpdate()`: Performance optimization
   - `render()`
   - `getSnapshotBeforeUpdate()`: Capture information before update
   - `componentDidUpdate()`: Run after the update is committed to DOM

3. **Unmounting Phase:**
   - `componentWillUnmount()`: Clean up resources before removal

**Example:**

```jsx
import React, { Component } from "react";

class LifecycleDemo extends Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
    console.log("Constructor: Initialize component");
  }

  static getDerivedStateFromProps(props, state) {
    console.log("getDerivedStateFromProps: Sync state to props if needed");
    return null; // Return null to indicate no state update needed
  }

  componentDidMount() {
    console.log("componentDidMount: Component is now in the DOM");
    // Perfect for API calls, subscriptions, or DOM manipulations
  }

  shouldComponentUpdate(nextProps, nextState) {
    console.log("shouldComponentUpdate: Decide if update is necessary");
    return nextState.count !== this.state.count; // Only update if count changed
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    console.log("getSnapshotBeforeUpdate: Capture pre-update information");
    // E.g., scroll position
    return { scrollPos: window.scrollY };
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    console.log("componentDidUpdate: Component updated in the DOM");
    if (snapshot) {
      console.log(`Scroll position was: ${snapshot.scrollPos}`);
    }
  }

  componentWillUnmount() {
    console.log("componentWillUnmount: Clean up before removal");
    // Clear timers, cancel network requests, unsubscribe from listeners
  }

  handleClick = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    console.log("render: Describe the UI");
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.handleClick}>Increment</button>
      </div>
    );
  }
}

export default LifecycleDemo;
```

## React Hooks

### Question: What are React Hooks and why were they introduced?

**Answer:** React Hooks are functions that let you "hook into" React state and lifecycle features from function components. They were introduced in React 16.8 to:

1. Reuse stateful logic between components without complex patterns like render props or higher-order components
2. Split complex components into smaller functions based on related pieces
3. Use React features without classes, which can be confusing with `this` binding and can't be easily optimized by compilers

**Example:**

```jsx
import React, { useState, useEffect } from "react";

function HooksExample() {
  // useState Hook for state management
  const [count, setCount] = useState(0);

  // useEffect Hook for side effects
  useEffect(() => {
    document.title = `You clicked ${count} times`;

    // Cleanup function (similar to componentWillUnmount)
    return () => {
      document.title = "React App";
    };
  }, [count]); // Only re-run if count changes

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

### Question: Explain useState and its benefits over this.setState in class components.

**Answer:** `useState` is a Hook that lets you add state to functional components. It returns a pair: the current state value and a function to update it.

Benefits over `this.setState`:

- More straightforward syntax without `this` binding issues
- State can be split into multiple state variables
- Each `setState` call is independent and doesn't require merging the state object
- Function updates (`setState(prev => prev + 1)`) are more intuitive

**Example:**

```jsx
import React, { useState } from "react";

// Class component with this.setState
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  increment = () => {
    this.setState((prevState) => ({ count: prevState.count + 1 }));
  };

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}

// Functional component with useState
function HookCounter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount((prevCount) => prevCount + 1)}>Increment</button>
    </div>
  );
}
```

### Question: Explain useEffect and how it replaces lifecycle methods.

**Answer:** `useEffect` is a Hook that lets you perform side effects in function components. It serves the same purpose as `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` combined.

It takes two arguments:

1. A function with your effect code
2. A dependency array to control when the effect runs

**Example:**

```jsx
import React, { useState, useEffect } from "react";

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // componentDidMount + componentDidUpdate (when userId changes)
    setLoading(true);

    const fetchUser = async () => {
      try {
        const response = await fetch(`https://api.example.com/users/${userId}`);
        if (!response.ok) throw new Error("Failed to fetch");
        const userData = await response.json();
        setUser(userData);
        setError(null);
      } catch (err) {
        setError(err.message);
        setUser(null);
      } finally {
        setLoading(false);
      }
    };

    fetchUser();

    // componentWillUnmount
    return () => {
      // Cleanup code (e.g., cancel requests, clear intervals)
      console.log("Cleanup before component unmounts or effect reruns");
    };
  }, [userId]); // Only re-run if userId changes

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>No user found</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>Email: {user.email}</p>
    </div>
  );
}
```

### Question: Explain useContext and how it simplifies working with React Context.

**Answer:** `useContext` is a Hook that lets you subscribe to React context without introducing nesting through Consumer components. It accepts a context object and returns the current context value from the nearest matching Provider.

**Example:**

```jsx
import React, { createContext, useContext, useState } from "react";

// Create a context with default value
const ThemeContext = createContext("light");

function App() {
  const [theme, setTheme] = useState("light");

  return (
    <ThemeContext.Provider value={theme}>
      <div>
        <Header />
        <Main />
        <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>Toggle Theme</button>
      </div>
    </ThemeContext.Provider>
  );
}

// Without useContext (traditional approach)
function HeaderOld() {
  return (
    <ThemeContext.Consumer>
      {(theme) => (
        <header className={`header-${theme}`}>
          <h1>My App</h1>
        </header>
      )}
    </ThemeContext.Consumer>
  );
}

// With useContext
function Header() {
  const theme = useContext(ThemeContext);

  return (
    <header className={`header-${theme}`}>
      <h1>My App</h1>
    </header>
  );
}

function Main() {
  const theme = useContext(ThemeContext);

  return (
    <main className={`main-${theme}`}>
      <p>This is the main content with {theme} theme.</p>
    </main>
  );
}
```

### Question: Explain useReducer and when you would use it instead of useState.

**Answer:** `useReducer` is a Hook for state management based on the reducer pattern from Redux. It's an alternative to `useState` that's preferable when:

1. You have complex state logic involving multiple sub-values
2. The next state depends on the previous state
3. You need to optimize performance for components that trigger deep updates

It takes a reducer function and an initial state, returning the current state and a dispatch function.

**Example:**

```jsx
import React, { useReducer } from "react";

// Reducer function
function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    case "reset":
      return { count: 0 };
    case "set":
      return { count: action.payload };
    default:
      throw new Error(`Unsupported action type: ${action.type}`);
  }
}

function Counter() {
  // useReducer returns [state, dispatch]
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
      <button onClick={() => dispatch({ type: "reset" })}>Reset</button>
      <button onClick={() => dispatch({ type: "set", payload: 10 })}>Set to 10</button>
    </div>
  );
}
```

### Question: Explain useMemo and useCallback. How do they improve performance?

**Answer:** Both Hooks are used for performance optimization:

- `useMemo` memoizes a computed value, recalculating only when dependencies change
- `useCallback` memoizes a function, preventing unnecessary re-renders in child components that receive the function as prop

**Example:**

```jsx
import React, { useState, useMemo, useCallback } from "react";

function ExpensiveCalculation({ list, onItemClick }) {
  const [filter, setFilter] = useState("");

  // useMemo for expensive calculations
  const filteredList = useMemo(() => {
    console.log("Filtering list..."); // Should only run when list or filter changes
    return list.filter((item) => item.name.toLowerCase().includes(filter.toLowerCase()));
  }, [list, filter]); // Dependencies array

  // useCallback for stable function references
  const handleItemClick = useCallback(
    (id) => {
      console.log("Item clicked:", id);
      onItemClick(id);
    },
    [onItemClick]
  ); // Only recreate if onItemClick prop changes

  return (
    <div>
      <input type="text" value={filter} onChange={(e) => setFilter(e.target.value)} placeholder="Filter items..." />
      <ul>
        {filteredList.map((item) => (
          <li key={item.id} onClick={() => handleItemClick(item.id)}>
            {item.name}
          </li>
        ))}
      </ul>
      <p>Total items after filtering: {filteredList.length}</p>
    </div>
  );
}

// Parent component
function App() {
  const [items, setItems] = useState([
    { id: 1, name: "Apple" },
    { id: 2, name: "Banana" },
    { id: 3, name: "Cherry" },
  ]);

  const handleItemClick = useCallback((id) => {
    console.log(`Item ${id} selected in parent`);
    // Some logic here
  }, []);

  return <ExpensiveCalculation list={items} onItemClick={handleItemClick} />;
}
```

### Question: Explain useRef and its common use cases.

**Answer:** `useRef` is a Hook that returns a mutable ref object whose `.current` property is initialized to the passed argument. The object persists for the full lifetime of the component.

Common use cases:

1. Accessing/manipulating DOM elements directly
2. Keeping a mutable value that doesn't cause re-renders when changed
3. Storing previous state or prop values
4. Storing instance variables (like you would use instance properties in classes)

**Example:**

```jsx
import React, { useState, useEffect, useRef } from "react";

function TextInputWithFocusButton() {
  // Create a ref
  const inputRef = useRef(null);

  // Focus the input element
  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focus Input</button>
    </div>
  );
}

function StopWatch() {
  const [time, setTime] = useState(0);
  const [isRunning, setIsRunning] = useState(false);
  // Using ref to store interval ID
  const intervalRef = useRef(null);
  // Using ref to track previous value
  const prevTimeRef = useRef(0);

  useEffect(() => {
    prevTimeRef.current = time;
  }, [time]);

  useEffect(() => {
    if (isRunning) {
      intervalRef.current = setInterval(() => {
        setTime((time) => time + 1);
      }, 1000);
    }

    // Cleanup function
    return () => {
      clearInterval(intervalRef.current);
    };
  }, [isRunning]);

  const handleStartStop = () => {
    setIsRunning(!isRunning);
  };

  const handleReset = () => {
    setTime(0);
    setIsRunning(false);
  };

  return (
    <div>
      <p>
        Current: {time}s (Previous: {prevTimeRef.current}s)
      </p>
      <button onClick={handleStartStop}>{isRunning ? "Stop" : "Start"}</button>
      <button onClick={handleReset}>Reset</button>
    </div>
  );
}
```

### Question: How do React Hooks replace class components? Discuss the practical differences.

**Answer:** React Hooks let you use React features without writing class components:

| Class Feature                     | Hook Replacement                           | Notes                                 |
| --------------------------------- | ------------------------------------------ | ------------------------------------- |
| `constructor`                     | `useState`, function scope                 | Initialize state and bind methods     |
| `this.state`/`this.setState`      | `useState`/`useReducer`                    | More modular state management         |
| `componentDidMount`               | `useEffect(() => {}, [])`                  | Empty dependency array for mount only |
| `componentDidUpdate`              | `useEffect(() => {}, [dep1, dep2])`        | Run when dependencies change          |
| `componentWillUnmount`            | `useEffect(() => { return () => {} }, [])` | Cleanup function                      |
| `shouldComponentUpdate`           | `React.memo`, `useMemo`                    | Optimize rendering                    |
| Class methods                     | Regular functions, `useCallback`           | No `this` binding needed              |
| Static `getDerivedStateFromProps` | `useState` + `useEffect`                   | Update state based on props           |
| Context via `contextType`         | `useContext`                               | Simpler context consumption           |
| Instance variables                | `useRef`                                   | Store mutable values                  |

**Example:**

```jsx
// Class component version
class UserProfileClass extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      user: null,
      loading: true,
      error: null,
    };
  }

  componentDidMount() {
    this.fetchUser();
  }

  componentDidUpdate(prevProps) {
    if (prevProps.userId !== this.props.userId) {
      this.fetchUser();
    }
  }

  componentWillUnmount() {
    // Clean up (e.g., cancel request)
    if (this.controller) {
      this.controller.abort();
    }
  }

  async fetchUser() {
    this.setState({ loading: true });
    this.controller = new AbortController();

    try {
      const response = await fetch(`https://api.example.com/users/${this.props.userId}`, { signal: this.controller.signal });
      if (!response.ok) throw new Error("Failed to fetch");
      const userData = await response.json();
      this.setState({ user: userData, error: null });
    } catch (err) {
      if (err.name !== "AbortError") {
        this.setState({ error: err.message, user: null });
      }
    } finally {
      this.setState({ loading: false });
    }
  }

  render() {
    const { loading, error, user } = this.state;

    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    if (!user) return <div>No user found</div>;

    return (
      <div>
        <h1>{user.name}</h1>
        <p>Email: {user.email}</p>
      </div>
    );
  }
}

// Functional component with hooks version
function UserProfileHooks({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  const controllerRef = useRef(null);

  useEffect(() => {
    // Equivalent to componentDidMount and componentDidUpdate
    async function fetchUser() {
      setLoading(true);

      // Create new abort controller for each request
      if (controllerRef.current) {
        controllerRef.current.abort();
      }
      controllerRef.current = new AbortController();

      try {
        const response = await fetch(`https://api.example.com/users/${userId}`, { signal: controllerRef.current.signal });
        if (!response.ok) throw new Error("Failed to fetch");
        const userData = await response.json();
        setUser(userData);
        setError(null);
      } catch (err) {
        if (err.name !== "AbortError") {
          setError(err.message);
          setUser(null);
        }
      } finally {
        setLoading(false);
      }
    }

    fetchUser();

    // Equivalent to componentWillUnmount
    return () => {
      if (controllerRef.current) {
        controllerRef.current.abort();
      }
    };
  }, [userId]); // Only re-run if userId changes

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>No user found</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>Email: {user.email}</p>
    </div>
  );
}
```

## State Management

### Question: What is prop drilling and what are ways to avoid it?

**Answer:** Prop drilling (also called "threading") is the process of passing props through intermediate components that don't need those props except to pass them down to deeper components. This can make code hard to maintain and refactor.

Ways to avoid prop drilling:

1. React Context API
2. State management libraries (Redux, Zustand, MobX)
3. Component composition
4. Custom hooks to encapsulate shared logic
5. Higher-Order Components (HOCs)

**Example:**

```jsx
// Prop drilling issue
function App() {
  const [user, setUser] = useState({ name: "John", theme: "dark" });

  return (
    <div>
      <Header user={user} />
      <Main user={user} />
      <Footer theme={user.theme} />
    </div>
  );
}

function Header({ user }) {
  // Only needs user.name but receives the whole user object
  return (
    <header>
      <UserInfo name={user.name} />
    </header>
  );
}

function UserInfo({ name }) {
  return <div>Welcome, {name}!</div>;
}

// Solution 1: Context API
const UserContext = createContext();
const ThemeContext = createContext();

function AppWithContext() {
  const [user, setUser] = useState({ name: "John", theme: "dark" });

  return (
    <UserContext.Provider value={{ name: user.name }}>
      <ThemeContext.Provider value={user.theme}>
        <div>
          <HeaderWithContext />
          <MainWithContext />
          <FooterWithContext />
        </div>
      </ThemeContext.Provider>
    </UserContext.Provider>
  );
}

function HeaderWithContext() {
  // No props needed
  return (
    <header>
      <UserInfoWithContext />
    </header>
  );
}

function UserInfoWithContext() {
  // Directly access what's needed
  const { name } = useContext(UserContext);
  return <div>Welcome, {name}!</div>;
}

function FooterWithContext() {
  const theme = useContext(ThemeContext);
  return <footer className={`footer-${theme}`}>Footer</footer>;
}

// Solution 2: Component composition
function AppWithComposition() {
  const [user, setUser] = useState({ name: "John", theme: "dark" });

  const userInfo = <UserInfo name={user.name} />;

  return (
    <div>
      <HeaderWithChildren userInfo={userInfo} />
      <Main user={user} />
      <Footer theme={user.theme} />
    </div>
  );
}

function HeaderWithChildren({ userInfo }) {
  // Receives the pre-composed component
  return <header>{userInfo}</header>;
}
```

### Question: Explain the Context API and when you would use it.

**Answer:** The Context API is React's built-in solution for sharing state across components without prop drilling. It consists of:

- `React.createContext()`: Creates a Context object
- `Context.Provider`: Wraps components that need access to the context value
- `Context.Consumer` or `useContext()`: Consumes the context value

Use Context when:

- Data needs to be accessible by many components at different nesting levels
- Passing props becomes cumbersome
- You have global state like themes, user authentication, or preferences
- You want to avoid prop drilling

Context is not optimized for high-frequency updates, so it might not be ideal for every state management need.

**Example:**

```jsx
import React, { createContext, useContext, useState } from "react";

// Create context with default value
const ThemeContext = createContext({
  theme: "light",
  toggleTheme: () => {},
});

// Custom hook to use theme context
function useTheme() {
  return useContext(ThemeContext);
}

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");

  const toggleTheme = () => {
    setTheme((prevTheme) => (prevTheme === "light" ? "dark" : "light"));
  };

  const value = {
    theme,
    toggleTheme,
  };

  return <ThemeContext.Provider value={value}>{children}</ThemeContext.Provider>;
}

function ThemedButton() {
  const { theme, toggleTheme } = useTheme();

  return (
    <button
      onClick={toggleTheme}
      style={{
        backgroundColor: theme === "light" ? "#fff" : "#333",
        color: theme === "light" ? "#333" : "#fff",
        padding: "10px",
        border: "1px solid #ccc",
      }}
    >
      Toggle to {theme === "light" ? "Dark" : "Light"} Theme
    </button>
  );
}

function ThemedHeader() {
  const { theme } = useTheme();

  return (
    <header
      style={{
        backgroundColor: theme === "light" ? "#f0f0f0" : "#222",
        color: theme === "light" ? "#333" : "#fff",
        padding: "20px",
      }}
    >
      <h1>Themed App</h1>
    </header>
  );
}

function App() {
  return (
    <ThemeProvider>
      <div>
        <ThemedHeader />
        <div style={{ padding: "20px" }}>
          <p>Change the theme using the button below:</p>
          <ThemedButton />
        </div>
      </div>
    </ThemeProvider>
  );
}
```

### Question: Compare Redux and useReducer for state management.

**Answer:** Both Redux and `useReducer` implement the reducer pattern for state management, but with key differences:

| Feature        | Redux                             | useReducer                     |
| -------------- | --------------------------------- | ------------------------------ |
| Scope          | Global app state                  | Component-level state          |
| Middleware     | Supports middleware (thunk, saga) | Requires custom solutions      |
| DevTools       | Robust debugging tools            | Limited debugging              |
| Complexity     | More boilerplate code             | Simpler, less code             |
| Side Effects   | Handled by middleware             | Combine with useEffect         |
| Performance    | Optimized for large applications  | Good for simpler state logic   |
| Persistence    | Built-in solutions available      | Requires custom implementation |
| Learning Curve | Steeper                           | More straightforward           |

**Example:**

```jsx
// Using Redux (simplified example)
// actions.js
export const INCREMENT = "INCREMENT";
export const DECREMENT = "DECREMENT";

export const increment = () => ({ type: INCREMENT });
export const decrement = () => ({ type: DECREMENT });

// reducer.js
const initialState = {
  count: 0,
};

function counterReducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return { ...state, count: state.count + 1 };
    case DECREMENT:
      return { ...state, count: state.count - 1 };
    default:
      return state;
  }
}

// store.js
import { createStore } from "redux";
import counterReducer from "./reducer";

const store = createStore(counterReducer, window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__());

export default store;

// Component using Redux
import React from "react";
import { useDispatch, useSelector } from "react-redux";
import { increment, decrement } from "./actions";

function CounterWithRedux() {
  // Get state from Redux store
  const count = useSelector((state) => state.count);
  // Get dispatch function to send actions
  const dispatch = useDispatch();

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
    </div>
  );
}

// Using useReducer
import React, { useReducer } from "react";

// Same reducer function
function counterReducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return { ...state, count: state.count + 1 };
    case "DECREMENT":
      return { ...state, count: state.count - 1 };
    default:
      return state;
  }
}

function CounterWithUseReducer() {
  // Local component state with reducer
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <div>
      <h2>Count: {state.count}</h2>
      <button onClick={() => dispatch({ type: "INCREMENT" })}>+</button>
      <button onClick={() => dispatch({ type: "DECREMENT" })}>-</button>
    </div>
  );
}

// Combined approach for larger apps
import React, { createContext, useReducer, useContext } from "react";

// Create a context for the state
const CounterContext = createContext();

// Provide the state and dispatch function
function CounterProvider({ children }) {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return <CounterContext.Provider value={{ state, dispatch }}>{children}</CounterContext.Provider>;
}

// Custom hook to use the counter context
function useCounter() {
  const context = useContext(CounterContext);
  if (!context) {
    throw new Error("useCounter must be used within a CounterProvider");
  }
  return context;
}

// Component that uses the context
function CounterDisplay() {
  const { state } = useCounter();
  return <h2>Count: {state.count}</h2>;
}

function CounterButtons() {
  const { dispatch } = useCounter();
  return (
    <div>
      <button onClick={() => dispatch({ type: "INCREMENT" })}>+</button>
      <button onClick={() => dispatch({ type: "DECREMENT" })}>-</button>
    </div>
  );
}

// App that puts it all together
function App() {
  return (
    <CounterProvider>
      <div>
        <CounterDisplay />
        <CounterButtons />
      </div>
    </CounterProvider>
  );
}
```

## Advanced React Concepts

### Question: What are Higher-Order Components (HOCs) and when should you use them?

**Answer:** Higher-Order Components are functions that take a component and return a new component with enhanced functionality. They're used for code reuse, logic abstraction, and cross-cutting concerns like authentication or data fetching.

HOCs follow this pattern: `const EnhancedComponent = higherOrderComponent(WrappedComponent);`

Use HOCs when:

- You need to share common functionality across multiple components
- You want to separate concerns (UI rendering vs behavior)
- You need to add features like logging, access control, or performance tracking

**Example:**

```jsx
// A Higher-Order Component that adds authentication checking
function withAuthentication(WrappedComponent) {
  // Return a new component
  return function WithAuthentication(props) {
    const [isAuthenticated, setIsAuthenticated] = useState(false);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
      // Check authentication status
      const checkAuth = async () => {
        try {
          // Simulating auth check
          const response = await fetch("/api/auth/status");
          const data = await response.json();
          setIsAuthenticated(data.isAuthenticated);
        } catch (error) {
          console.error("Auth check failed:", error);
          setIsAuthenticated(false);
        } finally {
          setLoading(false);
        }
      };

      checkAuth();
    }, []);

    if (loading) {
      return <div>Loading authentication status...</div>;
    }

    if (!isAuthenticated) {
      return <div>Please log in to access this content.</div>;
    }

    // Render the wrapped component with all its props
    return <WrappedComponent {...props} />;
  };
}

// Using the HOC
function UserProfile({ userId }) {
  return (
    <div>
      <h2>User Profile</h2>
      <p>User ID: {userId}</p>
      {/* Profile content */}
    </div>
  );
}

// Create an enhanced component
const AuthenticatedUserProfile = withAuthentication(UserProfile);

// In your app
function App() {
  return <AuthenticatedUserProfile userId="123" />;
}
```

### Question: What are render props and how do they compare to HOCs?

**Answer:** Render props is a technique where a component takes a function prop that returns a React element, allowing the component to share its state or behavior with the function.

Comparison with HOCs:

| Aspect         | Render Props                 | HOCs                                |
| -------------- | ---------------------------- | ----------------------------------- |
| Implementation | Uses a function prop         | Wraps component in another function |
| Prop Collision | No naming conflicts          | Can overwrite props accidentally    |
| Composition    | More explicit                | Can create "wrapper hell"           |
| Debuggability  | Easier to trace              | Can be harder to debug              |
| Reusability    | Clear separation of concerns | Good for cross-cutting concerns     |

**Example:**

```jsx
// Render props component for mouse position tracking
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    function handleMouseMove(event) {
      setPosition({
        x: event.clientX,
        y: event.clientY,
      });
    }

    window.addEventListener("mousemove", handleMouseMove);

    return () => {
      window.removeEventListener("mousemove", handleMouseMove);
    };
  }, []);

  // Call the render prop function with the state
  return render(position);
}

// Using the render props component
function App() {
  return (
    <div>
      <h1>Move the mouse around!</h1>
      <MouseTracker
        render={(position) => (
          <div>
            <p>
              Current mouse position: ({position.x}, {position.y})
            </p>
            <div
              style={{
                width: 10,
                height: 10,
                borderRadius: "50%",
                backgroundColor: "red",
                position: "absolute",
                left: position.x,
                top: position.y,
              }}
            />
          </div>
        )}
      />
    </div>
  );
}

// Alternative "children as function" pattern
function MouseTrackerAlt({ children }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  // Same effect as above...

  // Call the children function with the state
  return children(position);
}

function AppAlt() {
  return (
    <MouseTrackerAlt>
      {(position) => (
        <div>
          <p>
            Current position: ({position.x}, {position.y})
          </p>
        </div>
      )}
    </MouseTrackerAlt>
  );
}
```

### Question: What are React portals and when would you use them?

**Answer:** React portals provide a way to render children into a DOM node that exists outside the DOM hierarchy of the parent component. This is useful for components like modals, tooltips, or floating menus that need to visually "break out" of their container.

**Example:**

```jsx
import React, { useState } from "react";
import { createPortal } from "react-dom";

function Modal({ isOpen, onClose, children }) {
  if (!isOpen) return null;

  // Create a portal to a DOM node outside the regular hierarchy
  return createPortal(
    <div className="modal-overlay">
      <div className="modal">
        <button className="modal-close" onClick={onClose}>
          ×
        </button>
        <div className="modal-content">{children}</div>
      </div>
    </div>,
    // Target DOM node (typically at the end of the body)
    document.getElementById("modal-root")
  );
}

function App() {
  const [showModal, setShowModal] = useState(false);

  return (
    <div className="app">
      <h1>React Portals Example</h1>
      <p>This content might be overflowed or have z-index issues.</p>

      <button onClick={() => setShowModal(true)}>Open Modal</button>

      <Modal isOpen={showModal} onClose={() => setShowModal(false)}>
        <h2>This is a modal</h2>
        <p>It's rendered outside the DOM hierarchy via a Portal!</p>
      </Modal>
    </div>
  );
}

// In your HTML file:
// <div id="root"></div>
// <div id="modal-root"></div>
```

Use portals when:

1. Dealing with parent components with `overflow: hidden` or `z-index` constraints
2. Creating modals, dialogs, tooltips, or popovers
3. Working with third-party DOM libraries
4. Creating floating elements that need to appear above other content

### Question: How does React handle error boundaries? Why are they useful?

**Answer:** Error boundaries are React components that catch JavaScript errors in their child component tree, log those errors, and display a fallback UI. They prevent the entire application from crashing when an error occurs in a part of the UI.

Key points:

- Error boundaries catch errors during rendering, in lifecycle methods, and in constructors
- They do NOT catch errors in event handlers, asynchronous code, or server-side rendering
- Since React 16, uncaught errors will unmount the entire React component tree

**Example:**

```jsx
import React, { Component } from "react";

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null, errorInfo: null };
  }

  static getDerivedStateFromError(error) {
    // Update state so next render shows fallback UI
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // Log the error to an error reporting service
    console.error("Error caught by boundary:", error, errorInfo);
    this.setState({ error, errorInfo });

    // You could also log to a service like Sentry
    // logErrorToService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // Render fallback UI
      return (
        <div className="error-container">
          <h2>Something went wrong</h2>
          {this.props.fallback || <p>We're working on fixing this issue.</p>}
          {process.env.NODE_ENV !== "production" && (
            <details>
              <summary>Error Details</summary>
              <p>{this.state.error && this.state.error.toString()}</p>
              <p>Component Stack:</p>
              <pre>{this.state.errorInfo && this.state.errorInfo.componentStack}</pre>
            </details>
          )}
        </div>
      );
    }

    // If no error, render children normally
    return this.props.children;
  }
}

// Component that will cause an error
function BuggyCounter() {
  const [count, setCount] = useState(0);

  // This will cause an error when count reaches 5
  if (count === 5) {
    throw new Error("I crashed when count reached 5!");
  }

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// App with error boundaries
function App() {
  return (
    <div>
      <h1>Error Boundary Example</h1>

      <ErrorBoundary>
        <BuggyCounter />
      </ErrorBoundary>

      <hr />
      <p>This part of the UI won't crash even if the counter above does</p>

      <ErrorBoundary fallback={<p>Custom fallback UI for this section</p>}>
        <h2>Another Component</h2>
        <p>This has its own error boundary with custom fallback</p>
      </ErrorBoundary>
    </div>
  );
}
```

### Question: What are React's Suspense and lazy loading features?

**Answer:** React Suspense and lazy loading are features that enable code-splitting and component loading on demand, which improves performance by reducing the initial bundle size.

- `React.lazy()`: Lets you define a component that is loaded dynamically
- `<Suspense>`: Displays a fallback content while waiting for lazy components to load

**Example:**

```jsx
import React, { Suspense, lazy, useState } from "react";

// Instead of importing normally:
// import ExpensiveComponent from './ExpensiveComponent';

// Use lazy loading
const ExpensiveComponent = lazy(() => import("./ExpensiveComponent"));
const DataVisualizer = lazy(() => import("./DataVisualizer"));

function App() {
  const [showComponent, setShowComponent] = useState(false);
  const [activeTab, setActiveTab] = useState("home");

  return (
    <div>
      <h1>React Suspense Example</h1>

      {/* Navigation */}
      <nav>
        <button onClick={() => setActiveTab("home")}>Home</button>
        <button onClick={() => setActiveTab("visualizer")}>Data Visualizer</button>
        <button onClick={() => setShowComponent(true)}>Load Expensive Component</button>
      </nav>

      {/* Content area with Suspense */}
      <Suspense fallback={<div>Loading tab content...</div>}>
        {activeTab === "home" && <div>Home Content</div>}
        {activeTab === "visualizer" && <DataVisualizer />}
      </Suspense>

      {/* Conditionally loaded component with Suspense */}
      {showComponent && (
        <Suspense fallback={<div>Loading expensive component...</div>}>
          <ExpensiveComponent />
        </Suspense>
      )}
    </div>
  );
}

// You can also nest Suspense components
function NestedExample() {
  return (
    <Suspense fallback={<div>Loading outer content...</div>}>
      <div>
        <h2>Main Content</h2>
        <Suspense fallback={<div>Loading nested content...</div>}>
          <ExpensiveComponent />
        </Suspense>
      </div>
    </Suspense>
  );
}
```

Benefits:

1. Smaller initial bundle size, leading to faster page loads
2. Better user experience with fallback UI during loading
3. Loading code only when needed (on demand)
4. Route-based code splitting for larger applications

### Question: What is the useTransition hook and how does it work?

**Answer:** `useTransition` is a React Hook introduced in React 18 that helps manage state transitions by marking certain updates as non-urgent. It allows you to keep the UI responsive during heavy state updates by prioritizing more important updates.

The hook returns a tuple with:

1. A boolean `isPending` that indicates whether a transition is in progress
2. A `startTransition` function to wrap state updates that can be deferred

**Example:**

```jsx
import React, { useState, useTransition } from "react";

function FilterableList() {
  const [query, setQuery] = useState("");
  const [deferredQuery, setDeferredQuery] = useState("");
  const [isPending, startTransition] = useTransition();

  // Generate a large list for demonstration
  const allItems = Array.from({ length: 10000 }, (_, i) => `Item ${i + 1}`);

  // Filter the list (computationally expensive operation)
  const filteredItems = allItems.filter((item) => item.toLowerCase().includes(deferredQuery.toLowerCase()));

  function handleChange(e) {
    // Update the input immediately (urgent update)
    setQuery(e.target.value);

    // Defer the filtering operation (non-urgent update)
    startTransition(() => {
      setDeferredQuery(e.target.value);
    });
  }

  return (
    <div>
      <h2>Filterable List with useTransition</h2>
      <input type="text" value={query} onChange={handleChange} placeholder="Type to filter..." />

      {isPending && <div>Loading results...</div>}

      <ul>
        {filteredItems.slice(0, 100).map((item) => (
          <li key={item}>{item}</li>
        ))}
        {filteredItems.length > 100 && <li>...and {filteredItems.length - 100} more items</li>}
      </ul>
    </div>
  );
}
```

Use `useTransition` when:

1. You have UI updates that are computationally expensive
2. You want to maintain responsive input fields during heavy operations
3. You need to prioritize user interactions over background state updates
4. You're implementing search-as-you-type functionality with large datasets

## Performance Optimization

### Question: What are the common techniques to optimize React application performance?

**Answer:** React applications can be optimized using various techniques:

1. **Component Optimization:**

   - Use React.memo for functional components
   - Implement shouldComponentUpdate for class components
   - Use PureComponent for simple comparison-based optimization

2. **State Management:**

   - Keep state as local as possible
   - Use appropriate state management tools (Context API, Redux, etc.)
   - Normalize complex state structures

3. **Rendering Optimization:**

   - Virtual list libraries for long lists (react-window, react-virtualized)
   - Code-splitting with React.lazy and Suspense
   - Avoid unnecessary re-renders with proper key usage

4. **Hooks Optimization:**

   - Memoize callbacks with useCallback
   - Memoize computed values with useMemo
   - Use useTransition for expensive updates

5. **Bundle Optimization:**

   - Tree shaking to eliminate dead code
   - Dynamic imports for code splitting
   - Lazy loading of components and routes

6. **Other Techniques:**
   - Debouncing and throttling for frequent events
   - Image optimization and lazy loading
   - Web Workers for CPU-intensive tasks

**Example:**

```jsx
import React, { useState, useCallback, useMemo, memo } from "react";

// Memoized child component
const ExpensiveChild = memo(function ExpensiveChild({ data, onClick }) {
  console.log("ExpensiveChild render");

  // Expensive calculation
  const processedData = useMemo(() => {
    console.log("Processing data...");
    return data.map((item) => item * 2);
  }, [data]); // Only recalculate when data changes

  return (
    <div>
      <h3>Expensive Component</h3>
      <ul>
        {processedData.map((item) => (
          <li key={item} onClick={() => onClick(item)}>
            {item}
          </li>
        ))}
      </ul>
    </div>
  );
});

function OptimizedApp() {
  const [count, setCount] = useState(0);
  const [data, setData] = useState([1, 2, 3, 4, 5]);

  // Memoized callback
  const handleItemClick = useCallback((item) => {
    console.log("Item clicked:", item);
    // Do something with the item
  }, []); // Dependencies array - empty means this function never changes

  // Memoized data transformation
  const expensiveComputation = useMemo(() => {
    console.log("Performing expensive computation...");
    return count * count * count;
  }, [count]);

  return (
    <div>
      <h2>Optimized React Component</h2>
      <p>Count: {count}</p>
      <p>Expensive result: {expensiveComputation}</p>

      <button onClick={() => setCount(count + 1)}>Increment Count</button>

      <button onClick={() => setData([...data, Math.floor(Math.random() * 100)])}>Add Random Number</button>

      {/* Child component with memoized props */}
      <ExpensiveChild data={data} onClick={handleItemClick} />
    </div>
  );
}
```

### Question: Explain React.memo and when you should use it.

**Answer:** `React.memo` is a higher-order component that memoizes a functional component, preventing unnecessary re-renders when its props have not changed.

It performs a shallow comparison of props by default, but you can provide a custom comparison function as a second argument.

**Example:**

```jsx
import React, { useState, memo } from "react";

// Without memoization
function RegularComponent({ name, age }) {
  console.log(`RegularComponent rendering with name: ${name}, age: ${age}`);
  return (
    <div className="component">
      <h3>Regular Component</h3>
      <p>Name: {name}</p>
      <p>Age: {age}</p>
    </div>
  );
}

// With memoization
const MemoizedComponent = memo(function MemoizedComponent({ name, age }) {
  console.log(`MemoizedComponent rendering with name: ${name}, age: ${age}`);
  return (
    <div className="component">
      <h3>Memoized Component</h3>
      <p>Name: {name}</p>
      <p>Age: {age}</p>
    </div>
  );
});

// With custom comparison function
const CustomMemoComponent = memo(
  function CustomMemoComponent({ user, onEdit }) {
    console.log(`CustomMemoComponent rendering with user: ${user.name}`);
    return (
      <div className="component">
        <h3>Custom Comparison Component</h3>
        <p>Name: {user.name}</p>
        <p>Age: {user.age}</p>
        <button onClick={() => onEdit(user.id)}>Edit</button>
      </div>
    );
  },
  (prevProps, nextProps) => {
    // Only re-render if user ID changes
    return prevProps.user.id === nextProps.user.id;
  }
);

function ParentComponent() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("John");
  const [user, setUser] = useState({ id: 1, name: "Alice", age: 30 });

  // This function is recreated on every render
  const handleEdit = (id) => {
    console.log(`Edit user with id: ${id}`);
  };

  return (
    <div>
      <h2>React.memo Example</h2>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment Count</button>
      <button onClick={() => setName(name === "John" ? "Jane" : "John")}>Toggle Name</button>
      <button onClick={() => setUser({ ...user, age: user.age + 1 })}>Increment User Age</button>

      <div className="components-container">
        <RegularComponent name={name} age={30} />
        <MemoizedComponent name={name} age={30} />
        <CustomMemoComponent user={user} onEdit={handleEdit} />
      </div>
    </div>
  );
}
```

When to use React.memo:

1. For pure functional components that render the same result given the same props
2. For components that render often with the same props
3. For medium to large components where re-rendering is expensive
4. In component trees where a parent component updates frequently but child props don't change

When NOT to use React.memo:

1. For simple components where memoization overhead exceeds rendering cost
2. For components whose props change frequently
3. When most prop changes should cause re-rendering anyway

### Question: How do React keys work and why are they important for performance?

**Answer:** React keys are special attributes used to help React identify which items have changed, been added, or been removed in a list. They play a crucial role in React's reconciliation process.

Keys should be:

- Unique among siblings
- Stable across renders
- Preferably from your data (like IDs)

**Example:**

```jsx
import React, { useState } from "react";

// Component with inefficient key strategy
function IneffectiveList() {
  const [items, setItems] = useState([
    { id: 1, name: "Apple" },
    { id: 2, name: "Banana" },
    { id: 3, name: "Cherry" },
  ]);

  const addItem = () => {
    const newItem = {
      id: Math.floor(Math.random() * 1000),
      name: "New Item " + Date.now().toString().slice(-4),
    };
    setItems([newItem, ...items]);
  };

  return (
    <div>
      <h3>Ineffective List (using index as key)</h3>
      <button onClick={addItem}>Add Item to Top</button>
      <ul>
        {items.map((item, index) => (
          <li key={index}>
            {" "}
            {/* BAD: Using index as key */}
            <input type="text" defaultValue={item.name} />
          </li>
        ))}
      </ul>
    </div>
  );
}

// Component with proper key strategy
function EffectiveList() {
  const [items, setItems] = useState([
    { id: 1, name: "Apple" },
    { id: 2, name: "Banana" },
    { id: 3, name: "Cherry" },
  ]);

  const addItem = () => {
    const newItem = {
      id: Math.floor(Math.random() * 1000),
      name: "New Item " + Date.now().toString().slice(-4),
    };
    setItems([newItem, ...items]);
  };

  return (
    <div>
      <h3>Effective List (using unique ID as key)</h3>
      <button onClick={addItem}>Add Item to Top</button>
      <ul>
        {items.map((item) => (
          <li key={item.id}>
            {" "}
            {/* GOOD: Using unique ID as key */}
            <input type="text" defaultValue={item.name} />
          </li>
        ))}
      </ul>
    </div>
  );
}

function KeysExample() {
  return (
    <div>
      <h2>React Keys Example</h2>
      <div className="lists-container">
        <IneffectiveList />
        <EffectiveList />
      </div>
      <div className="explanation">
        <h3>Why keys matter:</h3>
        <p>Try adding items and typing in the inputs. With index-based keys, your input focus and values get mixed up when you add items at the beginning.</p>
        <p>With stable keys, React correctly preserves component state.</p>
      </div>
    </div>
  );
}
```

Performance implications of keys:

1. **Efficient Updates:** Proper keys help React identify which elements can be reused and which need to be recreated
2. **Preserved State:** Components maintain their state when moved within a list
3. **Reduced DOM Operations:** Fewer DOM manipulations when list items change
4. **Deterministic Rendering:** Predictable behavior during reconciliation

Common key mistakes:

1. Using index as key in dynamic lists (especially for lists that can be reordered or filtered)
2. Using non-stable values like random numbers generated during render
3. Using non-unique values like duplicate strings

## Testing in React

### Question: What testing frameworks and tools are commonly used with React applications?

**Answer:** Common testing frameworks and tools for React applications include:

1. **Test Runners and Frameworks:**

   - Jest (most popular, included with Create React App)
   - Mocha
   - Jasmine

2. **Testing Libraries:**

   - React Testing Library (recommended by React team)
   - Enzyme (older, less maintained now)
   - Cypress for end-to-end testing
   - Playwright for cross-browser testing

3. **Utilities:**

   - Mock Service Worker (MSW) for API mocking
   - user-event for simulating user events
   - jest-dom for custom DOM matchers

4. **Snapshot Testing:**
   - Jest's snapshot functionality
   - Storybook with Storyshots

**Example:**

```jsx
// Component to test
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p data-testid="count-value">Count: {count}</p>
      <button data-testid="increment-button" onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}

// Jest + React Testing Library test
import { render, screen, fireEvent } from "@testing-library/react";
import Counter from "./Counter";

describe("Counter Component", () => {
  test("renders initial count of 0", () => {
    render(<Counter />);
    const countElement = screen.getByTestId("count-value");
    expect(countElement).toHaveTextContent("Count: 0");
  });

  test("increments count when button is clicked", () => {
    render(<Counter />);
    const button = screen.getByTestId("increment-button");
    const countElement = screen.getByTestId("count-value");

    expect(countElement).toHaveTextContent("Count: 0");

    fireEvent.click(button);
    expect(countElement).toHaveTextContent("Count: 1");

    fireEvent.click(button);
    expect(countElement).toHaveTextContent("Count: 2");
  });
});

// Snapshot test
test("Counter matches snapshot", () => {
  const { container } = render(<Counter />);
  expect(container).toMatchSnapshot();
});

// Mock API call with Mock Service Worker
import { rest } from "msw";
import { setupServer } from "msw/node";

const server = setupServer(
  rest.get("/api/count", (req, res, ctx) => {
    return res(ctx.json({ initialCount: 5 }));
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

test("loads initial count from API", async () => {
  render(<CounterWithAPI />);

  // Wait for API call to complete
  const countElement = await screen.findByText("Count: 5");
  expect(countElement).toBeInTheDocument();
});
```
