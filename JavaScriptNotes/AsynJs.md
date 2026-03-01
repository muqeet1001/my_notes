# Asynchronous JavaScript (First Principles Notes)

## Phase 1 — Core Concepts

### 1. Core Truth: JavaScript Executes Code Sequentially

JavaScript executes code in a single execution thread.

This means:
- At any given moment, JavaScript can execute **only ONE operation**.

Execution always happens:
```
Line 1 → Line 2 → Line 3 → ...
```

There is **no parallel execution** on the main thread.

This is called:
- **Single-threaded execution model**

---

### 2. Default Execution is Synchronous

**Definition:**
- Synchronous execution means: Each operation must complete before the next operation begins.

**Example:**

```javascript
console.log("Start")

function task() {
  for(let i = 0; i < 1e9; i++){}
}

task()

console.log("End")
```

**Output:**
```
Start
(wait)
End
```

**Explanation:**
- JavaScript waits until `task()` finishes before moving forward.

This waiting is called:
- **Blocking execution**

---

### 3. Blocking is a Fundamental Limitation

Because JavaScript has only one thread:

**If one operation takes a long time:**
- Everything else must wait
- Nothing else can execute during that time.

This causes:
- UI freeze
- Application unresponsiveness
- Poor user experience

**Example:**
- Long loop blocks entire app

---

### 4. Real-World Operations Are Slow and Unpredictable

Many operations cannot complete instantly.

**Examples:**
- Network request → depends on internet speed
- Database query → depends on database performance
- File read → depends on disk speed
- Timer → depends on time delay
- User interaction → depends on user action

**Example:**
```javascript
fetch("/data")
```

Completion time unknown.

Could be:
- 10 ms
- 100 ms
- 5 seconds

JavaScript cannot block execution waiting.

---

### 5. Asynchronous Execution Solves the Blocking Problem

Asynchronous execution allows JavaScript to:
- Start an operation
- Continue executing other code
- Handle result when operation finishes

**Key idea:**
- JavaScript does **NOT wait** for async operations
- Execution continues immediately.

---

### 6. Async Operation Does NOT Block Execution

**Example:**

```javascript
console.log("Start")

setTimeout(() => {
  console.log("Async Task Done")
}, 2000)

console.log("End")
```

**Output:**
```
Start
End
Async Task Done
```

**Explanation:**
- JavaScript does NOT wait 2 seconds.
- It continues execution.
- Async task completes later.

---

### 7. Async Changes Execution Order

**Synchronous execution order:**
```
Start → Task → End
```

**Async execution order:**
```
Start → Register Task → End → Task executes later
```

This is called:
- **Non-blocking execution**

---

### 8. Async is Necessary for Efficient Systems

**Without async:**
- JavaScript would freeze during slow operations

**With async:**
- JavaScript remains responsive
- Multiple operations can be managed efficiently

**Async enables:**
- Network communication
- Timers
- User interactions
- Modern web applications

---

### 9. Important Example: setTimeout

**Syntax:**
```javascript
setTimeout(callback, delay)
```

**Behavior:**
- Registers callback to execute after delay
- Does NOT pause JavaScript execution
- Execution continues immediately

**Example:**

```javascript
console.log("A")

setTimeout(() => console.log("B"), 0)

console.log("C")
```

**Output:**
```
A
C
B
```

Even delay 0 executes later.

---

### 10. Core First Principle Model

Async follows this pattern:

1. Start async operation
2. Continue execution
3. Async operation finishes later
4. Execute async result

**This enables non-blocking systems.**

---

### 11. Phase 1 Core Definitions (Interview-Level)

**Synchronous:**
- Execution blocks until operation completes

**Asynchronous:**
- Execution does NOT block while operation completes

**Blocking:**
- Execution thread is occupied and cannot execute other code

**Non-blocking:**
- Execution thread remains free to execute other code

---

### 12. Most Important Rule (Golden Rule)

⭐ **Synchronous code executes immediately**

⭐ **Asynchronous code executes later**

**Always true.**

---

## Phase 1 Mental Model (Final)

Think like this:

```
JavaScript:
  → Do immediate work now
  → Schedule slow work for later
  → Handle slow work when ready
```

---

## Phase 1 Mastery Checklist

If you understand these, Phase 1 is complete:

- ✔ JavaScript is single-threaded
- ✔ JavaScript executes synchronously by default
- ✔ Blocking means execution waits
- ✔ Async prevents blocking
- ✔ Async operations execute later
- ✔ setTimeout does not block execution
- ✔ Async enables efficient applications

---

## Phase 2 — JavaScript Runtime and Async Execution Architecture

