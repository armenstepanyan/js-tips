
### DestroyRef
Angular v16 has introduced a new provider called DestroyRef, which allows for registering destroy callbacks for a specific lifecycle scope. This feature is applicable to components, directives, pipes, embedded views, and instances of EnvironmentInjector.

```ts
@Component({
  selector: 'foo',
  standalone: true,
  template: '',
})
class FooComponent {
  constructor() {
    inject(DestroyRef).onDestroy(() => {
      // do something when the component is destroyed
    })
  }
}
```

Note: `ngOnDestroy` is being called before the callback which is registered in `DestroyRef`

```ts
@Component({
  selector: 'app-users',
  standalone: true,
})
export class UsersComponent implements OnInit {
  constructor(private destroyRef: DestroyRef) {
    this.destroyRef.onDestroy(() => {
      console.log('destroyRef callback');
    });
  }


  ngOnDestroy() {
    console.log('ngOnDestroy');
  }
}
```

Output
```ts
ngOnDestroy
destroyRef callback
```

So the main difference is that the `ngOnDestroy` lifecycle hook it is a simple class method that is called by angular right before the scope  is destroyed and executes some cleanup logic.
The `destroyRef` is a class that is injected by a  dependency injection and it executes some cleanup logic inside the corresponding callback 

### takeUntilDestroyed

Operator which completes the Observable when the calling context (component, directive, service, etc) is destroyed.

### Basic usage

```ts
@Component({
  selector: 'app-users',
  standalone: true,
})
export class UsersComponent implements OnInit {
  // injecting in available injection context
  destroyRef = inject(DestroyRef);
  

  ngOnInit(): void {
    
    interval(1000).pipe(takeUntilDestroyed(this.destroyRef)).subscribe(number => {
      console.log("Number: ", number);
    })
  }
}
```

Without passing `this.destroyRef` to `takeUntilDestroyed` we will get this error

```js
RuntimeError: NG0203: takeUntilDestroyed() can only be used within an injection context such as a constructor
a factory function, a field initializer, or a function used with `runInInjectionContext`
```

Because we are registering subsriber inside lifecycle hooks. So if we move this to constructor is it work as expected

```ts
constructor() {
  interval(1000).pipe(takeUntilDestroyed()).subscribe(number => {
    console.log("Number: ", number);
  })
 }
```

Read more about **Injection context** [here](https://angular.dev/guide/di/dependency-injection-context)

### Example 1

We can create an `untilDestroyed` helper operator that relies on DestroyRef:
```ts
export function untilDestroyed() {
  const subject = new Subject();

  inject(DestroyRef).onDestroy(() => {
    subject.next(true);
    subject.complete();
  });

  return <T>() => takeUntil<T>(subject.asObservable());
}
```

Usage

```ts
@Component({
  selector: 'app-users',
  standalone: true,
})
export class UsersComponent implements OnInit {

  private untilDestroyed = untilDestroyed();

  ngOnInit(): void {
    interval(1000)
      .pipe(this.untilDestroyed())
      .subscribe(console.log);    
  }

}
```



### Example 2

```ts
function destroyNotifier() {
  const destroy = new Subject<void>();
  inject(DestroyRef).onDestroy(() => {
    destroy.next();
    destroy.complete();
  });

  return destroy;
}

@Component({
  selector: 'app-users',
  standalone: true,
})
export class UsersComponent implements OnInit {
  destroy$ = destroyNotifier();
 
 constructor() {
    interval(1000)
      .pipe(takeUntil(this.destroy$)) // usage
      .subscribe(() => {
        console.log('tick');
      });
  }

}

```

#### Limitations
There is one limitation though  since our custom function uses `inject` function under the hood this custom function can be used  only within their Constructor context.

Since the `destroyNotifier` function is encapsulated by our custom function  it might be sometimes hard to figure out which  exactly function is wrongly used because we could  have many such custom functions that use inject  
function under the hood we have to do an additional check using  their **assertInInjectionContext** helper function that is checking if the function is used in the  proper context and if it is not it will throw an  error where it is clearly visible which exactly  function is used in the wrong place

```ts
function destroyNotifier() {
  const destroy = new Subject<void>();
  // add checking here
  assertInInjectionContext(destroyNotifier);
  inject(DestroyRef).onDestroy(() => {
    destroy.next();
    destroy.complete();
  });

  return destroy;
}
```

Example of wrong usage

```ts
export class UsersComponent implements OnInit {
  destroy$! : Subject<void>;

  ngOnInit(): void {
    // create instance inside lifecycle hook
    this.destroy$ = destroyNotifier();
   //...
  }
}
```

Error
```js
NG0203: inject() must be called from an injection context such as a constructor, a factory function,
a field initializer, or a function used with `runInInjectionContext`
```

Error message after adding the `assertInInjectionContext`

```js
NG0203: destroyNotifier() can only be used within an injection context such as a constructor, a factory function,
a field initializer, or a function used with `runInInjectionContext`
```

Read more about `inject()` must be called from an injection context [here](https://angular.dev/errors/NG0203)
