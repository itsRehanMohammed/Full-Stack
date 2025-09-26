# JavaScript Interview Questions for 3 Years Experience

## Hoisting

**Question:** Explain hoisting in JavaScript with an example.

**Answer:** Hoisting is JavaScript's default behavior of moving declarations to the top of their scope during compilation. Only declarations are hoisted, not initializations.

**Example:**

```javascript
console.log(x); // undefined (not ReferenceError)
var x = 5;
console.log(x); // 5

// What happens behind the scenes:
// var x;
// console.log(x);
// x = 5;
// console.log(x);

// Function declarations are fully hoisted
sayHello(); // "Hello!" - This works
function sayHello() {
  console.log("Hello!");
}

// Function expressions are not hoisted
sayHi(); // TypeError: sayHi is not a function
var sayHi = function () {
  console.log("Hi!");
};
```

## Closure

**Question:** What is a closure in JavaScript and what are its practical uses?

**Answer:** A closure is a function that remembers and can access variables from its outer scope even after that scope has finished executing. Closures are commonly used for data privacy, function factories, and maintaining state.

**Example:**

```javascript
function createCounter() {
  let count = 0; // Private variable

  return {
    increment: function () {
      count++;
      return count;
    },
    getCount: function () {
      return count;
    },
  };
}

const counter = createCounter();
console.log(counter.getCount()); // 0
console.log(counter.increment()); // 1
console.log(counter.increment()); // 2

// The count variable is not accessible directly
console.log(count); // ReferenceError: count is not defined
```

## Scope (var, let, const)

**Question:** Explain the differences between var, let, and const with examples.

**Answer:**

- `var`: Function-scoped, can be redeclared and updated, hoisted to the top with initial value undefined
- `let`: Block-scoped, can be updated but not redeclared within same scope, hoisted but not initialized
- `const`: Block-scoped, cannot be updated or redeclared, must be initialized at declaration, hoisted but not initialized

**Example:**

```javascript
// var example
function varExample() {
  var x = 1;
  if (true) {
    var x = 2; // Same variable, redeclared
    console.log(x); // 2
  }
  console.log(x); // 2 - value was changed
}

// let example
function letExample() {
  let y = 1;
  if (true) {
    let y = 2; // Different variable in different scope
    console.log(y); // 2
  }
  console.log(y); // 1 - outer scope value unchanged
}

// const example
const PI = 3.14;
PI = 3.15; // TypeError: Assignment to constant variable

// Objects with const
const person = { name: "John" };
person.name = "Jane"; // Works - object properties can be modified
person = { name: "Jane" }; // TypeError - cannot reassign constant
```

## Undefined VS NULL VS Not Defined

**Question:** What's the difference between undefined, null, and not defined?

**Answer:**

- `undefined`: Variable has been declared but not assigned a value
- `null`: Intentional absence of value (assigned by developer)
- `not defined`: Variable that has not been declared (ReferenceError)

**Example:**

```javascript
let a; // Declared but not initialized
console.log(a); // undefined

let b = null; // Explicitly set to null
console.log(b); // null

console.log(c); // ReferenceError: c is not defined

console.log(typeof undefined); // "undefined"
console.log(typeof null); // "object" (JavaScript quirk/bug)
console.log(undefined == null); // true
console.log(undefined === null); // false
```

## Promise, async/await

**Question:** How do Promises work, and how does async/await simplify asynchronous code?

**Answer:** Promises represent the eventual completion or failure of an asynchronous operation and its resulting value. Async/await is syntactic sugar built on top of Promises that allows writing asynchronous code in a more readable, synchronous-like manner.

**Example:**

```javascript
// Promise example
function fetchData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const success = true;
      if (success) {
        resolve("Data fetched successfully");
      } else {
        reject("Error fetching data");
      }
    }, 1000);
  });
}

// Using Promise syntax
fetchData()
  .then((data) => console.log(data))
  .catch((error) => console.error(error));

// Using async/await syntax
async function getData() {
  try {
    const result = await fetchData();
    console.log(result);
  } catch (error) {
    console.error(error);
  }
}

getData();
```

## Debounce

**Question:** What is debounce and how would you implement it?

**Answer:** Debounce is a technique to limit the rate at which a function can fire. It ensures that a function won't be executed until after a certain amount of time has passed since it was last called. This is useful for performance optimization, especially for expensive operations triggered by frequent events (like resize, scroll, input).

**Example:**

```javascript
function debounce(func, delay) {
  let timeoutId;

  return function (...args) {
    clearTimeout(timeoutId);

    timeoutId = setTimeout(() => {
      func.apply(this, args);
    }, delay);
  };
}

// Usage
const handleSearch = debounce(function (event) {
  console.log("Searching for:", event.target.value);
  // API call or expensive operation here
}, 500);

// In a real app
document.getElementById("search").addEventListener("input", handleSearch);
```

## Throttle

**Question:** What is throttling and how would you implement it?

**Answer:** Throttling is a technique that ensures a function is called at most once in a specified time period. Unlike debounce, throttle will execute the function regularly at the specified rate, rather than waiting for a period of inactivity.

