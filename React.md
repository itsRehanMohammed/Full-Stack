# React Topics

## React Hooks

React Hooks are functions that let you use state and other React features without writing classes. They were introduced in React 16.8 to solve several problems in React:

- Difficulty reusing stateful logic between components
- Complex components becoming hard to understand
- Classes confusing both people and machines

### useState

The most basic Hook that lets you add React state to functional components.

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

### useEffect

Lets you perform side effects in function components. It serves the same purpose as `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` in React classes.

```jsx
import { useState, useEffect } from "react";

function ExampleWithEffect() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;

    // Cleanup function (equivalent to componentWillUnmount)
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

### useContext

Accepts a context object and returns the current context value. Helps avoid prop drilling.

```jsx
import { createContext, useContext, useState } from "react";

// Create a context
const ThemeContext = createContext("light");

function App() {
  const [theme, setTheme] = useState("light");

  return (
    <ThemeContext.Provider value={theme}>
      <div>
        <ThemedButton />
        <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
          Toggle Theme
        </button>
      </div>
    </ThemeContext.Provider>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);

  return (
    <button
      style={{
        background: theme === "light" ? "#fff" : "#333",
        color: theme === "light" ? "#333" : "#fff",
      }}
    >
      I'm styled based on theme context!
    </button>
  );
}
```

### useReducer

An alternative to useState for complex state logic. Similar to Redux's pattern.

```jsx
import { useReducer } from "react";

// Reducer function
function counterReducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <div>
      Count: {state.count}
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
    </div>
  );
}
```

### useMemo

Lets you memoize expensive calculations so they only recompute when dependencies change.

```jsx
import { useState, useMemo } from "react";

function ExpensiveCalculation({ list }) {
  const [count, setCount] = useState(0);

  // This calculation will only run when list changes, not on every render
  const sortedList = useMemo(() => {
    console.log("Sorting list...");
    return [...list].sort();
  }, [list]);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Clicked {count} times</button>
      <ul>
        {sortedList.map((item) => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
```

### useCallback

Returns a memoized version of a callback function that only changes if dependencies change. Useful for optimizing child component renders.

```jsx
import { useState, useCallback } from "react";

function ParentComponent() {
  const [count, setCount] = useState(0);
  const [todos, setTodos] = useState([]);

  // Without useCallback, this function would be recreated on every render
  const addTodo = useCallback(() => {
    setTodos((prev) => [...prev, `New Todo ${prev.length + 1}`]);
  }, []);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
      <ChildComponent todos={todos} addTodo={addTodo} />
    </div>
  );
}

function ChildComponent({ todos, addTodo }) {
  console.log("Child rendered");

  return (
    <div>
      <button onClick={addTodo}>Add Todo</button>
      <ul>
        {todos.map((todo, index) => (
          <li key={index}>{todo}</li>
        ))}
      </ul>
    </div>
  );
}
```

### useRef

Provides a way to access DOM nodes or persist values without causing re-renders.

```jsx
import { useRef } from "react";

function TextInputWithFocusButton() {
  const inputRef = useRef(null);

  function handleClick() {
    // Directly access the DOM element
    inputRef.current.focus();
  }

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={handleClick}>Focus the input</button>
    </div>
  );
}
```

## Hooks vs Class Components

Hooks replace class components by providing:

- Cleaner, more concise code
- Better reuse of stateful logic
- Less boilerplate
- No need for `this` keyword
- Avoidance of lifecycle method confusion

### Class Component Example:

```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
    this.increment = this.increment.bind(this);
  }

  increment() {
    this.setState({ count: this.state.count + 1 });
  }

  componentDidMount() {
    document.title = `Count: ${this.state.count}`;
  }

  componentDidUpdate() {
    document.title = `Count: ${this.state.count}`;
  }

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}
```

### Hooks Equivalent:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

## Prop Drilling

Prop drilling occurs when props need to be passed through multiple component layers to reach a deeply nested component.

### Example of Prop Drilling:

```jsx
function App() {
  const [user, setUser] = useState({ name: "John" });
  return <Layout user={user} />;
}

function Layout({ user }) {
  return (
    <div>
      <Header user={user} />
      <Content />
    </div>
  );
}

function Header({ user }) {
  return <UserInfo user={user} />;
}

function UserInfo({ user }) {
  return <h1>Hello, {user.name}</h1>;
}
```

Solution: Use Context API or state management libraries.

## Context API

Provides a way to share values between components without explicitly passing props.

```jsx
import { createContext, useContext, useState } from "react";

// Create context
const UserContext = createContext();

function App() {
  const [user, setUser] = useState({ name: "John" });

  return (
    <UserContext.Provider value={user}>
      <Layout />
    </UserContext.Provider>
  );
}

function Layout() {
  return (
    <div>
      <Header />
      <Content />
    </div>
  );
}

function Header() {
  return <UserInfo />;
}

function UserInfo() {
  const user = useContext(UserContext);
  return <h1>Hello, {user.name}</h1>;
}
```

## State Management

### Redux

A predictable state container for JavaScript apps with a single immutable state tree.

```jsx
// Action Types
const INCREMENT = "INCREMENT";
const DECREMENT = "DECREMENT";

