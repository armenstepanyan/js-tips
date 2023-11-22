### Signals vs BefavoirSubjects

```typescript
count = signal(0);
count = new BehaviorSubject(0);
```

#### Accessing values

```html
<!-- signal -->
<p>{{ count() }}</p>

<!-- BehaviorSubject -->
<p>{{ count | async }}</p>
```

In class

```typescript
log(){
    console.log(this.count()) // signal
}

log(){
    console.log(this.count.value) // BehaviorSubject
}
```

#### Derived/Computed values

In case if we need to do some computations on given values

```typescript
count = signal(0);
doubleCount = computed(() => this.count() * 2);

count$ = new BehaviorSubject(0);
doubleCount$ = this.count$.pipe(map((count) => count * 2));
```

In this case `doubleCount$` turns into standard observable and this means we can longer access its value
directly, we have to subscribe to it.

if we want to access the value of our double count in the class with signals we can just do

```typescript
someMethod(){
    console.log(this.doubleCount())
}

```

With BehaviorSubject

```typescript
someMethod(){
    this.doubleCount$
        .pipe(takeUntil(this.destroy$))
        .subscribe((value) => console.log(value));
}

```

Also we need to add some kind of unsubscribe starategy to avoid memory leak.

#### Diamond problem

What if we want to combine multiple reactive values together into a new derived value.
Signals are quite straightforward. We just create a computed signal using whatever other signal values we want to use and whenever any of these signals update, the computed signal will update as well.

```typescript
valueOne = signal(1);
valueTwo = signal(2);

deriveredValue = computed(() => this.valueOne() * this.valueTwo());

changeValues(){
    this.valueOne.set(2);
    this.valueTwo.set(20);
}
```

When `changeValues` is called the `deriveredValue` will be `40`;

Things are starting to get a little more complex with rxjs now.
We have to combine 2 observables values with `combineLatest` creation operator.

```typescript
valueOne$ = new BehaviorSubject(1);
valueTwo$ = new BehaviorSubject(10);

deriveredValue$ = combineLatest([this.valueOne$, this.valueTwo$]).pipe(
    map(([one, two]) => one * two)
);

changeValues(){
    this.valueOne$.next(2);
    this.valueTwo$.next(20);
}
```

Every time any of the streams get new value, combineLatest depends on updates it
will emit a new value using the new value from the stream that was just updated .
But what if multiple streams are updated at once in this case `combineLatest` will emit a new value using the new value from one of the streams but
the old value from the other stream that updated then immediately after it will run again using both of the new values.

changed values with combineLatest for `deriveredValue$`

```
10 -> 20 -> 40
```

with signals

```
10 -> 40
```

#### Side effects

```typescript
myService = inject(Service);

count= this.myService.getCount();
doubleCount = computed(() => this.count() * 2);

constructor(){
    effect(() => {
        console.log("count:", this.count())
    })
}
```

With rxjs we can use `tap` operator

```typescript
myService = inject(Service);

count$ = this.myService
           .getCount();
           .pipe(
               tap(count => {
                   console.log(count)
               })
           );

doubleCount$ = this.count$.pipe(map(count => count * 2))


```

If we try to access the count value multiple times in the template by using the `async` pipe, it is
going to trigger the side effect every time we try to access the value.

```html
<p>{{ count | async }}</p>
<p>{{ count | async }}</p>
<p>{{ count | async }}</p>
```