**Example:**

```javascript
function throttle(func, limit) {
  let inThrottle = false;

  return function (...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;

      setTimeout(() => {
        inThrottle = false;
      }, limit);
    }
  };
}

// Usage
const handleScroll = throttle(function () {
  console.log("Window scrolled");
  // Expensive calculation based on scroll position
}, 300);

// In a real app
window.addEventListener("scroll", handleScroll);
```

## Shallow VS Deep Copy

**Question:** What's the difference between shallow and deep copy in JavaScript?

**Answer:**

- **Shallow copy**: Creates a new object/array, but nested objects/arrays are still references to the original.
- **Deep copy**: Creates a completely independent copy including all nested objects.

**Example:**

```javascript
// Original object
const originalObject = {
  name: "John",
  age: 30,
  address: {
    city: "New York",
    country: "USA",
  },
};

// Shallow copy methods
const shallowCopy1 = Object.assign({}, originalObject);
const shallowCopy2 = { ...originalObject };

shallowCopy1.name = "Jane"; // This changes only the copy
shallowCopy1.address.city = "Boston"; // This changes BOTH objects
console.log(originalObject.address.city); // "Boston"

// Deep copy methods
const deepCopy1 = JSON.parse(JSON.stringify(originalObject)); // Simple but has limitations
// Better deep copy with a library like lodash
// const deepCopy2 = _.cloneDeep(originalObject);

deepCopy1.address.city = "Chicago"; // Only affects the copy
console.log(originalObject.address.city); // Still "Boston"
```

## Currying

**Question:** What is currying in JavaScript and what are its benefits?

**Answer:** Currying is a technique of transforming a function that takes multiple arguments into a sequence of functions that each take a single argument. Benefits include partial application of functions, better code reuse, and more readable code composition.

**Example:**

```javascript
// Regular function
function add(x, y, z) {
  return x + y + z;
}

// Curried version
function curriedAdd(x) {
  return function (y) {
    return function (z) {
      return x + y + z;
    };
  };
}

// Usage
console.log(add(1, 2, 3)); // 6
console.log(curriedAdd(1)(2)(3)); // 6

// Practical example - creating specialized functions
const addFive = curriedAdd(5);
const addFiveAndTen = addFive(10);
console.log(addFiveAndTen(15)); // 30

// ES6 arrow function syntax for currying
const arrowCurriedAdd = (x) => (y) => (z) => x + y + z;
```

## SetTimeout

**Question:** How does setTimeout work and what are common pitfalls?

**Answer:** setTimeout schedules a function to run after a specified delay (in milliseconds). Common pitfalls include scope issues with `this`, non-guaranteed timing precision, and how it works in the event loop.

**Example:**

```javascript
// Basic usage
setTimeout(() => {
  console.log("This runs after 1 second");
}, 1000);

// Issues with 'this'
const user = {
  name: "John",
  greet() {
    console.log(`Hello, I'm ${this.name}`);
  },
  greetLater() {
    // 'this' will be the global object, not 'user'
    setTimeout(function () {
      console.log(`Hello, I'm ${this.name}`);
    }, 1000);
  },
  greetLaterFixed() {
    // Solution 1: Use arrow function
    setTimeout(() => {
      console.log(`Hello, I'm ${this.name}`);
    }, 1000);

    // Solution 2: Bind 'this'
    setTimeout(
      function () {
        console.log(`Hello, I'm ${this.name}`);
      }.bind(this),
      1000
    );
  },
};

// Zero delay doesn't mean immediate execution
console.log("Start");
setTimeout(() => console.log("Timeout"), 0);
console.log("End");
// Output: "Start", "End", "Timeout"
```

## Call, Apply, Bind

**Question:** Explain the purpose and differences between call, apply, and bind methods.

**Answer:**

- `call`: Calls a function with a given `this` value and arguments provided individually
- `apply`: Calls a function with a given `this` value and arguments provided as an array
- `bind`: Creates a new function with a bound `this` value and optional preset arguments

**Example:**

```javascript
const person = {
  fullName: function (city, country) {
    return `${this.firstName} ${this.lastName} from ${city}, ${country}`;
  },
};

const john = {
  firstName: "John",
  lastName: "Doe",
};

// Using call
console.log(person.fullName.call(john, "New York", "USA"));
// "John Doe from New York, USA"

// Using apply
console.log(person.fullName.apply(john, ["Boston", "USA"]));
// "John Doe from Boston, USA"

// Using bind
const johnInfo = person.fullName.bind(john);
console.log(johnInfo("Chicago", "USA"));
// "John Doe from Chicago, USA"

// Bind with preset arguments
const johnFromSF = person.fullName.bind(john, "San Francisco");
console.log(johnFromSF("USA"));
// "John Doe from San Francisco, USA"
```

## ES6+ Features

**Question:** What are some key ES6+ features and how have they improved JavaScript?

**Answer:** ES6+ introduced many features that made JavaScript more powerful and expressive:

**Example:**

```javascript
// Arrow functions
const add = (a, b) => a + b;

// Destructuring
const { name, age } = { name: "John", age: 30 };
const [first, second] = [1, 2, 3];

