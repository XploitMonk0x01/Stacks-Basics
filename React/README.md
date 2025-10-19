# The Complete React Learning Guide

This guide provides a comprehensive, hands-on journey into React, from core concepts to advanced state management and routing.

## Table of Contents

1.  [**Introduction to React & JSX**](#1-introduction-to-react--jsx)
    - What is React?
    - Understanding JSX
    - Creating Your First Component
2.  [**Core Concepts: State and Props**](#2-core-concepts-state-and-props)
    - Managing Component State with `useState`
    - Passing Data with Props
3.  [**Handling Events & Conditional Rendering**](#3-handling-events--conditional-rendering)
4.  [**The React Hook System**](#4-the-react-hook-system)
    - `useState`: State Management
    - `useEffect`: Handling Side Effects
    - `useContext`: Global State Management
    - `useRef`: Accessing DOM Elements
    - `useCallback`: Memoizing Functions
5.  [**Building a UI with Tailwind CSS**](#5-building-a-ui-with-tailwind-css)
6.  [**Routing with React Router**](#6-routing-with-react-router)
    - Setting Up the Router
    - Creating Routes and Layouts
    - Dynamic Routes and Data Loading
7.  [**Advanced State Management**](#7-advanced-state-management)
    - The Context API for Prop Drilling
    - Local Storage with Context
    - Redux Toolkit for Complex State

---

## 1. Introduction to React & JSX

### What is React?

React is a JavaScript library for building user interfaces. It allows you to create reusable UI components and manage the state of your application efficiently.

### Understanding JSX

JSX (JavaScript XML) is a syntax extension for JavaScript that looks similar to HTML. It allows you to write your component's markup directly within your JavaScript code.

**Example from `01vitereact/src/App.jsx`:**

```jsx
function App() {
  const username = 'react'
  return (
    // This looks like HTML, but it's JSX
    <h1>Chai aur {username}</h1>
  )
}
```

Here, `{username}` is a JavaScript expression embedded inside JSX.

### Creating Your First Component

A React component is a JavaScript function that returns JSX.

```jsx
// A simple functional component
function Welcome() {
  return <h1>Hello, World!</h1>
}
```

---

## 2. Core Concepts: State and Props

### Managing Component State with `useState`

The `useState` hook is the most fundamental hook in React. It lets you add a "state variable" to your component, which is a value that can change over time and cause the component to re-render.

**Example from `02counter/src/App.jsx`:**
This component has a `counter` state variable that is updated when the buttons are clicked.

```jsx
import { useState } from 'react'

function App() {
  // `counter` is the state variable, `setCounter` is the function to update it.
  let [counter, setCounter] = useState(15) // Initial value is 15

  const addValue = () => {
    // Always use the updater function to change state
    if (counter < 20) setCounter(counter + 1)
  }

  const removeValue = () => {
    if (counter > 0) setCounter(counter - 1)
  }

  return (
    <>
      <h2>Counter value: {counter}</h2>
      <button onClick={addValue}>Add value</button>
      <button onClick={removeValue}>remove value</button>
    </>
  )
}
```

**Key Point**: When updating state based on the previous state, use the functional update form to avoid bugs: `setCounter(prevCounter => prevCounter + 1)`.

### Passing Data with Props

Props (short for properties) allow you to pass data from a parent component to a child component. This makes components reusable.

**Example from `03tailwindprops/src/App.jsx`:**
The `App` component passes a `username` and `btntext` prop to the `Card` component.

```jsx
// In App.jsx
function App() {
  return (
    <>
      <Card username="chaiaurcode" btntext="Click me" />
      <Card username="hitesh" btntext="visit me" />
    </>
  )
}

// In components/Card.jsx
// Use object destructuring to receive props
function Card({ username, btntext = 'default text' }) {
  return (
    <div>
      <h1>{username}</h1>
      <button>{btntext}</button>
    </div>
  )
}
```

---

## 3. Handling Events & Conditional Rendering

You can handle events like clicks using camelCased attributes like `onClick`. You can render content conditionally using standard JavaScript logic.

```jsx
function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false)

  return (
    <div>
      {isLoggedIn ? (
        <p>Welcome back!</p>
      ) : (
        <button onClick={() => setIsLoggedIn(true)}>Log In</button>
      )}
    </div>
  )
}
```

---

## 4. The React Hook System

### `useState`: State Management

As seen above, `useState` is for managing local component state.

### `useEffect`: Handling Side Effects

The `useEffect` hook lets you perform side effects in your components. Common use cases include fetching data, subscribing to events, or directly manipulating the DOM.

```jsx
import { useState, useEffect } from 'react'

function Timer() {
  const [count, setCount] = useState(0)

  // This effect runs after every render
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`
  }) // Add a dependency array [] to run only once

  return <button onClick={() => setCount(count + 1)}>Click me</button>
}
```

### `useContext`: Global State Management

`useContext` lets you subscribe to React context without introducing nesting. It's a way to pass data through the component tree without having to pass props down manually at every level.

See the **Advanced State Management** section for a full example.

### `useRef`: Accessing DOM Elements

The `useRef` hook provides a way to access DOM nodes directly within React. It can also be used to hold a mutable value that does not cause a re-render when it changes.

**Primary Use Case: Focusing a text input.**

```jsx
import { useRef, useEffect } from 'react'

function TextInputWithFocusButton() {
  // Create a ref object
  const inputEl = useRef(null)

  useEffect(() => {
    // The 'current' property points to the mounted text input element
    inputEl.current.focus()
  }, []) // Empty dependency array ensures this runs only once after mount

  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={() => inputEl.current.focus()}>Focus the input</button>
    </>
  )
}
```

### `useCallback`: Memoizing Functions

The `useCallback` hook is used for performance optimization. It returns a memoized version of a callback function that only changes if one of its dependencies has changed. This is useful when passing callbacks to optimized child components that rely on reference equality to prevent unnecessary renders.

**Example: Preventing a child component from re-rendering.**

```jsx
import { useState, useCallback } from 'react'

// Assume `ExpensiveComponent` is a component that is costly to re-render.
function ParentComponent({ ExpensiveComponent }) {
  const [count, setCount] = useState(0)
  const [text, setText] = useState('')

  // Without useCallback, a new `handleClick` function is created on every render.
  // This would cause `ExpensiveComponent` to re-render even if only `text` changes.
  const handleClick = useCallback(() => {
    setCount(count + 1)
  }, [count]) // Only re-create the function if `count` changes

  return (
    <div>
      <input
        type="text"
        value={text}
        onChange={(e) => setText(e.target.value)}
      />
      <ExpensiveComponent onClick={handleClick} />
    </div>
  )
}
```

---

## 5. Building a UI with Tailwind CSS

Tailwind CSS is a utility-first CSS framework that pairs perfectly with React's component-based architecture.

**Example from `03tailwindprops/src/App.jsx`:**

```jsx
function App() {
  return (
    // These are Tailwind classes
    <h1 className="bg-green-400 text-black p-4 rounded-xl mb-4">
      Tailwind test
    </h1>
  )
}
```

---

## 6. Routing with React Router

React Router is the standard library for routing in React. It allows you to build a single-page application with navigation.

### Setting Up the Router

**Example from `07reactrouter/src/main.jsx`:**
You define your routes using `createBrowserRouter` and provide them to your app via `RouterProvider`.

```jsx
import {
  RouterProvider,
  createBrowserRouter,
  createRoutesFromElements,
  Route,
} from 'react-router-dom'

const router = createBrowserRouter(
  createRoutesFromElements(
    // The Layout component contains shared UI like a header and footer
    <Route path="/" element={<Layout />}>
      {/* Child routes are rendered inside the Layout's <Outlet /> */}
      <Route path="" element={<Home />} />
      <Route path="about" element={<About />} />
      <Route path="contact" element={<Contact />} />
    </Route>
  )
)

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
)
```

### Dynamic Routes and Data Loading

React Router can handle dynamic routes (e.g., user profiles) and even load data before a component renders.

**Example from `07reactrouter/src/main.jsx`:**

```jsx
<Route path='user/:userid' element={<User />} />
<Route
  loader={githubInfoLoader} // This function fetches data
  path='github'
  element={<Github />}
/>
```

- The `:userid` part is a URL parameter that can be accessed in the `User` component.
- The `loader` function is called before the `Github` component renders, and its return value is available to the component.

---

## 7. Advanced State Management

### The Context API for Prop Drilling

When you need to share state across many components, the Context API is a great solution.

**1. Create the Context:**
**File: `08miniContext/src/context/UserContext.js`**

```javascript
import React from 'react'
const UserContext = React.createContext()
export default UserContext
```

**2. Create the Provider:**
The provider is a component that makes the context value available to all its children.
**File: `08miniContext/src/context/UserContextProvider.jsx`**

```jsx
import React from 'react'
import UserContext from './UserContext'

const UserContextProvider = ({ children }) => {
  const [user, setUser] = React.useState(null)
  return (
    // The value prop makes `user` and `setUser` available
    <UserContext.Provider value={{ user, setUser }}>
      {children}
    </UserContext.Provider>
  )
}
```

**3. Consume the Context:**
Use the `useContext` hook in any child component to access the value.

```jsx
import React, { useContext } from 'react'
import UserContext from './UserContext'

function Profile() {
  const { user } = useContext(UserContext)

  if (!user) return <div>Please login</div>
  return <div>Welcome {user.username}</div>
}
```

### Local Storage with Context

You can combine `useContext` with `localStorage` to persist state across browser sessions.

**Example from `10todocontextLocal/src/contexts/TodoContext.js`:**
This project uses `localStorage` inside its `App.jsx` component's `useEffect` hooks to save and load todos, while the context itself just defines the "shape" of the shared data and functions.

```javascript
// In TodoContext.js
export const TodoContext = createContext({
  todos: [],
  addTodo: (todo) => {},
  updateTodo: (id, todo) => {},
  deleteTodo: (id) => {},
  toggleComplete: (id) => {},
})
```

### Redux Toolkit for Complex State

For very large applications, Redux provides a robust, centralized state management solution. Redux Toolkit is the modern, recommended way to use Redux.

**Key Concepts:**

- **Store**: A single, global object that holds the entire state of your app.
- **Slice**: A collection of Redux logic for a single feature. It includes a reducer and action creators.
- **Reducer**: A pure function that takes the current state and an action, and returns the new state.

**Example from `reduxtoolkittodo/src/features/todo/todoSlice.js`:**

```javascript
import { createSlice, nanoid } from '@reduxjs/toolkit'

const initialState = {
  todos: [{ id: 1, text: 'Hello world' }],
}

export const todoSlice = createSlice({
  name: 'todo',
  initialState,
  reducers: {
    // `addTodo` is an action creator
    addTodo: (state, action) => {
      const todo = {
        id: nanoid(),
        text: action.payload,
      }
      state.todos.push(todo)
    },
    removeTodo: (state, action) => {
      state.todos = state.todos.filter((todo) => todo.id !== action.payload)
    },
  },
})

export const { addTodo, removeTodo } = todoSlice.actions
export default todoSlice.reducer
```

This slice defines how to add and remove todos from the global state. Components can then "dispatch" these actions to update the state.
