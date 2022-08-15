### RxJS
RxJS is a library for composing asynchronous and event-based programs by using observable sequences. It provides one core type, the Observable, satellite types (Observer, Schedulers, Subjects) and operators inspired by 
Array methods (map, filter, reduce, every, etc) to allow handling asynchronous events as collections.

The essential concepts in RxJS which solve async event management are:

- **Observable:** represents the idea of an invokable collection of future values or events.
- **Observer:** is a collection of callbacks that knows how to listen to values delivered by the Observable.
- **Subscription:** represents the execution of an Observable, is primarily useful for cancelling the execution.
- **Operators:** are pure functions that enable a functional programming style of dealing with collections with operations like map, filter, concat, reduce, etc.
- **Subject:** is equivalent to an EventEmitter, and the only way of multicasting a value or event to multiple Observers.
- **Schedulers:** are centralized dispatchers to control concurrency, allowing us to coordinate when computation happens on e.g. 
- setTimeout or requestAnimationFrame or others.


### Observables
Observables are lazy Push collections of multiple values. 
```
import { Observable } from 'rxjs';

const observable = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  setTimeout(() => {
    subscriber.next(4);
    subscriber.complete();
  }, 1000);
});

console.log('just before subscribe');
observable.subscribe({
  next(x) { console.log('got value ' + x); },
  error(err) { console.error('something wrong occurred: ' + err); },
  complete() { console.log('done'); }
});
console.log('just after subscribe');
```

Output
```
just before subscribe
got value 1
got value 2
got value 3
just after subscribe
got value 4
done
```
Observables are like functions with zero arguments, but generalize those to allow multiple values.
```
function foo() {
  console.log('Hello');
  return 42;
}

const x = foo.call(); // same as foo()
console.log(x);
const y = foo.call(); // same as foo()
console.log(y);
```

Subscribing to an Observable is analogous to **calling** a Function.
```
console.log('before');
console.log(foo.call());
console.log('after');

const foo = new Observable(subscriber => {
  console.log('Hello');
  subscriber.next(42);
});
 
foo.subscribe(x => {
  console.log(x);
});
foo.subscribe(y => {
  console.log(y);
});

```
Conclusion:
- func.call() means "give me one value synchronously"
- observable.subscribe() means "give me any amount of values, either synchronously or asynchronously"

### Observer
 An Observer is a consumer of values delivered by an Observable. 
 Observers are simply a set of callbacks, one for each type of notification delivered by the Observable: `next`, `error`, and `complete`
 ```
 const observer = {
  next: x => console.log('Observer got a next value: ' + x),
  error: err => console.error('Observer got an error: ' + err),
  complete: () => console.log('Observer got a complete notification'),
};
observable.subscribe(observer);
 ```
### Subscription
A Subscription is an object that represents a disposable resource, usually the execution of an Observable. 
A Subscription has one important method, **unsubscribe**, that takes no argument and just disposes the resource held by the subscription

### Subject
An RxJS **Subject** is a special type of Observable that allows values to be multicasted to many Observers. 
While plain Observables are **unicast** (each subscribed Observer owns an independent execution of the Observable), Subjects are **multicast**.

### BehaviorSubject
One of the variants of Subjects is the BehaviorSubject, which has a notion of `the current value`. 
It stores the latest value emitted to its consumers, and whenever a new Observer subscribes, 
it will **immediately** receive the "current value" from the BehaviorSubject.
```
import { BehaviorSubject } from 'rxjs';
const subject = new BehaviorSubject(0); // 0 is the initial value

subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});

subject.next(1);
subject.next(2);

// last emitted values is 2
subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});

subject.next(3);
// any time we can get last emmited value
console.log(subject.value);
```
Output
First value will be **initial/last emitted** value of BehaviorSubject
```
observerA: 0
observerA: 1
observerA: 2
// observerB starts work with last emitted value
observerB: 2
observerA: 3
observerB: 3
```


### ReplaySubject
A ReplaySubject is similar to a BehaviorSubject in that it can send old values to new subscribers, but it can also record a part of the Observable execution.
When creating a ReplaySubject, you can specify how many values to replay:
```
import { ReplaySubject} from 'rxjs';
const subject = new ReplaySubject(3); // buffer 3 values for new subscribers

subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});

subject.next(10);
subject.next(20);
subject.next(30);
subject.next(40);

subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});

subject.next(5);
```
Output
```
observerA: 10
observerA: 20
observerA: 30
observerA: 40
// here observerB starts work and get last 3 emitted values
observerB: 20
observerB: 30
observerB: 40

observerA: 5
observerB: 5
```
Same example with `BehaviorSubject`
```
import { ReplaySubject, BehaviorSubject} from 'rxjs';
const subject = new BehaviorSubject(3); // buffer 3 values for new subscribers

subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});

subject.next(10);
subject.next(20);
subject.next(30);
subject.next(40);

subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});

subject.next(5);
```
Output
```
observerA: 3
observerA: 10
observerA: 20
observerA: 30
observerA: 40
// observerB starts work and get only last emitted value (40)
observerB: 40
observerA: 5
observerB: 5
```


### AsyncSubject
The AsyncSubject is a variant where only the last value of the Observable execution is sent to its observers, and only when the execution completes.
```
import { AsyncSubject } from 'rxjs';
const subject = new AsyncSubject();

subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});

subject.next(1);
subject.next(2);
subject.next(3);
subject.next(4);

subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});

subject.next(5);
subject.complete();

// Logs:
// observerA: 5
// observerB: 5
```

### Hot and Cold observables
There are two types of observables: hot and cold. The main difference is that a cold observable creates a data producer for each subscriber,
 whereas a hot observable creates a data producer first, and each subscriber gets the data from one producer, starting from the moment of subscription.