// Spread/rest operators
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5]; // [1, 2, 3, 4, 5]

function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}

// Template literals
const user = "John";
console.log(`Hello, ${user}!`);

// Default parameters
function greet(name = "Guest") {
  return `Hello, ${name}!`;
}

// Classes
class Person {
  constructor(name) {
    this.name = name;
  }

  sayHello() {
    return `Hello, I'm ${this.name}`;
  }
}

// Modules
// file1.js
export const PI = 3.14;
export function square(x) {
  return x * x;
}

// file2.js
import { PI, square } from "./file1.js";

// Promises & async/await (covered in another section)

// Object shorthand
const x = 10,
  y = 20;
const point = { x, y };
```

## Higher-Order Functions

**Question:** What are higher-order functions? Give examples.

**Answer:** Higher-order functions are functions that take other functions as arguments or return functions as results. They enable more modular, reusable, and expressive code. Examples include map, filter, reduce, and function composition.

**Example:**

```javascript
// Functions that take functions as arguments
const numbers = [1, 2, 3, 4, 5];

// map
const doubled = numbers.map((num) => num * 2);
console.log(doubled); // [2, 4, 6, 8, 10]

// filter
const evens = numbers.filter((num) => num % 2 === 0);
console.log(evens); // [2, 4]

// reduce
const sum = numbers.reduce((total, num) => total + num, 0);
console.log(sum); // 15

// Functions that return functions
function multiply(factor) {
  return function (number) {
    return number * factor;
  };
}

const double = multiply(2);
const triple = multiply(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15

// Function composition
const compose = (f, g) => (x) => f(g(x));
const addOne = (x) => x + 1;
const square = (x) => x * x;

const addThenSquare = compose(square, addOne);
console.log(addThenSquare(3)); // (3+1)² = 16
```

## Spread VS Rest

🔹 Spread Operator (...)

👉 Used to expand elements of an array/object.

**Example:**

```javascript
const arr1 = [1, 2, 3];
const arr2 = [4, 5];

const combined = [...arr1, ...arr2];  
console.log(combined); // [1, 2, 3, 4, 5]
```
Here, ...arr1 spreads out [1, 2, 3] into the new array.

🔹 Rest Operator (...)

👉 Used to collect multiple arguments into a single variable.

**Example:**

```javascript
function sum(...numbers) {
return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3, 4)); // 10
```
Here, ...numbers collects all arguments into an array: [1, 2, 3, 4].

✅ Key difference:

Spread = expands

Rest = collects

## Pure Functions and Immutability

**Question:** What are pure functions and why is immutability important in JavaScript?

**Answer:** Pure functions are functions that:

1. Given the same input, always return the same output
2. Have no side effects (don't modify external state)
3. Don't depend on external state

Immutability means not changing data once it's created. This approach leads to more predictable code, easier debugging, better testability, and works well with React's rendering model.

**Example:**

```javascript
// Pure function example
function add(a, b) {
  return a + b;
}

// Impure function (has side effects)
let total = 0;
function addToTotal(value) {
  total += value; // Side effect: modifies external state
  return total;
}

// Immutability example - working with objects
const user = { name: "John", age: 30 };

// Bad: Mutating the object
function increaseAge(user) {
  user.age += 1; // Modifies original object
  return user;
}

// Good: Preserving immutability
function increaseAgeImmutably(user) {
  return { ...user, age: user.age + 1 }; // Returns new object
}

// Working with arrays immutably
const numbers = [1, 2, 3];

// Bad: Mutating the array
numbers.push(4); // Modifies original array

// Good: Immutable approaches
const newNumbers1 = [...numbers, 4]; // Spread syntax
const newNumbers2 = numbers.concat(4); // concat returns new array
const filteredNumbers = numbers.filter((n) => n !== 2); // Returns new array
```

## Error Handling

**Question:** How do you handle errors in JavaScript?

**Answer:** JavaScript uses try/catch blocks for synchronous code and either promises with .catch() or try/catch with async/await for asynchronous code. Custom errors can be created by extending the Error class.

**Example:**

```javascript
// Basic try/catch for synchronous code
try {
  const result = someFunctionThatMightFail();
  console.log(result);
} catch (error) {
  console.error("Something went wrong:", error.message);
} finally {
  console.log("This runs regardless of success or failure");
}

// Error handling in promises
fetchData()
  .then((data) => processData(data))
  .catch((error) => console.error("Error fetching data:", error))
  .finally(() => console.log("Fetch operation complete"));

// Error handling with async/await
async function getData() {
  try {
    const response = await fetch("https://api.example.com/data");

    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }

    const data = await response.json();
    return data;
  } catch (error) {
    console.error("Failed to fetch data:", error);
    // Graceful fallback or rethrow
    throw error; // Rethrow if you want callers to handle it
  }
}

// Custom error classes
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
  }
}

function validateUser(user) {
  if (!user.name) {
    throw new ValidationError("User name is required");
  }
  // More validation...
}

