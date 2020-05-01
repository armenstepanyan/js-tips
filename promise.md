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
