### forkJoin
When all observables complete, emit the last emitted value from each.

This operator is best used when you have a group of observables and only care about the final emitted value of each. 
One common use case for this is if you wish to issue multiple requests on page load (or some other event) and only want to take action when a response has been received for all.
In this way it is similar to how you might use Promise.all

```
forkJoin(
  ajax.getJSON('https://api.github.com/users/armenstepanyan'), 
  ajax.getJSON('https://api.github.com/users/microsoft'), 
  ajax.getJSON('https://api.github.com/users')
)
// [{…}, {…}, Array(30)]
.subscribe(console.log);
```

As of RxJS 6.5+ we can use a dictionary of sources

```
forkJoin(
  {
    google: ajax.getJSON('https://api.github.com/users/google'),
    microsoft: ajax.getJSON('https://api.github.com/users/microsoft'),
    users: ajax.getJSON('https://api.github.com/users')
  }
)
  // { google: object, microsoft: object, users: array }
  .subscribe(console.log);
```

We will get same result by using `combineLatest`

```
combineLatest([
  ajax.getJSON('https://api.github.com/users/armenstepanyan'), 
  ajax.getJSON('https://api.github.com/users/microsoft'), 
  ajax.getJSON('https://api.github.com/users')
  ]).subscribe({
    next: data => {
    // [{…}, {…}, Array(30)]
     console.log(data)
    }
  })
```
[Example on Stackblitz](https://stackblitz.com/edit/a-forkjoin)

### Example 2
Observables completing after different durations
```
const myPromise = val =>
  new Promise(resolve =>
    setTimeout(() => resolve(`Promise Resolved: ${val}`), 5000)
  );

/*
  when all observables complete, give the last
  emitted value from each as an array
*/
const example = forkJoin(
  //emit 'Hello' immediately
  of('Hello'),
  //emit 'World' after 1 second
  of('World').pipe(delay(1000)),
  //emit 0 after 1 second
  interval(1000).pipe(take(1)),
  //emit 0...1 in 1 second interval
  interval(1000).pipe(take(2)),
  //promise that resolves to 'Promise Resolved' after 5 seconds
  myPromise('RESULT')
);
const subscribe = example.subscribe(val => console.log(val));
```

After 5 second we will see output
```
["Hello", "World", 0, 1, "Promise Resolved: RESULT"]
```

[Example on Stackblitz](https://stackblitz.com/edit/typescript-mzbcrw?file=index.ts&devtoolsheight=100)


### forkJoin vs combineLatest
```
forkJoin(
  of("Hello"),
  of("World").pipe(delay(1000)),
  interval(1000).pipe(take(1)),
  interval(1000).pipe(take(5))
).subscribe(val => console.log('forkJoin', val));
```

After 5 second we will see output
```
["Hello", "World", 0, 4]
```


```
combineLatest([
  of("Hello"),
  of("World").pipe(delay(1000)),
  interval(1000).pipe(take(1)),
  interval(1000).pipe(take(5))
]
).subscribe(val => console.log('combineLatest',val));
```

We will see output each second
```
["Hello", "World", 0, 0]
["Hello", "World", 0, 1]
["Hello", "World", 0, 2]
["Hello", "World", 0, 3]
["Hello", "World", 0, 4]
```

[Example on Stackblitz](https://stackblitz.com/edit/a-forkjoin-combinelatest)

