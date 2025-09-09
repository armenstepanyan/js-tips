### async
After calling `async` function return `Promise`. Async functions can contain zero or more await expressions. Await expressions suspend progress through an async function, yielding control and subsequently resuming progress only when an awaited promise-based asynchronous operation is either fulfilled or rejected. The resolved value of the promise is treated as the return value of the await expression. Use of async / await enables the use of ordinary `try / catch` blocks around asynchronous code.

```ts
async function foo() {
   return 1
}
```

is equivalent to:

```ts
function foo() {
   return Promise.resolve(1)
}
```

The body of an `async` function can be thought of as being split by zero or more await expressions. 
Top-level code, up to and including the first await expression (if there is one), is run synchronously. 
In this way, an async function without an await expression will run synchronously. 
If there is an await expression inside the function body, however, the async function will always complete asynchronously.

For example:
```ts
async function foo() {
   await 1
}
```

is equivalent to:
```ts
function foo() {
   return Promise.resolve(1).then(() => undefined)
}
```

Error handling
```ts
async function foo() {
   const p1 = new Promise((resolve) => setTimeout(() => resolve('1'), 1000))
   const p2 = new Promise((_,reject) => setTimeout(() => reject('Error'), 500))   
   //const results = [await p1, await p2] // Do not do this! Use Promise.all or Promise.allSettled instead.
   return Promise.allSettled([p1, p2])
}

foo().then(result => {
  console.log(result)
}).catch(error => {
  console.log(error)
}) 
```
[Stackblitz Example](https://stackblitz.com/edit/a-async-2?file=index.js)

### Example

```ts
const resolveAfterDelay = delay => {
  return new Promise((resolve, reject) => {
    console.log('in promise');
    setTimeout(() => {
      console.log(`resolved after ${delay} second`);
      resolve(delay);
    }, delay);
  });
};
```

Create 2 async functions
```ts
async function sum1(val) {
  const a = resolveAfterDelay(2000);
  const b = resolveAfterDelay(3000);
  return val + (await a) + (await b);
}

async function sum2(val) {
  const a = await resolveAfterDelay(2000);
  console.log("a calculated");
  const b = await resolveAfterDelay(3000);
  console.log("b calculated");
  return val + a + b;
}
```

For function `sum1` after 3 second (max of 2 promises) we will see total result
```ts
in promise // show immediately
in promise // show immediately
resolved after 2000 second  // show after 2 second
resolved after 3000 second  // show after 3 second 
total 6000 // total spended time - 3 second
```

For function `sum2` total result will be 6000 after 5 second 
```ts
in promise // show immediately
resolved after 2000 second  // show after 2 second
a calculated // show with previous line
in promise // start work second promise
resolved after 3000 second // after 3 second from previous line
b calculated
total 6000
```
[Stackblitz Example](https://stackblitz.com/edit/a-async?file=index.js)