try {
  validateUser({});
} catch (error) {
  if (error instanceof ValidationError) {
    console.log("Validation failed:", error.message);
  } else {
    console.error("Unknown error:", error);
  }
}
```

## Event Loop & Asynchronous JavaScript

**Question:** Explain the JavaScript Event Loop and how it handles asynchronous operations.

**Answer:** The Event Loop is JavaScript's mechanism for handling asynchronous operations. JavaScript is single-threaded, but can perform non-blocking operations through the event loop, which constantly checks if the call stack is empty and then processes items from the callback queue.

**Example:**

```javascript
console.log("Start");

setTimeout(() => {
  console.log("Timeout callback");
}, 0);

Promise.resolve().then(() => {
  console.log("Promise resolved");
});

console.log("End");

// Output:
// "Start"
// "End"
// "Promise resolved"
// "Timeout callback"
```

The execution happens in this order because:

1. Synchronous code executes first ("Start", "End")
2. Microtasks (Promises) execute before macrotasks (setTimeout)
3. Even with 0ms timeout, setTimeout callback goes through the task queue

## == vs ===

**Question:** What's the difference between == and === operators?

**Answer:**

- `==` (loose equality): Compares values after type conversion
- `===` (strict equality): Compares both value and type without conversion

**Example:**

```javascript
console.log(5 == "5"); // true (string "5" is converted to number 5)
console.log(5 === "5"); // false (different types)

console.log(0 == false); // true (false converts to 0)
console.log(0 === false); // false (different types)

console.log(null == undefined); // true
console.log(null === undefined); // false

// Best practice: Generally use === for more predictable comparisons
```

## Prototypal Inheritance

**Question:** Explain JavaScript's prototypal inheritance model.

**Answer:** In JavaScript, objects inherit directly from other objects through a prototype chain. Each object has an internal prototype link to another object, and if a property isn't found on the object itself, JavaScript looks up the prototype chain until it finds it or reaches the end (null).

**Example:**

```javascript
// Constructor function approach
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function () {
  return `Hello, my name is ${this.name}`;
};

const john = new Person("John");
console.log(john.greet()); // "Hello, my name is John"
console.log(john.__proto__ === Person.prototype); // true

// Object.create approach
const personProto = {
  greet() {
    return `Hello, my name is ${this.name}`;
  },
};

const jane = Object.create(personProto);
jane.name = "Jane";
console.log(jane.greet()); // "Hello, my name is Jane"

// ES6 class syntax (still uses prototypes behind the scenes)
class Employee extends Person {
  constructor(name, role) {
    super(name);
    this.role = role;
  }

  introduce() {
    return `${this.greet()}. I am a ${this.role}.`;
  }
}

const dev = new Employee("Alex", "Developer");
console.log(dev.introduce()); // "Hello, my name is Alex. I am a Developer."
```

## Function Declaration vs Expression

**Question:** What's the difference between function declarations and function expressions?

**Answer:**

- **Function Declaration**: Defined with the `function` keyword at the start of a statement, hoisted completely
- **Function Expression**: Function assigned to a variable, only variable declaration is hoisted (if using var)

**Example:**

```javascript
// Function Declaration
hoisted(); // Works because the entire function is hoisted
function hoisted() {
  console.log("This function is hoisted");
}

// Function Expression
// notHoisted(); // TypeError: notHoisted is not a function
var notHoisted = function () {
  console.log("Function expressions are not hoisted");
};

// Arrow Function Expression (ES6)
const arrow = () => {
  console.log("Arrow functions are always expressions");
};

// Immediately Invoked Function Expression (IIFE)
(function () {
  console.log("IIFE executes immediately");
})();
```

## The 'this' Keyword

**Question:** How does the 'this' keyword work in JavaScript?

**Answer:** The value of 'this' depends on how a function is called, not where it's defined:

1. In global context: refers to the global object (window in browsers, global in Node.js)
2. In a method: refers to the object that owns the method
3. In a function: refers to global object (non-strict) or undefined (strict mode)
4. In an event: refers to the element that received the event
5. In arrow functions: inherits 'this' from the parent scope
6. With call/apply/bind: explicitly set by these methods

**Example:**

```javascript
// Global context
console.log(this === window); // true in browser

// Method context
const user = {
  name: "John",
  greet() {
    console.log(`Hello, ${this.name}`);
  },
};
user.greet(); // "Hello, John"

// Function context
function standalone() {
  console.log(this);
}
standalone(); // window (in non-strict mode)

// Event handler
document.getElementById("button").addEventListener("click", function () {
  console.log(this); // the button element
});

// Arrow function
const obj = {
  name: "Object",
  regularFunction: function () {
    console.log(this.name); // "Object"

    // Arrow function inherits 'this' from parent
    const arrowFunction = () => {
      console.log(this.name); // "Object"
    };
    arrowFunction();

    // Regular function has its own 'this'
    function innerRegular() {
      console.log(this.name); // undefined (or global in non-strict)
    }
    innerRegular();
  },
};
obj.regularFunction();

// call/apply/bind
function introduce(greeting) {
  console.log(`${greeting}, I'm ${this.name}`);
}

