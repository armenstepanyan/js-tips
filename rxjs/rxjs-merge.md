### merge
Turn multiple observables into a single observable.

```
//emit every 1 seconds
const first = interval(1000);
//emit every 2 seconds
const second = interval(2000);

const example = merge(
  first.pipe(mapTo('First')),
  second.pipe(mapTo('Second')),
);
example.subscribe(val => console.log(val));
```

Output
```
First
First
Second
First
First
Second
...
```
[Stackblitz example](https://stackblitz.com/edit/a-rxjs-merge?file=index.ts&devtoolsheight=60)

Count total clicks on button

```
const btnOne$ = fromEvent(document.getElementById('btn1'), 'click');
const btnTwo$ = fromEvent(document.getElementById('btn2'), 'click');

let clicks = 0;

merge(btnOne$, btnTwo$).subscribe(value => {
  ++clicks;
  document.getElementById('total').innerText = '' + clicks;
})
```
[Stackblitz example](https://stackblitz.com/edit/a-rxjs-merge2?file=index.ts&devtoolsheight=60)

