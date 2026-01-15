# Why We Need React 

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