// Action Creators
const increment = () => ({ type: INCREMENT });
const decrement = () => ({ type: DECREMENT });

// Reducer
const counterReducer = (state = { count: 0 }, action) => {
  switch (action.type) {
    case INCREMENT:
      return { count: state.count + 1 };
    case DECREMENT:
      return { count: state.count - 1 };
    default:
      return state;
  }
};

// Store
import { createStore } from "redux";
const store = createStore(counterReducer);

// Component with Redux
import { Provider, useSelector, useDispatch } from "react-redux";

function App() {
  return (
    <Provider store={store}>
      <Counter />
    </Provider>
  );
}

function Counter() {
  const count = useSelector((state) => state.count);
  const dispatch = useDispatch();

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
    </div>
  );
}
```

## Higher Order Component (HOC)

A pattern where a function takes a component and returns a new enhanced component.

```jsx
function withLogger(WrappedComponent) {
  return function WithLogger(props) {
    useEffect(() => {
      console.log("Component rendered with props:", props);
    }, [props]);

    return <WrappedComponent {...props} />;
  };
}

// Usage
function Button({ onClick, children }) {
  return <button onClick={onClick}>{children}</button>;
}

const ButtonWithLogging = withLogger(Button);

function App() {
  return (
    <ButtonWithLogging onClick={() => alert("Clicked!")}>
      Click Me
    </ButtonWithLogging>
  );
}
```

## Virtual DOM vs Real DOM

- **Real DOM**: The actual browser DOM that's expensive to manipulate
- **Virtual DOM**: A lightweight copy of the real DOM in memory that React uses to track changes

React's process:

1. Changes are made to Virtual DOM
2. React compares old and new Virtual DOM (diffing)
3. Only changed elements in the real DOM are updated (reconciliation)

## Reconciliation

The algorithm React uses to diff one tree with another to determine which parts need to be changed.

Key aspects:

- Elements of different types produce different trees
- Developer-supplied keys help identify which child elements remain stable across renders
- React updates from top-down, one-pass algorithm (O(n) complexity)

## State Updates in React

When you call `setState`:

1. React schedules an update (doesn't apply it immediately)
2. Multiple state updates may be batched for performance
3. React merges the state changes and performs a reconciliation
4. The component re-renders

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    // This won't work as expected
    setCount(count + 1);
    setCount(count + 1); // Still references original count

    // Functional updates fix this
    setCount((prev) => prev + 1);
    setCount((prev) => prev + 1); // Now increments twice
  }

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleClick}>Increment Twice</button>
    </div>
  );
}
```

## Keys in React Lists

Keys help React identify which items have changed, been added, or removed in lists.

```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {/* Bad: Using index as key */}
      {todos.map((todo, index) => (
        <li key={index}>{todo.text}</li>
      ))}

      {/* Good: Using unique ID */}
      {todos.map((todo) => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```

## useEffect vs useLayoutEffect

- **useEffect**: Runs asynchronously after render is committed to screen
- **useLayoutEffect**: Runs synchronously after DOM mutations but before paint

```jsx
function ExampleComponent() {
  const [width, setWidth] = useState(0);
  const ref = useRef(null);

  // Runs after paint, may cause flicker
  useEffect(() => {
    if (ref.current) {
      setWidth(ref.current.getBoundingClientRect().width);
    }
  }, []);

  // Runs before paint, prevents flicker
  useLayoutEffect(() => {
    if (ref.current) {
      setWidth(ref.current.getBoundingClientRect().width);
    }
  }, []);

  return <div ref={ref}>Width: {width}px</div>;
}
```

## React Portals

Render children into a DOM node outside parent hierarchy.

```jsx
import { createPortal } from "react-dom";

function Modal({ isOpen, onClose, children }) {
  if (!isOpen) return null;

  return createPortal(
    <div className="modal-overlay">
      <div className="modal">
        <button onClick={onClose}>✕</button>
        {children}
      </div>
    </div>,
    document.getElementById("modal-root")
  );
}
```

## Error Boundaries

Catch JavaScript errors in child components and display fallback UI.

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error("Error caught:", error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

## Performance Optimization

```jsx
// Memoize components
const MemoizedComponent = React.memo(function MyComponent(props) {
  return <div>{props.value}</div>;
});

// Code splitting
const LazyComponent = lazy(() => import("./LazyComponent"));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <LazyComponent />
    </Suspense>
  );
}
```

## Testing

```jsx
import { render, screen, fireEvent } from "@testing-library/react";
import Counter from "./Counter";

test("counter increments when clicked", () => {
  render(<Counter />);
  expect(screen.getByText(/count: 0/i)).toBeInTheDocument();
  fireEvent.click(screen.getByRole("button", { name: /increment/i }));
  expect(screen.getByText(/count: 1/i)).toBeInTheDocument();
});
```

This comprehensive guide covers all major React concepts with proper Markdown formatting and syntax highlighting for code blocks. The structure is organized logically from basic to advanced topics, with clear headings and subheadings.
