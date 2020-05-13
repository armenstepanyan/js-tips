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
