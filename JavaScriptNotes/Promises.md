# JavaScript Promises -- Complete Notes

## 1. Why Promises Exist

JavaScript is single-threaded and synchronous by default. But some tasks
take time:

-   Network requests (fetch)
-   setTimeout / setInterval
-   File system operations
-   Database calls

We need a way to handle these asynchronous operations safely and
predictably.

------------------------------------------------------------------------

## 2. What is a Promise?

A Promise is:

> An object that represents the eventual completion (or failure) of an
> asynchronous operation and its resulting value.

It is basically a **container for a future value**.

------------------------------------------------------------------------

## 3. Promise States

A Promise has 3 states:

1.  Pending -- initial state
2.  Fulfilled -- operation successful
3.  Rejected -- operation failed

A Promise can settle (fulfilled/rejected) only once.

------------------------------------------------------------------------

## 4. Creating a Promise

``` js
const p = new Promise((resolve, reject) => {
  const success = true;

  if (success) {
    resolve("Done");
  } else {
    reject("Error");
  }
});
```

-   resolve(value) → moves promise to fulfilled
-   reject(error) → moves promise to rejected

------------------------------------------------------------------------

## 5. Consuming a Promise

``` js
p.then(result => {
  console.log(result);
}).catch(error => {
  console.log(error);
}).finally(() => {
  console.log("Always runs");
});
```

-   .then() → handles success
-   .catch() → handles failure
-   .finally() → runs regardless

------------------------------------------------------------------------

## 6. Important Rule -- Always Return

If you want chaining to work properly:

``` js
.then(value => {
  return value * 2;   // MUST return
})
```

If you don't return anything:

``` js
.then(value => {
  value * 2;   // returns undefined
})
```

The next `.then()` receives `undefined`.

------------------------------------------------------------------------

## 7. Promise Resolution Behavior

Inside `.then()`:

  You return               What next .then() receives
  ------------------------ ----------------------------
  value                    value
  Promise.resolve(value)   value
  new Promise(...)         resolved value
  throw error              goes to catch
  Promise.reject(error)    goes to catch

This is called **Promise Flattening**.

------------------------------------------------------------------------

## 8. Returning vs Not Returning setTimeout

Wrong:

``` js
.then(x => {
  setTimeout(() => {
    return x * 2;
  }, 1000);
})
```

This returns `undefined` immediately.

Correct:

``` js
.then(x => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x * 2);
    }, 1000);
  });
})
```

Now the chain waits properly.

------------------------------------------------------------------------

## 9. Error Propagation

``` js
Promise.resolve(1)
  .then(x => x + 1)
  .then(x => { throw new Error("Fail"); })
  .catch(err => console.log(err.message));
```

When an error is thrown inside `.then()`:

-   Promise becomes rejected
-   Control jumps to nearest `.catch()`

------------------------------------------------------------------------

## 10. Microtask vs Macrotask

Execution priority:

1.  Call Stack
2.  Microtask Queue (Promises)
3.  Macrotask Queue (setTimeout)

Example:

``` js
console.log("1");

setTimeout(() => console.log("2"));

Promise.resolve().then(() => console.log("3"));

console.log("4");
```

Output:

1 4 3 2

------------------------------------------------------------------------

## 11. Promise Static Methods

### Promise.all()

-   Runs multiple promises in parallel
-   Fails if any one fails

### Promise.race()

-   First settled promise wins

### Promise.allSettled()

-   Waits for all, regardless of success/failure

### Promise.any()

-   First fulfilled promise wins

------------------------------------------------------------------------

## 12. Common Production Bugs

1.  Forgetting to return inside `.then()`
2.  Mixing callbacks and promises incorrectly
3.  Not handling rejection
4.  Using Promise.all with heavy parallel requests (can overload server)

------------------------------------------------------------------------

# HARD QUESTION (Interview Level)

What will this print and why?

``` js
Promise.resolve("Start")
  .then(val => {
    console.log(val);
    return Promise.resolve("Middle");
  })
  .then(val => {
    console.log(val);
    return new Promise(resolve => {
      setTimeout(() => resolve("End"), 0);
    });
  })
  .then(val => {
    console.log(val);
  });

console.log("Outside");
```

