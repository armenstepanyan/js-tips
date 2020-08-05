### combineLatest
When any observable emits a value, emit the last emitted value from each

```
// timerOne emits first value at 1s, then once every 1s
const timerOne$ = timer(1000, 1000);

// timerTwo emits first value at 1s, then once every 4s
const timerTwo$ = timer(1000, 4000);
```

Set filter function to emit values when that pass the provided condition

```
let clicks = 0;

fromEvent(document.getElementById('btn'), 'click').pipe(tap( val => {
  ++clicks;
})).subscribe()
```

When one timer emits, emit the latest values from each timer as an array
```
combineLatest([timerOne$, timerTwo$])
.pipe(filter(val => clicks % 2 == 0))
.subscribe(([ timerValOne, timerValTwo ]) => {
  console.log(
    `Timer One Latest: ${timerValOne}`,
    `Timer Two Latest: ${timerValTwo}`,
    )
});

```

Html
```
<button id="btn">Toggle</button>
```

Output
```
[1, 0]
[2, 0]
[3, 0]
[4, 0]  // (1)
[4, 1]  // (2)
[5, 1]
[6, 1]
[7, 1]
[8, 1] // (3)
[8, 2] // (4)
[9, 2]
....
```
Lines `(1)` and `(2)` will works in same time, because in this time both observables emits new value, we see `[4, 0]` and `[4,1]`. Same behavior at lines `(3)` and `(4)`

Note: Timers will still works when we toggle the filter.

[Stackblitz Example](https://stackblitz.com/edit/a-combinelatest)

### Combine inputs changes
```
const name$ = fromEvent(document.getElementById('name'), 'input').pipe(map(event => event.target.value));
const lastname$ = fromEvent(document.getElementById('lastname'), 'input').pipe(map(event => event.target.value));

combineLatest([name$, lastname$]).subscribe({
  next: ([name, lastname]) => {
    console.log(name, lastname);
    document.getElementById('full-name').innerText = `${name} ${lastname}`;
  }
})
```
Html 
```
<p>
  <input type="text" id="name" placeholder="Name" autocomplete="off">
</p>
<p>
  <input type="text" id="lastname" placeholder="Lastname" autocomplete="off">
</p>
<p>
  Full name <b id="full-name"></b>
</p>
```
**Note** We will see combined value of 2 inputs only when each observable emit at least one value. If we type in first input, we will not see result, we must type in second input also to see result.
[Stackblitz Example](https://stackblitz.com/edit/a-combinelatest2)
