### switchmap
Map to observable, complete previous inner observable, emit values. The main difference between switchMap and other flattening operators is the cancelling effect. On each emission the previous inner observable (the result of the function you supplied) is cancelled and the new observable is subscribed. 
You can remember this by the phrase switch to a new observable.

```
import { interval, fromEvent } from 'rxjs';
import { switchMap } from 'rxjs/operators';

fromEvent(document, 'click')
.pipe(
  // restart counter on every click
  switchMap(() => interval(1000))
)
.subscribe(console.log);
```

[Stackblitz](https://stackblitz.com/edit/typescript-s4pvix?file=index.ts&devtoolsheight=80)

Show/hide menu using switchmap

```
const main =  <HTMLInputElement>document.getElementById('main');
const content =  <HTMLInputElement>document.getElementById('content');


 fromEvent(main, 'mouseenter')
 .pipe(   
   switchMap(event => {
     content.style.display = 'block';
     return fromEvent(main, 'mouseleave').pipe(debounceTime(500));
   })
   )
 .subscribe(val => {
  content.style.display = 'none';
});
```

[Stackblitz](https://stackblitz.com/edit/typescript-mouse-leave?embed=1&file=index.ts)
