```
  from(["1", "2", "3", "4", "5"])
    .pipe(
      switchMap(value => {
        if (value === "3") {
          throw "Limit Error";
        }
        console.log("from data", value);
        return from(value);
      }),
      tap({
        next: data => {
          console.log("tap ", data);
        },
        error: err => {
          console.log("error in tap:", err);
        }
      }),
      catchError(err => {
        console.log("Error in catch: ", err);
        return of("error from catch:" + err);
      })
    )
    .subscribe({
      next: val => {
        console.log("subscribe", val);
      },
      error: err => {
        console.log("subscribe - error:", err);
      }
    });
```
Errors will be catched in `tap.error`, `subscribe.error`. Output:
```
from data 1
tap 1
subscribe 1

from data 2
tap 2
subscribe 2

error in tap: Limit Error
Error in catch:Limit Error
subscribe error from catch: Limit Error
```
[Stackblitz](https://stackblitz.com/edit/rxjs-throw-tap?devtoolsheight=33&file=index.ts)
