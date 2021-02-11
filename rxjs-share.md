### Share
Share source among multiple subscribers.

```
import { timer } from 'rxjs';
import { tap, mapTo, share } from 'rxjs/operators';

//emit value in 1s
const source = timer(1000);
//log side effect, emit result
const example = source.pipe(
  tap(() => console.log('***SIDE EFFECT***')),
  mapTo('***RESULT***')
);

const subscribe = example.subscribe(val => console.log(val));
const subscribeTwo = example.subscribe(val => console.log(val));
```

NOT SHARED, SIDE EFFECT WILL BE EXECUTED TWICE. Output
```
***SIDE EFFECT***
***RESULT***

***SIDE EFFECT***
***RESULT***
```
SHARED, SIDE EFFECT EXECUTED ONCE
```
//share observable among subscribers
const sharedExample = example.pipe(share());

const subscribeThree = sharedExample.subscribe(val => console.log(val));
const subscribeFour = sharedExample.subscribe(val => console.log(val));
```

Output
```
***SIDE EFFECT***

***RESULT***
***RESULT***
```
[Stackblitz](https://stackblitz.com/edit/typescript-dlaa1p?devtoolsheight=33&file=index.ts)


Sometimes we need to make cold observable behave as hot, for example with http requests. Consider following http request example in Angular
```
ngOnInit() {
   this.user$ = this.http.get(`api/user/1`)
   this.name$ = this.user$.pipe(
      map(user => user.name)
   );
   this.age$ = this.user$.pipe(
      map(user => user.age)
   );
}
```
And we are displaying user’s name and age in the template using async pipe (assume in different places so wrapping it in one async pipe is not possible)

```
<div>{{name$ | async}}</div>
<div>{{age$ | async}}</div>
```

In browser’s network tab we will see two requests. The reason is that Angular’s Http creates cold observable so each new subscriber is equal to new request. 
Solving this actually is really easy. All we need to do is to add `share()` or `publish()`, `refCount()`

```
this.user$ = this.http.get(`api/user/1`).pipe(
  share()
);
//or 
this.user$ = this.http.get(`api/user/1`).pipe(
 publish(),
 refCount()
);
```
And now in network tab we have one request because the data was shared among all subscribers.
