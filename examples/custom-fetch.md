### Cancelable fetch

```
(function () {
    const request = () => {

        let args = "";
        let currentMethod = "GET";
        let abortController = new AbortController();

        const utils = {
            subscribe: (callbackObj) => {
                const { success, error } = callbackObj || {};
                let request$ = null;
                if (currentMethod === "REQUEST") {
                    const [route, params] = args;
                    request$ = fetch(route, { ...params, signal: abortController.signal })
                } else {
                    const [route, bodyContent, headers = { "Content-Type": "application/json" }] = args;
                    request$ = fetch(route, {
                        method: currentMethod,
                        headers,
                        ...(currentMethod !== "GET" && { body: bodyContent }),
                        signal: abortController.signal
                    })
                }

                request$.then(async statusResp => {
                    let resp;
                    let obj = {};
                    try {
                        resp = await statusResp.clone().json();
                        obj = { success: statusResp.ok, data: resp, statusCode: statusResp.status, headers: statusResp.headers };
                    } catch (e) {
                        resp = await statusResp.text();
                        obj = { success: statusResp.ok, data: resp, statusCode: statusResp.status, headers: statusResp.headers };
                    }
                    if (obj.success) {
                        success && success(obj);
                    } else {
                        error && error(obj);
                    }

                }).catch(e => {
                    error && error({ success: false, statusCode: 0, data: `Unable to fetch ${e}` });
                })
                return {
                    cancel: utils.cancel
                }
            },
            cancel: () => {
                abortController.abort();
            }
        }

        const requests = {
            get: (...arguments) => {
                args = arguments;
                currentMethod = "GET"
                return {
                    subscribe: utils.subscribe
                }
            },
            post: (...arguments) => {
                args = arguments;
                currentMethod = "POST"
                return {
                    subscribe: utils.subscribe
                }
            },
            put: (...arguments) => {
                args = arguments;
                currentMethod = "PUT"
                return {
                    subscribe: utils.subscribe
                }
            },
            delete: (...arguments) => {
                args = arguments;
                currentMethod = "DELETE"
                return {
                    subscribe: utils.subscribe
                }
            },
            request: (...arguments) => {
                args = arguments;
                currentMethod = "REQUEST"
                return {
                    subscribe: utils.subscribe
                }
            },

        }
        return requests;
    }

    window.customFetch = request;
})();
```

Simple usage
```
customFetch().get("https://jsonplaceholder.typicode.com/invalid").subscribe()
```

With callbacks
```
customFetch().get("https://jsonplaceholder.typicode.com/posts/1").subscribe({
    success: (res) => {
        console.log("success", res)
    },
    error: (err) => {
        console.log("error", err)
    }
})
```

POST request
```
customFetch()
    .post("https://jsonplaceholder.typicode.com/posts", JSON.stringify({ a: 1 }))
    .subscribe({
        success: (res) => {
            console.log(res)
        }, error: (error) => {
            console.log(error)
        }
    });
```

POST request with FormData
```
const formData = new FormData();
const fileField = document.querySelector('input[type="file"]');

formData.append('username', 'abc123');
formData.append('avatar', fileField.files[0]);

customFetch().post("https://jsonplaceholder.typicode.com/posts/1", formData).subscribe({
    error: (resp) => {
        console.log("file", resp)
    }
})
```

Cancell request
```
const request = customFetch().get("https://jsonplaceholder.typicode.com/posts/1").subscribe();
requests.cancel();
```

Simple fetch request
```
customFetch().request("https://jsonplaceholder.typicode.com/posts/1", {
    method: "GET",
    withCredentials: true,
    redirect: 'follow',
    cache: 'no-cache',
}).subscribe(() => {

}, error => {
    console.log("errorrr", error)
})
```
[Stackblitz](https://stackblitz.com/edit/js-custom-fetch?file=index.js)