### 1. JavaScript Runtime Components

JavaScript async execution is made possible by the runtime environment.

**Core components:**

1. **Call Stack**
2. **Web APIs** (Browser) / **libuv** (Node.js)
3. **Callback Queue** (Task Queue / Macrotask Queue)
4. **Event Loop**

**Key Fact:**
- JavaScript Engine executes code only via the Call Stack.
- Async execution is coordinated using these components.

---

### 2. Call Stack

**Definition:**
- Call Stack is a **LIFO (Last In First Out)** data structure that manages execution contexts.

**Purpose:**
- Tracks function calls
- Executes synchronous code

**Example:**

```javascript
function A() {
  console.log("A")
}

function B() {
  A()
}

B()
```

**Execution flow:**
```
Push B
Push A
Execute console.log
Pop A
Pop B
```

**Key Rule:**
- Only functions inside Call Stack can execute

---

### 3. Problem: Call Stack Cannot Handle Long Async Tasks

If async tasks executed in Call Stack, execution would block.

**Example:**
```javascript
setTimeout(fn, 5000)
```

**If Call Stack waited 5 seconds:**
- Application would freeze

**Solution:**
- Async tasks are handled **outside Call Stack**.

---

### 4. Web APIs (Async Execution Environment)

**Definition:**
- Web APIs are runtime-provided async handlers.

**Provided by:**
- **Browser** → Web APIs
- **Node.js** → libuv + OS

**Examples:**
- setTimeout
- fetch
- setInterval
- DOM events
- File system operations
- Database calls

**Role:**
- Execute async operations outside Call Stack
- JavaScript delegates async work to Web APIs.

---

### 5. Async Operation Execution Flow

**Example:**

```javascript
setTimeout(() => {
  console.log("Hello")
}, 2000)
```

**Execution:**

| Step | Action |
|------|--------|
| 1 | Call Stack → register setTimeout |
| 2 | Web APIs → start timer |
| 3 | Call Stack becomes free |
| 4 | Timer completes in Web APIs |
| 5 | Callback function moves to Callback Queue |

---

### 6. Callback Queue (Task Queue / Macrotask Queue)

**Definition:**
- Callback Queue is a **FIFO (First In First Out)** queue that stores async callbacks ready for execution.

**Contains:**
- Callback function references

**Does NOT contain:**
- Raw data

**Example:**

```
Callback Queue:
[
  callbackFunctionReference
]
```

**Key Point:**
- Callbacks wait here until Call Stack is empty.

---

### 7. Event Loop

**Definition:**
- Event Loop continuously monitors:
  - Call Stack
  - Callback Queue

**Role:**
- Moves callback from Callback Queue → Call Stack when Call Stack is empty

**Algorithm:**

```javascript
while(true) {
   if(callStackIsEmpty) {
      moveCallbackFromQueueToStack()
   }
}
```

**Key Fact:**
- Event Loop ensures **non-blocking execution**.

---

### 8. Full Async Execution Pipeline

**Complete flow:**

1. JavaScript executes synchronous code in Call Stack
2. Async operation is delegated to Web APIs
3. Web APIs perform async task
4. When async task completes, callback is placed in Callback Queue
5. Event Loop detects empty Call Stack
6. Event Loop moves callback to Call Stack
7. JavaScript executes callback

---

### 9. Important Rule: Callback Queue Stores Functions, Not Data

**Callback Queue contains:**
- Function reference

**NOT:**
- Raw data

**Data is available to function via closure or internal state.**

**Example conceptual representation:**

```
Callback Queue:
[
   () => console.log(data)
]
```

---

### 10. Closures in Async Execution

**Definition:**
- Closures capture variable **references**, not values.

**Example:**

```javascript
let data = "initial"

setTimeout(() => {
  console.log(data)
}, 2000)

data = "updated"
```

**Output:**
```
updated
```

**Reason:**
- Callback references variable, not original value.
- Value is resolved at execution time.

---

### 11. Execution Priority Rule

**Execution order:**

1. Call Stack executes all **synchronous code first**
2. Async callbacks execute **only after Call Stack becomes empty**

**Critical Rule:**
- Async callbacks **NEVER interrupt** synchronous execution.

---

### 12. Visual Architecture Diagram

```
           JavaScript Runtime

        ┌────────────────────┐
        │     Call Stack     │
        └─────────┬──────────┘
                  │
                  ▼
        ┌────────────────────┐
        │     Web APIs       │
        │  (async handlers)  │
        └─────────┬──────────┘
                  │
                  ▼
        ┌────────────────────┐
        │   Callback Queue   │
        │    (callbacks)     │
        └─────────┬──────────┘
                  │
                  ▼
        ┌────────────────────┐
        │    Event Loop      │
        │  (coordinator)     │
        └────────────────────┘
```

