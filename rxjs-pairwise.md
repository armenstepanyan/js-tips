### pairwise
Emit the previous and current values as an array.  Puts the current value and previous value together as an array, and emits that.

```
import { of, fromEvent } from "rxjs";
import { map, pairwise } from "rxjs/operators";

const click$ = fromEvent(document, "click");

click$.pipe(
  map(event => event.clientX),
  pairwise(),
  ).subscribe({
  next: pairValues => {
    console.log(pairValues);
  }
});

```

After first click we will not see any result. `pairwise` waits for seconds emit.The Nth emission from the source Observable will cause the output 
Observable to emit an array [(N-1)th, Nth] of the previous and the current value, as a pair. For this reason, `pairwise` emits 
on the second and subsequent emissions from the source Observable, but not on the first emission, because * there is no previous value in that case.

To see result at first click we can use `startWith` operator.

```
const click$ = fromEvent(document, "click");

click$.pipe(
  map(event => event.clientX),
  startWith(0),
  pairwise(),
  ).subscribe({
  next: pairValues => {
    console.log(pairValues);
  }
});
```

Output will be 
```
[0, 65]
[65, 211]
[211, 77]
[77, 228]
[228, 393]
.....
```
[Stackblitz Example](https://stackblitz.com/edit/a-rxjs-pairwise?file=index.ts)