Explain:

1.  Exact output order
2.  Why that order happens
3.  Role of microtask vs macrotask
4.  Why Promise flattening matters here
---
# Promise Execution Flow -- Deep Analysis

## Code Under Discussion

``` js
Promise.resolve("Start")
  .then(val => {
    console.log(val);
    return Promise.resolve("Middle");
  })
  .then(val => {
    console.log(val);
    return new Promise(resolve => {
      setTimeout(() => resolve("End"), 0);
    });
  })
  .then(val => {
    console.log(val);
  });

console.log("Outside");
```

------------------------------------------------------------------------

# ✅ Final Output

    Outside
    Start
    Middle
    End

------------------------------------------------------------------------

# 📌 Step-by-Step Execution Explanation

## 1️⃣ Synchronous Phase

JavaScript executes line by line.

-   `Promise.resolve("Start")` creates a resolved promise.
-   `.then()` callbacks are pushed into the **Microtask Queue**.
-   `console.log("Outside")` runs immediately.

Output so far:

    Outside

------------------------------------------------------------------------

## 2️⃣ Microtask Queue Starts

### First `.then()` runs

Receives:

    "Start"

Prints:

    Start

Returns:

    Promise.resolve("Middle")

Promise Resolution Procedure automatically unwraps it.

------------------------------------------------------------------------

## 3️⃣ Second `.then()` runs

Receives:

    "Middle"

Prints:

    Middle

Returns:

``` js
new Promise(resolve => {
  setTimeout(() => resolve("End"), 0);
});
```

Now:

-   `setTimeout` goes to **Macrotask Queue**
-   The Promise remains pending until timeout resolves

------------------------------------------------------------------------

## 4️⃣ Event Loop Continues

After microtasks finish:

Macrotask Queue runs `setTimeout` callback.

It resolves with:

    "End"

------------------------------------------------------------------------

## 5️⃣ Third `.then()` runs (Microtask)

Receives:

    "End"

Prints:

    End

------------------------------------------------------------------------

# 📊 Execution Flow Chart

    CALL STACK
       ↓
    Promise.resolve("Start")
       ↓
    Microtask Queue (.then callbacks scheduled)
       ↓
    console.log("Outside")  →  Outside
       ↓
    ----------------------------
    Microtask Phase Begins
    ----------------------------
    1st then → prints Start
       ↓
    returns Promise.resolve("Middle")
       ↓
    2nd then → prints Middle
       ↓
    returns new Promise (pending)
       ↓
    ----------------------------
    Macrotask Phase
    ----------------------------
    setTimeout resolves "End"
       ↓
    Microtask Queue
       ↓
    3rd then → prints End

------------------------------------------------------------------------

# 🔥 Important Concepts Used Here

  Concept              Explanation
  -------------------- ---------------------------------------
  Promise.resolve      Creates already fulfilled promise
  Promise Flattening   Nested promises automatically unwrap
  Microtask Queue      All `.then()` callbacks go here
  Macrotask Queue      `setTimeout` callbacks go here
  Event Loop           Executes microtasks before macrotasks

------------------------------------------------------------------------

# 🧠 Key Takeaways

1.  Synchronous code runs first.
2.  Microtasks (Promises) run before macrotasks (setTimeout).
3.  Returning `Promise.resolve()` behaves same as returning normal
    value.
4.  Returning a new Promise makes the chain wait.
5.  Promise resolution automatically unwraps nested promises.

------------------------------------------------------------------------

# 🎯 Hard Follow-Up Question

What will this print and why?

``` js
Promise.resolve("A")
  .then(val => {
    console.log(val);
    return new Promise(resolve => {
      resolve(
        Promise.resolve("B")
      );
    });
  })
  .then(val => {
    console.log(val);
  });

console.log("C");
```

Explain:

1.  Exact output order
2.  Why nested Promise inside resolve still flattens
3.  Event loop reasoning

