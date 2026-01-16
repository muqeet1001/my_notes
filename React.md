# How React Works (Start → End)

## First-Principles, Simple Language

### 🟢 STEP 1: You open a React website

You type: `example.com`

👉 Browser sends a request to the server.

### 🟢 STEP 2: Server sends files

Server sends:
- HTML file (comes first)
- CSS file
- JavaScript file (React + your code)

### 🟢 STEP 3: Browser reads the HTML

The HTML looks like this (conceptually):

```html
<body>
  <div id="root"></div>
</body>
```

**Important points:**
- HTML is almost empty
- Only one container: `div#root`
- No buttons, no text yet

👉 Browser creates initial DOM with an empty root.

### 🟢 STEP 4: Browser loads JavaScript

Now browser loads the JS file.

This JS contains:
- React library
- Your React code (written using JSX, already converted to JS)

### 🟢 STEP 5: React takes control of the page 🔑

React JavaScript says:

> "I will control everything inside div#root."

So React:
- finds `div#root`
- starts creating UI using JavaScript

👉 JavaScript becomes the page

### 🟢 STEP 6: React creates Virtual DOM (first time)

React:
- runs your component functions
- creates a Virtual DOM
- Virtual DOM = JavaScript object version of UI
- This happens in memory, not on screen

### 🟢 STEP 7: React updates the REAL DOM (first render)

Since this is first time:
- No old Virtual DOM exists
- React puts everything into the real DOM

👉 Browser paints UI  
👉 You see the page 🎉

### 🟢 STEP 8: User interacts (click, input, etc.)

User clicks a button or types.

**What changes?**
- State (data) changes
- NOT the DOM directly

### 🟢 STEP 9: React re-renders (VERY IMPORTANT)

When state changes:
- React re-runs the component
- Creates a new Virtual DOM
- Old Virtual DOM still exists

👉 React now has:
- Old Virtual DOM
- New Virtual DOM

### 🟢 STEP 10: React compares Virtual DOMs (diffing)

React compares: old vs new Virtual DOM

Here:
- `key` helps React identify list items
- React finds exactly what changed

### 🟢 STEP 11: React updates REAL DOM (minimal update)

React:
- updates only the changed parts
- does NOT reload the page
- does NOT rebuild everything

👉 UI updates smoothly

### 🟢 STEP 12: This cycle repeats forever 🔁

Every time:
- state changes
- React re-renders
- Virtual DOM is compared
- minimal DOM update happens

---

## Pre-React (Traditional Way)

### Websites were Multi Page Applications (MPA)

- Server sends HTML pages
- Browser reloads page for every action

### Page Load Flow

1. Browser requests page
2. Server sends HTML + CSS
3. Browser creates DOM (tree structure)
4. Page is rendered
5. JavaScript runs after DOM is created

### DOM

- DOM = live tree representation of HTML
- Stored in browser memory
- JavaScript manipulates DOM, not HTML file

### Problem (Form / Link Click)

1. Browser sends data to server
2. Server returns new HTML
3. Old DOM destroyed ❌
4. New DOM created ❌
5. Full page reload happens

### Issues

- Page reload on every action
- UI state lost
- Slow user experience
- Hard to manage DOM in big apps

## Why React

- Avoid full page reloads
- Keep page loaded once
- JavaScript controls UI
- Update only changed parts
- Build Single Page Applications (SPA)

## One-Line Summary

React is needed to avoid full page reloads by updating the UI using JavaScript instead of requesting new HTML pages.
 -------------------------------------------------------------------------------


# Why React Needs `key`

---

## What is `key`?
- `key` is a **unique identity** given to each item in a list rendered by React.
- It helps React **recognize** list items between renders.

---

## When do we use `key`?
- When rendering lists using `map()`
- Example:
```js
items.map(item => <Item key={item.id} />)
```
----------------------------------------------------
 ```js
   Whenever data changes, React creates a new Virtual DOM.
Then React compares the old Virtual DOM with the new one.
If list items don’t have keys, React cannot identify which item changed.
So React may update wrong or unnecessary elements.
key helps React identify list items correctly, so it updates only what actually changed.
```