const person = { name: "Alice" };
introduce.call(person, "Hi"); // "Hi, I'm Alice"
introduce.apply(person, ["Hello"]); // "Hello, I'm Alice"
const boundIntroduce = introduce.bind(person);
boundIntroduce("Hey"); // "Hey, I'm Alice"
```

## Event Propagation & Delegation

**Question:** Explain event bubbling, capturing, and delegation in JavaScript.

**Answer:**

- **Event Bubbling**: Events "bubble" up from the target element to its ancestors
- **Event Capturing**: Events are first captured by the outermost element, then propagate to the target
- **Event Delegation**: Using event bubbling to handle events at a higher level in the DOM, reducing the number of event listeners

**Example:**

```javascript
// Event bubbling and capturing
document.getElementById("parent").addEventListener(
  "click",
  function () {
    console.log("Parent clicked");
  },
  false
); // false = bubbling phase (default)

document.getElementById("child").addEventListener(
  "click",
  function (e) {
    console.log("Child clicked");
    // e.stopPropagation(); // Uncomment to stop bubbling
  },
  false
);

// Event capturing (third parameter true)
document.getElementById("parent").addEventListener(
  "click",
  function () {
    console.log("Parent captured");
  },
  true
); // true = capturing phase

// Event delegation
document.getElementById("todo-list").addEventListener("click", function (e) {
  if (e.target.tagName === "LI") {
    console.log("Task clicked:", e.target.textContent);
    e.target.classList.toggle("completed");
  } else if (e.target.classList.contains("delete-btn")) {
    e.target.parentElement.remove();
    console.log("Task deleted");
  }
});

// HTML structure for above example:
// <ul id="todo-list">
//   <li>Task 1 <button class="delete-btn">X</button></li>
//   <li>Task 2 <button class="delete-btn">X</button></li>
// </ul>
```

## LocalStorage vs SessionStorage vs Cookies

**Question:** Compare and contrast localStorage, sessionStorage, and cookies.

**Answer:**

| Feature            | localStorage             | sessionStorage   | Cookies                 |
| ------------------ | ------------------------ | ---------------- | ----------------------- |
| Lifetime           | Until explicitly deleted | Tab session      | Can set expiration      |
| Storage Size       | ~5MB                     | ~5MB             | ~4KB                    |
| Sent with Requests | No                       | No               | Yes (increases traffic) |
| Accessibility      | Any window               | Same tab only    | Any window              |
| API                | Simple key/value         | Simple key/value | String parsing required |

**Example:**

```javascript
// localStorage - persists across browser sessions
localStorage.setItem("user", "John");
const user = localStorage.getItem("user");
localStorage.removeItem("user");
localStorage.clear(); // Clear all items

// sessionStorage - cleared when tab is closed
sessionStorage.setItem("sessionUser", "Jane");
const sessionUser = sessionStorage.getItem("sessionUser");
sessionStorage.removeItem("sessionUser");

// Cookies - can set expiration, sent with requests
document.cookie =
  "username=Alice; expires=Fri, 31 Dec 2023 23:59:59 GMT; path=/";
document.cookie = "language=en; SameSite=Strict; Secure";

// Reading cookies
function getCookie(name) {
  const cookies = document.cookie.split("; ");
  for (const cookie of cookies) {
    const [cookieName, cookieValue] = cookie.split("=");
    if (cookieName === name) {
      return cookieValue;
    }
  }
  return null;
}

console.log(getCookie("username")); // "Alice"
```

## Web Performance Optimization

**Question:** What techniques would you use to optimize a JavaScript application's performance?

**Answer:** JavaScript performance optimization involves several strategies:

**Example:**

```javascript
// 1. Code splitting and lazy loading
import("./moduleA.js").then((moduleA) => {
  // Use moduleA only when needed
});

// 2. Debouncing expensive handlers
const handleSearch = debounce(() => {
  // Expensive search operation
}, 300);

// 3. Use efficient selectors
// Bad
document.querySelectorAll(".item"); // Slow for large DOM
// Better
document.getElementById("container"); // Fast

// 4. Optimize loops
const arr = [1, 2, 3, 4, 5];
// Bad
for (let i = 0; i < arr.length; i++) {} // Length checked each iteration
// Better
for (let i = 0, len = arr.length; i < len; i++) {} // Cached length
// Even better for iterations
arr.forEach((item) => {}); // Or map, filter, etc.

// 5. Use requestAnimationFrame for visual updates
function updateUI() {
  // DOM updates here
  requestAnimationFrame(updateUI);
}

// 6. Web Workers for CPU-intensive tasks
const worker = new Worker("worker.js");
worker.onmessage = function (e) {
  console.log("Worker result:", e.data);
};
worker.postMessage({ data: complexDataToProcess });

// 7. Memoization for expensive calculations
function memoize(fn) {
  const cache = {};
  return function (...args) {
    const key = JSON.stringify(args);
    if (cache[key]) {
      return cache[key];
    }
    const result = fn.apply(this, args);
    cache[key] = result;
    return result;
  };
}

