### Subject
An RxJS Subject is a special type of Observable that allows values to be multicasted to many Observers. While plain Observables are unicast (each subscribed Observer owns an independent execution of the Observable), 
Subjects are multicast.

```
import { Subject } from "rxjs";

const source$ = new Subject();
source$.subscribe(val => console.log(val));
source$.next(1);
source$.next(2);
```

Output
```
1
2
```
*Note* If we put `source$.subscribe` after `source$.next(1)` we will not see any messages.

Completing subject with `complete()` function. After calling `complete` subscribers will not receive any data.
```
source$.next(1);
source$.next(2);
source$.complete(); // <----
source$.next(4);
source$.next(5);
```

### BehaviorSubject
Requires an initial value and emits the current value to new subscribers.
```
import { BehaviorSubject } from "rxjs";

const behaviorSource$ = new BehaviorSubject(0);

behaviorSource$.subscribe(val => console.log(val));
behaviorSource$.next(10);
behaviorSource$.next(20);
console.log("last value:", behaviorSource$.value);
```
Output
```
0 <-- initial value
10
20
last value: 20
```
If we subscribe again aftyer `next(vale)`, we will see last emitted value of `BehaviorSubject`
```
behaviorSource$.subscribe(val => console.log(val));
behaviorSource$.next(10);
behaviorSource$.next(20);
console.log("last value:", behaviorSource$.value);

behaviorSource$.subscribe(val => console.log('Second ',val));
```
Output
```
0 // initial data
10
20
last value: 20
Secound 20
```

### AsyncSubject
Emits its last value on completion
```
const asyncSubject = new AsyncSubject();
asyncSubject.subscribe(x => console.log('async',x)); // first subscriber

asyncSubject.next(1);
asyncSubject.next(2);

asyncSubject.subscribe(x => console.log('async',x)); // second subscriber

asyncSubject.complete(); 
```
After calling `complte()` in console we will see last emitted value 2 times (for each subscriber)
```
async 2
async 2
```

[Stakblitz](https://stackblitz.com/edit/a-rxjs-subject?devtoolsheight=40&file=index.ts)
