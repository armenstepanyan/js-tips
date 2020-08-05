### mergeMap

This operator is best used when you wish to flatten an inner observable but want to manually control the number of inner subscriptions

```
const name$ = fromEvent(document.getElementById('name'), 'input');
const lastname$ = fromEvent(document.getElementById('lastname'), 'input');

name$.pipe(mergeMap(event1 => {
   return lastname$.pipe(map(event2 => event1.target.value + ' ' + event.target.value))
})).subscribe({
  next: combinedValue => {
    document.getElementById('full-name').innerText = combinedValue;
  }
})
```
We will get combined value only when we type in second (inner) input. 

**Note:** If we type in first input 3 characters for exmaple, then in second input, we will see 3 `log` messages. And for this case better to ise `switchMap`  operator.

[Stackblitz Example](https://stackblitz.com/edit/a-mergemap)