const expensiveCalculation = memoize(function (n) {
  console.log("Calculating...");
  return n * n;
});
```

## AJAX and Fetch API

**Question:** Compare XMLHttpRequest and the Fetch API for making HTTP requests.

**Answer:** Both are used to make HTTP requests, but Fetch is modern and uses Promises, while XMLHttpRequest is older with a callback-based approach.

**Example:**

```javascript
// XMLHttpRequest approach
function makeXHRRequest(url, callback) {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", url);

  xhr.onload = function () {
    if (xhr.status === 200) {
      callback(null, JSON.parse(xhr.responseText));
    } else {
      callback(new Error(`Request failed with status ${xhr.status}`));
    }
  };

  xhr.onerror = function () {
    callback(new Error("Network error"));
  };

  xhr.send();
}

makeXHRRequest("https://api.example.com/data", (error, data) => {
  if (error) {
    console.error(error);
  } else {
    console.log(data);
  }
});

// Fetch API approach
fetch("https://api.example.com/data")
  .then((response) => {
    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }
    return response.json();
  })
  .then((data) => console.log(data))
  .catch((error) => console.error("Fetch error:", error));

// Fetch with async/await
async function fetchData() {
  try {
    const response = await fetch("https://api.example.com/data");

    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }

    const data = await response.json();
    return data;
  } catch (error) {
    console.error("Fetch error:", error);
  }
}

// POST request with Fetch
fetch("https://api.example.com/submit", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({ name: "John", age: 30 }),
})
  .then((response) => response.json())
  .then((data) => console.log("Success:", data))
  .catch((error) => console.error("Error:", error));
```

## Micro-optimization Techniques

**Question:** What micro-optimization techniques can improve JavaScript code performance?

**Answer:** While modern JavaScript engines are very optimized, there are still techniques that can improve performance:

**Example:**

```javascript
// 1. Use appropriate array methods
const numbers = [1, 2, 3, 4, 5];

// Less efficient - multiple array iterations
let sum = 0;
numbers.forEach((num) => {
  if (num % 2 === 0) {
    sum += num * 2;
  }
});

// More efficient - single pass with reduce
const betterSum = numbers.reduce((acc, num) => {
  if (num % 2 === 0) {
    return acc + num * 2;
  }
  return acc;
}, 0);

// 2. Avoid unnecessary object creation in loops
// Less efficient
function createObjects(count) {
  const result = [];
  for (let i = 0; i < count; i++) {
    result.push({
      id: i,
      value: Math.random(),
    });
  }
  return result;
}

// More efficient - reuse object
function processItems(count) {
  const obj = { id: 0, value: 0 };
  const results = [];

  for (let i = 0; i < count; i++) {
    obj.id = i;
    obj.value = Math.random();
    // Do something with obj without pushing it
    results.push({ ...obj }); // Only create new object when needed
  }

  return results;
}

// 3. Use appropriate data structures
// Inefficient for lookups
const array = [
  { id: "a1", value: "Item 1" },
  { id: "a2", value: "Item 2" },
  // Hundreds more items...
];

// Find item by id - O(n) operation
const item = array.find((item) => item.id === "a99");

// More efficient - use object/Map for lookups - O(1)
const itemMap = new Map();
array.forEach((item) => {
  itemMap.set(item.id, item);
});
const foundItem = itemMap.get("a99"); // O(1) lookup

// 4. String concatenation
// Less efficient for many operations
let str = "";
for (let i = 0; i < 1000; i++) {
  str += i; // Creates new string each time
}

// More efficient
const parts = [];
for (let i = 0; i < 1000; i++) {
  parts.push(i);
}
const betterStr = parts.join("");
```

## Testing Strategies

**Question:** What testing strategies and tools would you use for a JavaScript application?

**Answer:** Effective JavaScript testing involves several layers and approaches:

**Example:**

```javascript
// Example using Jest for unit testing

// Function to test
function sum(a, b) {
  return a + b;
}

// Unit test
describe("sum function", () => {
  test("adds 1 + 2 to equal 3", () => {
    expect(sum(1, 2)).toBe(3);
  });

  test("handles negative numbers", () => {
    expect(sum(-1, -2)).toBe(-3);
  });

  test("adds decimals correctly", () => {
    expect(sum(0.1, 0.2)).toBeCloseTo(0.3);
  });
});