---

## Phase 2 Core Mental Model

**Async execution model:**

1. Call Stack executes sync code
2. Web APIs execute async tasks
3. Callback Queue stores callbacks
4. Event Loop schedules callback execution
5. Call Stack executes callback

**The Dance:**
- Call Stack works → Web APIs work
- Web APIs finish → Callback Queue receives
- Call Stack empty → Event Loop activates
- Event Loop moves callback → Call Stack executes

---

## Phase 2 Mastery Checklist

You should fully understand:

- ✔ Call Stack execution model
- ✔ Web APIs role in async execution
- ✔ Callback Queue purpose
- ✔ Event Loop coordination
- ✔ Async execution pipeline
- ✔ Callback Queue stores functions, not data
- ✔ Closure behavior in async callbacks
- ✔ Execution priority (sync before async)

---

## Phase 3 Coming Soon

**Next: Promises, Async/Await, and Microtasks vs Macrotasks**
# JavaScript Promises — Complete Mastery Guide

---

## Table of Contents

1. What is a Promise?
2. Why Promises Were Created
3. Promise States
4. Creating Promises
5. Consuming Promises (.then())
6. Error Handling (.catch() and .finally())
7. Promise Chaining
8. Golden Rules
9. Promise Flattening and Resolution Behavior
10. Microtask Queue
11. Promise Combinators (all, allSettled, race, any)
12. Common Production Bugs
13. Advanced Examples and Interview Questions

---

## 1. What is a Promise?

### Definition (Technical)

A Promise is an object that represents the **eventual completion or failure** of an asynchronous operation and its resulting value.

### Simple Definition

**Promise = Placeholder for a future value**

It's like saying: "I promise to give you the result later"

### Real Life Analogy

You order food and receive a **token** (receipt).

- Token = Promise
- Food ready → Promise fulfilled
- Food failed → Promise rejected

### Key Characteristics

- A class (constructor)
- An object (instance)
- Represents asynchronous work
- Container for future value

```javascript
const p = new Promise(() => {})
```

---

## 2. Why Promises Were Created

JavaScript is **single-threaded and synchronous** by default. But some tasks take time:

- Network requests (fetch)
- setTimeout / setInterval
- File system operations
- Database calls

### Problems with Callbacks (Before Promises)

1. **Callback Hell** — Deeply nested callbacks
2. **Inversion of Control** — Loss of control over code flow
3. **Difficult Error Handling** — Hard to track errors
4. **Poor Readability** — Code is hard to understand

### Example of Callback Hell

```javascript
getUser(function(user) {
  getOrders(user, function(orders) {
    getDetails(orders, function(details) {
      console.log(details)
    })
  })
})
```

### How Promises Solve This

```javascript
getUser()
  .then(user => getOrders(user))
  .then(orders => getDetails(orders))
  .then(details => console.log(details))
```

**Benefits:**

- Flat structure
- Readable
- Maintainable
- Built-in error handling

---

## 3. Promise States

Every Promise has exactly **3 states**:

### State 1: Pending

- Initial state
- Operation still running
- No result yet

Example:

```javascript
new Promise((resolve, reject) => {})
```

### State 2: Fulfilled

- Operation completed successfully
- Result is available
- Occurs when `resolve()` is called

Example:

```javascript
resolve("Success")
```

### State 3: Rejected

- Operation failed
- Error is available
- Occurs when `reject()` is called

Example:

```javascript
reject("Error")
```

### State Transition Rules

A Promise can transition **only once**:

```
Pending → Fulfilled
OR
Pending → Rejected
```

**Never:**

```
Fulfilled → Rejected
Rejected → Fulfilled
```

Promise state is **immutable** after resolution.

### Visual Diagram

```
           Pending
          /       \
         /         \
 Fulfilled       Rejected
(success)         (error)
```

---

## 4. Creating Promises

### Syntax

```javascript
const promise = new Promise((resolve, reject) => {
  // resolve → marks promise fulfilled
  // reject → marks promise rejected
})
```

### Example 1: Simple Promise with resolve()

```javascript
const promise = new Promise((resolve, reject) => {
  resolve("Task completed")
})
```

**State:** Fulfilled  
**Result:** "Task completed"

### Example 2: Async Promise with setTimeout

```javascript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("Done")
  }, 2000)
})
```

**State flow:** Pending → Fulfilled (after 2 seconds)

