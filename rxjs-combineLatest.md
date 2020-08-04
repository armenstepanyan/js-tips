### combineLatest
When any observable emits a value, emit the last emitted value from each

```
// timerOne emits first value at 1s, then once every 2s
const timerOne$ = timer(1000, 2000);

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

Note: Timers will still works when we toggle the filter.

[Example](https://stackblitz.com/edit/a-combinelatest)