// Testing async functions
async function fetchUser(id) {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

// Mock the fetch API
jest.mock("fetch");

test("fetchUser calls the correct endpoint", async () => {
  fetch.mockResolvedValueOnce({
    json: async () => ({ id: 1, name: "John" }),
  });

  const user = await fetchUser(1);
  expect(user).toEqual({ id: 1, name: "John" });
  expect(fetch).toHaveBeenCalledWith("/api/users/1");
});

// React component testing example (using React Testing Library)
// Counter.jsx
function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <span data-testid="count">{count}</span>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// Counter.test.jsx
import { render, fireEvent, screen } from "@testing-library/react";

test("counter increments when button is clicked", () => {
  render(<Counter />);

  // Initial state
  expect(screen.getByTestId("count")).toHaveTextContent("0");

  // Click the button
  fireEvent.click(screen.getByText("Increment"));

  // Check updated state
  expect(screen.getByTestId("count")).toHaveTextContent("1");
});
```

## Security Best Practices

**Question:** What JavaScript security best practices should developers follow?

**Answer:** Security in JavaScript applications requires attention to several areas:

**Example:**

```javascript
// 1. Sanitize user input to prevent XSS
// Unsafe
document.getElementById("userContent").innerHTML = userInput; // XSS vulnerability

// Better - use textContent instead of innerHTML
document.getElementById("userContent").textContent = userInput;

// Or use a library like DOMPurify
import DOMPurify from "dompurify";
document.getElementById("userContent").innerHTML =
  DOMPurify.sanitize(userInput);

// 2. Avoid eval() and other dangerous functions
// Unsafe
function calculateUserInput(input) {
  return eval(input); // Never do this
}

// Better
// Use a proper expression parser or restrict input format

// 3. Secure cookies
document.cookie = "sessionId=abc123; HttpOnly; Secure; SameSite=Strict";

// 4. Prevent Prototype Pollution
// Unsafe
function merge(target, source) {
  for (const key in source) {
    if (key === "__proto__") continue; // Prevent prototype pollution
    target[key] = source[key];
  }
  return target;
}

// Better - use Object.assign with a new object
const result = Object.assign({}, target, source);

// 5. Use Content Security Policy (CSP)
// In your HTML or HTTP headers:
// <meta http-equiv="Content-Security-Policy" content="default-src 'self'">

// 6. Sanitize JSON data before parsing
try {
  // First, validate that the string contains valid JSON
  const data = JSON.parse(jsonString);

  // Then, validate the structure
  if (typeof data !== "object" || !data.hasOwnProperty("expectedProp")) {
    throw new Error("Invalid data structure");
  }

  // Use the data
} catch (e) {
  console.error("Invalid JSON:", e);
}

// 7. Protect against CSRF
const csrfToken = document.querySelector('meta[name="csrf-token"]').content;

fetch("/api/data", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "CSRF-Token": csrfToken,
  },
  body: JSON.stringify(data),
});
```

## Design Patterns in JavaScript

**Question:** What are some common design patterns in JavaScript and when would you use them?

**Answer:** Design patterns are reusable solutions to common problems. Here are some important patterns in JavaScript:

**Example:**

```javascript
// 1. Module Pattern
const calculator = (function () {
  // Private variables
  let result = 0;

  // Public interface
  return {
    add(x) {
      result += x;
      return this;
    },
    subtract(x) {
      result -= x;
      return this;
    },
    getResult() {
      return result;
    },
  };
})();

calculator.add(5).subtract(2);
console.log(calculator.getResult()); // 3

// 2. Singleton Pattern
const Database = (function () {
  let instance;

  function createInstance() {
    return {
      data: {},
      get(key) {
        return this.data[key];
      },
      set(key, value) {
        this.data[key] = value;
      },
    };
  }

  return {
    getInstance() {
      if (!instance) {
        instance = createInstance();
      }
      return instance;
    },
  };
})();

const db1 = Database.getInstance();
const db2 = Database.getInstance();
console.log(db1 === db2); // true

// 3. Observer Pattern
class EventEmitter {
  constructor() {
    this.events = {};
  }

  on(event, listener) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(listener);
    return this;
  }

  emit(event, ...args) {
    if (!this.events[event]) return false;
    this.events[event].forEach((listener) => {
      listener.apply(this, args);
    });
    return true;
  }

  off(event, listener) {
    if (!this.events[event]) return this;
    this.events[event] = this.events[event].filter((l) => l !== listener);
    return this;
  }
}

const emitter = new EventEmitter();
const handler = (data) => console.log("Data received:", data);
emitter.on("data", handler);
emitter.emit("data", { value: 42 }); // "Data received: { value: 42 }"

// 4. Factory Pattern
function createUser(type) {
  if (type === "admin") {
    return {
      name: "Admin",
      permissions: ["read", "write", "delete"],
    };
  } else if (type === "user") {
    return {
      name: "User",
      permissions: ["read"],
    };
  }
}

const admin = createUser("admin");
console.log(admin.permissions); // ['read', 'write', 'delete']

// 5. Decorator Pattern
function Car() {
  this.cost = function () {
    return 20000;
  };
}

function withAC(car) {
  const originalCost = car.cost();
  car.hasAC = true;
  car.cost = function () {
    return originalCost + 1000;
  };
  return car;
}

function withSunroof(car) {
  const originalCost = car.cost();
  car.hasSunroof = true;
  car.cost = function () {
    return originalCost + 1500;
  };
  return car;
}

const myCar = withSunroof(withAC(new Car()));
console.log(myCar.cost()); // 22500
```

## Memory Management & Leaks

**Question:** How does memory management work in JavaScript, and how can you prevent memory leaks?

**Answer:** JavaScript uses automatic garbage collection to free memory that's no longer needed. However, memory leaks can still occur in certain scenarios:

**Example:**

```javascript
// 1. Common memory leak: Forgotten global variables
function leak() {
  leakedVariable = "I am leaked"; // Missing 'var', 'let', or 'const'
}