### Example 3: Rejected Promise

```javascript
const promise = new Promise((resolve, reject) => {
  reject("Error occurred")
})
```

**State:** Rejected

### Example 4: Conditional Promise

```javascript
const promise = new Promise((resolve, reject) => {
  let success = true

  if(success)
    resolve("Success")
  else
    reject("Failed")
})
```

---

## 5. Consuming Promises (.then())

### Syntax

```javascript
promise.then(onFulfilled)
promise.then(onFulfilled, onRejected)
```

### Basic Example

```javascript
promise.then((result) => {
  console.log(result)
})
```

### Receiving the resolve Value

When `resolve()` is called with a value:

```javascript
resolve(10)
```

The `.then()` receives it:

```javascript
.then(value => console.log(value))  // value = 10
```

### Example: Complete Flow

```javascript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("Task completed")
  }, 2000)
})

promise.then((result) => {
  console.log(result)
})
```

Output after 2 seconds:

```
Task completed
```

### Internal Flow

1. Promise created → Pending
2. `resolve()` called → Fulfilled
3. `.then()` callback enters **Microtask Queue**
4. Event Loop executes callback
5. Result printed

---

## 6. Error Handling (.catch() and .finally())

### Using .catch() for Error Handling

**Syntax:**

```javascript
promise.catch(onRejected)
```

**Example:**

```javascript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject("Error occurred")
  }, 2000)
})

promise.catch((error) => {
  console.log(error)
})
```

Output:

```
Error occurred
```

### Error Propagation

When an error is thrown inside `.then()`:

```javascript
Promise.resolve(1)
  .then(x => x + 1)
  .then(x => { throw new Error("Fail") })
  .catch(err => console.log(err.message))
```

Errors **automatically propagate** to nearest `.catch()`.

### Important: reject() Skips .then()

When a Promise is rejected, `.then()` is **skipped**:

```javascript
Promise.reject("Error")
  .then(() => console.log("Success"))
  .catch(console.log)
```

Output:

```
Error
```

### Using .finally() for Cleanup

**Definition:** Runs regardless of success or failure

```javascript
promise
  .then(result => console.log(result))
  .catch(error => console.log(error))
  .finally(() => console.log("Cleanup"))
```

The `.finally()` block **always executes**.

### Real-world Example: Complete Error Handling

```javascript
const promise = new Promise((resolve, reject) => {
  let success = true

  if(success)
    resolve("Success")
  else
    reject("Failed")
})

promise
  .then(result => console.log(result))
  .catch(error => console.log(error))
  .finally(() => console.log("Operation complete"))
```

---

## 7. Promise Chaining

### Key Rule: .then() Always Returns a New Promise

**This is CRITICAL**

Every `.then()` call returns a **new Promise**:

```javascript
Promise.resolve(5)
  .then(x => x * 2)
  .then(x => x * 3)
  .then(console.log)
```

Output:

```
30
```

**Flow:**

```
5 → 10 → 30
```

### How Promise Chaining Works

1. First `.then()` receives 5, returns 10
2. Second `.then()` receives 10, returns 30
3. Third `.then()` receives 30, prints it

### Two Ways to Return Values

#### Case 1: Returning Normal Value

```javascript
.then(x => x * 2)
```

Internally becomes:

```javascript
.then(x => Promise.resolve(x * 2))
```

#### Case 2: Returning a Promise

```javascript
.then(x => Promise.resolve(x * 2))
```

The next `.then()` **waits** for the promise.

---

## 8. Golden Rules

### Rule 1: Promise is Object Representing Future Value

A Promise stores internally:

- state
- result
- callbacks

```javascript
Promise {
  state: "fulfilled",
  result: "Success"
}
```

### Rule 2: resolve → Fulfilled, reject → Rejected

```javascript
resolve(value)     // → fulfilled
reject(error)      // → rejected
```

### Rule 3: .then() Receives Resolved Value

```javascript
resolve(10)
.then(value => console.log(value))  // value = 10
```

### Rule 4: .then() Returns New Promise

```javascript
Promise.resolve(5)
  .then(x => x * 2)  // Returns Promise.resolve(10)
```

### Rule 5: If .then() Does NOT Return, undefined Propagates

**Wrong:**

```javascript
Promise.resolve(10)
  .then(x => {
    console.log(x)
    // No return! → undefined
  })
  .then(console.log)
```

Output:

```
10
undefined
```

**Correct:**

```javascript
Promise.resolve(10)
  .then(x => {
    console.log(x)
    return x * 2
  })
  .then(console.log)
```

Output:

```
10
20
```

