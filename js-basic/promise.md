A Promise is a proxy for a value not necessarily known when the promise is created. 
It allows you to associate handlers with an asynchronous action's eventual success value or failure reason. This lets asynchronous methods return values like synchronous methods: instead of immediately returning the final value, the asynchronous method returns a promise to supply the value at some point in the future.
A Promise is in one of these states:

* pending: initial state, neither fulfilled nor rejected.
* fulfilled: meaning that the operation completed successfully.
* rejected: meaning that the operation failed.

```
var promise = new Promise(function(resolve, reject) {
  // Make any async operation then call one of this functions
  // resolve(result) - if success 
  // reject(error) if error
}

promise.then(onFulfilled, onRejected)
```


Example with setTimeout
```
let promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    // set promise to fulfilled state with "result message"
    resolve("result");
    // or call reject('error')
  }, 1000);

});

promise
  .then(
    result => {
      alert("Fulfilled: " + result); // result - argument of resolve
    },
    error => {
      alert("Rejected: " + error); // error - argument of reject
    }
  )

```
## Load script using Promise
```
function loadScript(url) {
  return new Promise((resolve, reject) => {

    const script = document.createElement("script");
    script.src = url;

    script.onload = () => {
     // script is loaded successfully, call resolve()
      resolve("loaded");
    };

    script.onerror = () => {
      // script is not loaded, call reject()
      reject("error");
    };

    document.head.appendChild(script);
  });
}
```

Usage [Example on Stackblitz](https://stackblitz.com/edit/t-promise?embed=1&file=index.ts)

```
  loadScript(url)
    .then(
      result => {
        console.log("Success: ", result);
      },
      error => {
        console.log("Reject: ", error);
      }
    )
    .catch(error => {
      console.log("Catch error: ", error);
    });
```

Validate image
```
function validateImage(imageSrc: string) {
  return new Promise((resolve, reject) => {
    if (!imageSrc) {
      reject('invalid url');
      return;
    }
    const img = new Image();
    img.onload = () => {
      resolve('success');
    };
    img.onerror = () => {
      reject('error');
    };
    img.src = imageSrc;
  });
}

  validateImage(url)
    .then(
      success => {
        // image is loaded
      },
      error => {
        // image is not loaded
      }
    )
    .catch(error => {
      // Catch other errors like 404 or 500
      
    });

```

## Promise chain

```
new Promise(function(resolve, reject) {

  setTimeout(() => resolve(1), 1000); // (line 1)

}).then(function(result) { // (line 2)

  alert(result); // 1
  return result * 2;

}).then(function(result) { // (line 3)

  alert(result); // 2
  return result * 2;

}).then(function(result) {

  alert(result); // (line 4)
  return result * 2;

});
```
[Example](https://stackblitz.com/edit/t-promise2?embed=1&file=index.ts)

Here the flow is:

- The initial promise resolves in 1 second (line 1),
- Then the .then handler is called (line 2).
- The value that it returns is passed to the next .then handler (line 3)
- …and so on

As the result is passed along the chain of handlers, we can see a sequence of alert calls: 1 → 2 → 4

Instead of ` return result * 2;` we can return `Promise.resolve(result * 2)`
The whole thing works, because a call to promise.then returns a promise, so that we can call the next `.then` on it.

## Promise.all
Promise.all takes an array of promises (it technically can be any iterable, but is usually an array) and returns a new promise.
The new promise resolves when all listed promises are settled, and the array of their results becomes its result.
[Stackblitz example](https://stackblitz.com/edit/a-promise-all-settled?embed=1&file=index.js)

```
const promise = value => {
  return  new Promise((resolve) => {
  setTimeout(() => { 
    resolve(value); 
  }, value * 100);
})

}

const p1 = promise(20);
const p2 = promise(30);

Promise.all([p1, p2]).then(data => {
  console.log(data); // data = [20, 30] after 3 second
})
```

If any of the promises is rejected, the promise returned by Promise.all immediately rejects with that error.
```
Promise.all([
  new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
  new Promise((resolve, reject) => setTimeout(() => reject(new Error("Error!")), 500)),
  new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
]).catch(alert);
```
After 0.5 second will catch error `Error!`. In case of an error, other promises are ignored. **Note** this does not mean that fetch requests for example will be cancelled.
If one promise rejects, `Promise.all` immediately rejects, completely forgetting about the other ones in the list. Their results are ignored. `Promise.all` does nothing to cancel them, as there’s no concept of 'cancellation' in promises

**Note** The setTimeout will still works after Promise catch error. The thing is its never goes to `then()` block of `Promise.all(...)`

To handle error and continue Promise chain work need to add `catch` to promise and return error.

```
var p = Promise.all([
  Promise.resolve(1),
  Promise.resolve(2),
  Promise.reject("Error").catch(e => e), // catch error here
  new Promise((resolve) => {
    setTimeout(() => {
      resolve('async')
    }, 1500)
  })
]).then(data => {
// [1, 2, "Error", "async"]
  console.log(data)
}).catch(err => console.log(err))

```


## Promise.allSettled
Promise.all rejects as a whole if any promise rejects. That’s good for 'all or nothing' cases, when we need all results successful to proceed. Promise.allSettled just waits for all promises to settle, regardless of the result. The resulting array has:

- {status:"fulfilled", value:result} for successful responses,
- {status:"rejected", reason:error} for errors.

[Stackblitz Example](https://stackblitz.com/edit/a-promise-all-settled?file=index.js)