// Fix: Always declare variables
function noLeak() {
  const localVariable = "I am contained";
}

// 2. Memory leak: Forgotten event listeners
function setupListener() {
  const button = document.getElementById("button");
  button.addEventListener("click", function () {
    // This keeps a reference to the button even if it's removed from DOM
    console.log("Button clicked", button);
  });
}

// Fix: Remove event listeners when no longer needed
function cleanSetup() {
  const button = document.getElementById("button");
  const handler = function () {
    console.log("Button clicked");
  };

  button.addEventListener("click", handler);

  return function cleanup() {
    button.removeEventListener("click", handler);
  };
}

// 3. Memory leak: Closures holding references
function createLargeObject() {
  const largeData = new Array(1000000).fill("data");

  return function () {
    // This function keeps largeData in memory
    // even if only a tiny bit is needed
    return largeData[0];
  };
}

// Fix: Only keep what you need
function betterFunction() {
  const largeData = new Array(1000000).fill("data");
  const firstItem = largeData[0];

  return function () {
    // Only keeps reference to what's needed
    return firstItem;
  };
}

// 4. Memory leak: Circular references (less common in modern browsers)
function createCircular() {
  const obj1 = {};
  const obj2 = {};

  obj1.ref = obj2;
  obj2.ref = obj1;

  return obj1;
}

// 5. Timers that aren't cleared
function startInterval() {
  const data = { counter: 0 };

  const intervalId = setInterval(() => {
    data.counter++;
    console.log(data.counter);
  }, 1000);

  // If we don't store intervalId or clear it,
  // this keeps data in memory forever
}

// Fix: Store and clear intervals/timeouts
function betterInterval() {
  const data = { counter: 0 };
  const intervalId = setInterval(() => {
    data.counter++;
    console.log(data.counter);

    if (data.counter >= 10) {
      clearInterval(intervalId);
    }
  }, 1000);

  return function stopInterval() {
    clearInterval(intervalId);
  };
}
```

## Web Components

**Question:** What are Web Components and how would you create one?

**Answer:** Web Components are a set of web platform APIs that allow you to create custom, reusable HTML elements. They consist of Custom Elements, Shadow DOM, and HTML Templates.

**Example:**

```javascript
// Creating a custom tooltip component
class CustomTooltip extends HTMLElement {
  constructor() {
    super();

    // Create shadow DOM
    this.attachShadow({ mode: "open" });

    // Create template
    const template = document.createElement("template");
    template.innerHTML = `
      <style>
        :host {
          position: relative;
          display: inline-block;
        }
        
        .tooltip-content {
          position: absolute;
          bottom: 125%;
          left: 50%;
          transform: translateX(-50%);
          background-color: #333;
          color: white;
          padding: 8px;
          border-radius: 4px;
          display: none;
          width: max-content;
          max-width: 200px;
        }
        
        :host(:hover) .tooltip-content {
          display: block;
        }
      </style>
      <slot></slot>
      <div class="tooltip-content"></div>
    `;

    // Attach template to shadow DOM
    this.shadowRoot.appendChild(template.content.cloneNode(true));

    // Store reference to tooltip content
    this.tooltipContent = this.shadowRoot.querySelector(".tooltip-content");
  }

  // Called when element is added to DOM
  connectedCallback() {
    // Get tooltip text from 'text' attribute
    const tooltipText = this.getAttribute("text");
    if (tooltipText) {
      this.tooltipContent.textContent = tooltipText;
    }
  }

  // Monitor attribute changes
  static get observedAttributes() {
    return ["text"];
  }

  attributeChangedCallback(name, oldValue, newValue) {
    if (name === "text" && this.tooltipContent) {
      this.tooltipContent.textContent = newValue;
    }
  }
}

// Register custom element
customElements.define("custom-tooltip", CustomTooltip);

// Using the component in HTML
// <custom-tooltip text="This is helpful information">Hover me</custom-tooltip>
```

## JavaScript Frameworks Comparison

**Question:** How would you compare React, Vue, and Angular? What are their strengths and weaknesses?

**Answer:** Each framework has its own philosophy and approach:

**Key Differences:**

| Feature          | React                    | Vue                    | Angular                   |
| ---------------- | ------------------------ | ---------------------- | ------------------------- |
| Learning Curve   | Moderate                 | Low                    | Steep                     |
| Size             | Small                    | Very Small             | Larger                    |
| Data Binding     | One-way                  | Two-way (optional)     | Two-way                   |
| State Management | External (Redux/Context) | Vuex                   | Services/RxJS             |
| Syntax           | JSX                      | Templates + JSX option | Templates with directives |
| Backed By        | Facebook                 | Community (Vue Team)   | Google                    |
| Model            | Component-based          | Component-based        | Complete framework        |

**Example of a Counter in Each:**

**React:**

```jsx
import React, { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

**Vue:**

```html
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        count: 0,
      };
    },
    methods: {
      increment() {
        this.count++;
      },
    },
  };
</script>
```

**Angular:**

```typescript
// counter.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-counter',
  template: `
    <div>
      <p>Count: {{ count }}</p>
```