### Rule 6: Returning Promise Makes Chain Wait

When you return a Promise:

```javascript
.then(x => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x * 2)
    }, 1000)
  })
})
```

The **next `.then()` waits** for resolution.

### Rule 7: Promise Callbacks Go to Microtask Queue

Not the regular callback queue.

```javascript
Promise.resolve().then(() => console.log("Promise"))
setTimeout(() => console.log("Timeout"), 0)
```

Output:

```
Promise
Timeout
```

Because Microtask Queue executes first.

### Rule 8: Missing Return Breaks Promise Chain

**Common bug:**

```javascript
.then(x => {
  fetchData(x)  // ❌ No return
})
```

**Correct:**

```javascript
.then(x => {
  return fetchData(x)  // ✅ Return
})
```

OR shorthand:

```javascript
.then(fetchData)
```

---

## 9. Promise Flattening and Resolution Behavior

### What is Promise Flattening?

JavaScript **automatically unwraps** nested promises.

### Example: Return Normal Value

```javascript
Promise.resolve(5)
  .then(x => x * 2)
  .then(console.log)
```

Output:

```
10
```

### Example: Return Promise.resolve()

```javascript
Promise.resolve(5)
  .then(x => Promise.resolve(x * 2))
  .then(console.log)
```

Output:

```
10
```

**Both behave the same!** The promise is automatically unwrapped.

### Example: Return new Promise

```javascript
Promise.resolve(5)
  .then(x => new Promise(resolve => {
    resolve(x * 2)
  }))
  .then(console.log)
```

Output:

```
10
```

### Promise Resolution Behavior Table

| You return | What next .then() receives |
|----------|--------------------------|
| value | value |
| Promise.resolve(value) | value |
| new Promise(...) | resolved value |
| throw error | goes to catch |
| Promise.reject(error) | goes to catch |

---

## 10. Microtask Queue (VERY IMPORTANT)

### Priority Order

1. **Call Stack** — Regular code
2. **Microtask Queue** — Promises, .then(), .catch()
3. **Macrotask Queue** — setTimeout, setInterval

### Example: Execution Order

```javascript
console.log("1")

setTimeout(() => console.log("2"), 0)

Promise.resolve().then(() => console.log("3"))

console.log("4")
```

Output:

```
1
4
3
2
```

**Explanation:**

1. `console.log("1")` — Call Stack → **1**
2. `setTimeout` → Macrotask Queue
3. `Promise.then()` → Microtask Queue
4. `console.log("4")` — Call Stack → **4**
5. Microtask Queue runs → **3**
6. Macrotask Queue runs → **2**

### Promise Execution Pipeline

**Full flow:**

1. Create promise (Call Stack)
2. Web APIs execute async work
3. resolve/reject is called
4. Promise state changes
5. .then callback enters Microtask Queue
6. Event Loop moves callback to Call Stack
7. Callback executes

---

## 11. Promise Combinators

### 1. Promise.all() — "Wait for ALL to succeed"

#### Definition

- Waits for **ALL** promises to succeed
- Fails **immediately** if ANY fails
- Returns results as an **array**

#### Mental Model

You and 3 friends ordered food.

You start eating ONLY when ALL food arrives.

If ANY friend's food fails → whole dinner fails.

#### Example

```javascript
const p1 = Promise.resolve("Burger")
const p2 = Promise.resolve("Pizza")
const p3 = Promise.resolve("Juice")

Promise.all([p1, p2, p3])
  .then(console.log)
```

Output:

```
["Burger", "Pizza", "Juice"]
```

#### Failure Example

```javascript
Promise.all([
  Promise.resolve("Burger"),
  Promise.reject("Failed"),
  Promise.resolve("Juice")
])
.catch(console.log)
```

Output:

```
Failed
```

#### Rules

- Waits for ALL promises
- Returns results in ORIGINAL ORDER
- If ANY fails → whole Promise.all fails
- Fails fast (doesn't wait for others)

#### Real-world Use

Load user, posts, comments in parallel:

```javascript
Promise.all([
  fetch('/user'),
  fetch('/posts'),
  fetch('/comments')
])
```

---

### 2. Promise.allSettled() — "Wait for ALL, even failures"

#### Definition

- Waits for **ALL** promises regardless of success/failure
- **Never fails**
- Returns detailed status of each promise

#### Mental Model

You ordered from 3 restaurants.

You want a report:

- Which delivered?
- Which failed?

#### Example

```javascript
Promise.allSettled([
  Promise.resolve("Burger"),
  Promise.reject("Pizza failed"),
  Promise.resolve("Juice")
])
.then(console.log)
```

Output:

```javascript
[
  { status: "fulfilled", value: "Burger" },
  { status: "rejected", reason: "Pizza failed" },
  { status: "fulfilled", value: "Juice" }
]
```

#### Rules

- Never fails
- Always returns full report
- Shows status and reason for each

#### Real-world Use

Load multiple APIs and show which failed:

```javascript
Promise.allSettled([
  fetch(API1),
  fetch(API2),
  fetch(API3)
]).then(results => {
  results.forEach(result => {
    if(result.status === 'rejected') {
      console.log("Failed:", result.reason)
    }
  })
})
```

---

### 3. Promise.race() — "First result wins"

#### Definition

- Returns **FIRST** promise that completes (success OR failure)
- Doesn't care about success/failure
- Ignores rest of promises

#### Mental Model

3 runners racing.

Whoever finishes first → winner.

Doesn't matter if they win or lose.

#### Example

```javascript
const slow = new Promise(r => setTimeout(() => r("Slow"), 2000))
const fast = new Promise(r => setTimeout(() => r("Fast"), 1000))

Promise.race([slow, fast])
  .then(console.log)
```

Output:

```
Fast
```

#### Rules

- First completed promise wins (success OR failure)
- Ignores rest

#### Real-world Use: Timeout System

```javascript
Promise.race([
  fetch(API),
  new Promise((_, reject) =>
    setTimeout(() => reject("Timeout"), 5000)
  )
])
```

---

### 4. Promise.any() — "First SUCCESS wins"

#### Definition

- Returns **FIRST SUCCESSFUL** promise
- Ignores failures
- Fails **only if ALL fail**

#### Mental Model

You try logging in using:

- Google login
- Facebook login
- Email login

If one login works → you enter.

It ignores failed login methods.

#### Example

```javascript
Promise.any([
  Promise.reject("Failed"),
  Promise.resolve("Delivered"),
  Promise.reject("Failed")
])
.then(console.log)
```

Output:

```
Delivered
```

#### Rules

- First success wins
- Ignores failures
- Fails only if ALL fail

#### race vs any Difference

| Method | Chooses | Criteria |
|--------|---------|----------|
| race | First completed | success OR failure |
| any | First completed | success ONLY |

---

### 5. Promise.finally() — "Always runs cleanup"

#### Definition

- Runs **regardless** of success or failure

#### Mental Model

After eating food:

Clean table always.

Regardless food was good or bad.

#### Example

```javascript
Promise.resolve("Food")
  .finally(() => console.log("Cleaning"))
  .then(console.log)
```

Output:

```
Cleaning
Food
```

---

### Comparison Table (Memorize This)

| Method | Waits for | Fails when | Returns |
|--------|-----------|-----------|---------|
| all | All success | Any fails | Array of values |
| allSettled | All finish | Never | Array of {status, value/reason} |
| race | First finish | N/A | First settled result |
| any | First success | All fail | First successful value |
| finally | Always | Never | Original value |

### Memory Tricks

- **all** → ALL must succeed
- **allSettled** → Wait for ALL results
- **race** → Fastest wins
- **any** → First success wins
- **finally** → Cleanup runs always

---

## 12. Common Production Bugs

### Bug 1: Forgetting to Return

**Wrong:**

```javascript
.then(x => {
  fetchData(x)  // No return!
})
```

**Correct:**

```javascript
.then(x => {
  return fetchData(x)
})
```

### Bug 2: Missing Error Handling

**Wrong:**

```javascript
Promise.resolve()
  .then(() => dangerousOperation())
  // No .catch()!
```

**Correct:**

```javascript
Promise.resolve()
  .then(() => dangerousOperation())
  .catch(error => console.log(error))
```

### Bug 3: Mixing Callbacks and Promises Incorrectly

**Wrong:**

```javascript
.then(x => {
  setTimeout(() => {
    return x * 2  // Promise doesn't see this
  }, 1000)
})
```

**Correct:**

```javascript
.then(x => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x * 2)
    }, 1000)
  })
})
```

### Bug 4: Not Handling Rejection in Promise.all()

When using Promise.all with unreliable promises, always handle catch:

```javascript
Promise.all([p1, p2, p3])
  .then(results => {...})
  .catch(error => console.log("One failed:", error))
```

---

## 13. Static Promise Methods

### Promise.resolve()

Creates an **already fulfilled** promise:

```javascript
Promise.resolve(10)
  .then(console.log)
```

Output:

```
10
```

### Promise.reject()

Creates an **already rejected** promise:

```javascript
Promise.reject("Error")
  .catch(console.log)
```

Output:

```
Error
```

---

## 14. Advanced Examples

### Example 1: Hard Interview Question

What will this print and why?

```javascript
Promise.resolve("Start")
  .then(val => {
    console.log(val)
    return Promise.resolve("Middle")
  })
  .then(val => {
    console.log(val)
    return new Promise(resolve => {
      setTimeout(() => resolve("End"), 0)
    })
  })
  .then(val => {
    console.log(val)
  })

console.log("Outside")
```

**Output:**

```
Outside
Start
Middle
End
```

**Explanation:**

1. **Synchronous phase:** `console.log("Outside")` runs immediately → **Outside**
2. **Microtask phase:**
   - First `.then()` → prints "Start"
   - Second `.then()` → prints "Middle"
   - Returns promise with setTimeout (goes to Macrotask Queue)
3. **Macrotask phase:**
   - setTimeout callback resolves
   - Third `.then()` → prints "End"

### Example 2: Promise Flattening

What will this print?

```javascript
Promise.resolve("A")
  .then(val => {
    console.log(val)
    return new Promise(resolve => {
      resolve(
        Promise.resolve("B")
      )
    })
  })
  .then(val => {
    console.log(val)
  })

console.log("C")
```

**Output:**

```
C
A
B
```

**Explanation:**

Even though Promise is nested inside `resolve()`, JavaScript **automatically flattens** it.

---

## 15. Interview Questions

### Easy Questions

#### Q1: What are the 3 states of Promise?

**Answer:**

1. Pending — Initial state
2. Fulfilled — Success
3. Rejected — Failure

#### Q2: Predict output

```javascript
const p = new Promise((resolve) => {
  resolve("Hello")
})

p.then(console.log)
```

**Answer:**

```
Hello
```

### Medium Questions

#### Q3: Predict output

```javascript
const p = new Promise((resolve) => {
  setTimeout(() => {
    resolve("Done")
  }, 1000)
})

console.log("Start")
p.then(console.log)
console.log("End")
```

**Answer:**

```
Start
End
Done
```

(After 1 second)

#### Q4: Create promise that resolves after 3 seconds

```javascript
const p = new Promise(resolve => {
  setTimeout(() => {
    resolve("Finished")
  }, 3000)
})

p.then(console.log)
```

#### Q5: What's the difference between Promise.race() and Promise.any()?

**Answer:**

- **Promise.race()** → Returns first completed (success OR failure)
- **Promise.any()** → Returns first successful only

### Hard Questions

#### Q6: Predict output

```javascript
Promise.any([
  Promise.reject("A"),
  Promise.reject("B"),
  Promise.resolve("C"),
  Promise.resolve("D")
])
.then(console.log)
```

**Answer:**

```
C
```

First successful promise wins.

#### Q7: Will this ever fail?

```javascript
Promise.allSettled([
  Promise.reject("A"),
  Promise.reject("B"),
  Promise.reject("C")
])
.then(console.log)
.catch(error => console.log("Caught:", error))
```

**Answer:**

No. Promise.allSettled **never fails**.

The `.catch()` will never run.

#### Q8: Design a Promise timeout system

```javascript
function fetchWithTimeout(url, timeout = 5000) {
  return Promise.race([
    fetch(url),
    new Promise((_, reject) =>
      setTimeout(
        () => reject("Timeout"),
        timeout
      )
    )
  ])
}

fetchWithTimeout('/api', 3000)
  .then(res => res.json())
  .catch(error => console.log(error))
```

#### Q9: Real-world Challenge

You are loading data from 3 APIs simultaneously. You want:

- Show successful data immediately
- Show which APIs failed
- Don't fail entire page if one API fails

Which combinator would you use?

**Answer:**

Use **Promise.allSettled()** because:

- Never fails (page stays functional)
- Shows success and failures
- Can display partial data

```javascript
Promise.allSettled([
  fetch(API1),
  fetch(API2),
  fetch(API3)
]).then(results => {
  results.forEach((result, index) => {
    if(result.status === 'fulfilled') {
      console.log(`API ${index} succeeded:`, result.value)
    } else {
      console.log(`API ${index} failed:`, result.reason)
    }
  })
})
```

---

## Summary Checklist

You now understand:

✅ Promise creation  
✅ resolve and reject  
✅ Promise states  
✅ .then() chaining  
✅ Returning values  
✅ Returning promises  
✅ Error handling (.catch, .finally)  
✅ Missing return bug  
✅ Promise flattening  
✅ Promise execution pipeline  
✅ Microtask queue behavior  
✅ Promise.all  
✅ Promise.allSettled  
✅ Promise.race  
✅ Promise.any  
✅ Common production bugs  
✅ Advanced interview questions  

---

## Key Takeaways

1. **Promise solves callback hell** — provides readable async handling
2. **State is immutable** — changes only once
3. **.then() returns new promise** — enables chaining
4. **Always return from .then()** — breaks chain otherwise
5. **Microtask queue is priority** — promises execute before setTimeout
6. **Promise flattening is automatic** — nested promises unwrap
7. **Choose right combinator** — all, allSettled, race, any fit different needs
8. **Error handling is crucial** — use .catch() always
9. **Follow best practices** — avoid common production bugs
10. **Master interview questions** — understand execution flow deeply
# Async / Await Complete Notes (Namaste JavaScript + Deep Understanding)

## 1. What is async?

-   `async` is a keyword used before a function.
-   It makes the function return a Promise.

### Example:

``` js
async function getData() {
    return "Hello";
}
```

Return value is actually:

``` js
Promise.resolve("Hello")
```

------------------------------------------------------------------------

## 2. What is await?

-   `await` pauses async function execution until Promise resolves.
-   It can ONLY be used inside async function.

Example:

``` js
async function getData() {
    let data = await fetch("url");
    console.log(data);
}
```

------------------------------------------------------------------------

## 3. Important Truth: Promise starts immediately

Promise starts when created, NOT when awaited.

Example:

``` js
const p = new Promise(res => {
    console.log("Promise started");
    res("Done");
});
```

Output immediately:

    Promise started

------------------------------------------------------------------------

## 4. How async/await works internally

Steps:

1.  async function starts
2.  await encountered
3.  function pauses
4.  function removed from call stack
5.  Promise resolves
6.  function resumes via Microtask Queue

Important: JS does NOT block thread.

------------------------------------------------------------------------

## 5. Execution Flow Diagram

Call Stack → WebAPI → Microtask Queue → Event Loop → Call Stack

------------------------------------------------------------------------

## 6. Example with explanation

``` js
async function test() {
    console.log("A");
    await Promise.resolve();
    console.log("B");
}
console.log("C");
test();
console.log("D");
```

Output:

    C
    A
    D
    B

Explanation: - await creates microtask

------------------------------------------------------------------------

## 7. Promise vs async/await

Promise style:

``` js
fetch(url)
.then(res => res.json())
.then(data => console.log(data))
```

async/await style:

``` js
const res = await fetch(url);
const data = await res.json();
console.log(data);
```

Better readability.

------------------------------------------------------------------------

## 8. Error handling

Using try/catch:

``` js
try {
   const data = await fetch(url);
} catch(err) {
   console.log(err);
}
```

------------------------------------------------------------------------

## 9. Important Interview Concepts

Remember:

-   async always returns Promise
-   await pauses function
-   await creates microtask
-   Promise executor runs immediately
-   Microtask has higher priority than setTimeout

Priority order:

1.  Call stack
2.  Microtask queue
3.  Task queue

------------------------------------------------------------------------

## 10. Tricky Interview Questions with Solutions

### Question 1

``` js
console.log("Start");

async function test() {
  console.log("A");
  await Promise.resolve();
  console.log("B");
}

test();
console.log("End");
```

Answer:

    Start
    A
    End
    B

Reason: await moves continuation to Microtask Queue.

------------------------------------------------------------------------

### Question 2

``` js
console.log("Start");

setTimeout(() => console.log("Timeout"), 0);

Promise.resolve().then(() => console.log("Promise"));

console.log("End");
```

Answer:

    Start
    End
    Promise
    Timeout

Reason: Microtask runs before Task Queue.

------------------------------------------------------------------------

### Question 3

``` js
const p = new Promise(res => {
   console.log("Executor");
   res("Done");
});

async function test() {
   const val = await p;
   console.log(val);
}

test();
```

Answer:

    Executor
    Done

Reason: Executor runs immediately.

------------------------------------------------------------------------

## 11. Key Golden Rules

Rule 1: Promise executor runs immediately.

Rule 2: await pauses function, not Promise.

Rule 3: await always creates microtask.

Rule 4: Microtask runs before setTimeout.

------------------------------------------------------------------------

## 12. Real-world fetch example

``` js
async function getUser() {
   const res = await fetch("https://api.github.com/users/octocat");
   const data = await res.json();
   console.log(data);
}
```

------------------------------------------------------------------------

## 13. Summary

async → returns Promise

await → pauses function

Promise executor → runs immediately

Microtask → higher priority

async/await → syntactic sugar over Promise

------------------------------------------------------------------------

END OF NOTES

