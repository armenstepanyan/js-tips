The decorator pattern is a design pattern that allows behavior to be added to an individual object, 
dynamically, without affecting the behavior of other objects from the same class.

An Angular Decorator is a function, using which we attach metadata to a class, method, accessor, 
property, or parameter. We apply the decorator using the form **@expression**, where expression is the name of the decorator.
To enable Angular Decorators, we need to add the experimentalDecorators to the `tsconfig.json`
```
{
  "compilerOptions": {
    "target": "ES5",
    "experimentalDecorators": true
  }
}
```

### Creating a new Decorator in Angular
```
export function simpleDecorator(target: any) {
  console.log('Decorator works');
 
  Object.defineProperty(target.prototype, 'value1', {
    value: 100,
    writable: false
  });
 
  Object.defineProperty(target.prototype, 'value2', {
    value: 200,
    writable: false
  });
}
```

Add new created decorator to component
```
import { simpleDecorator } from '../simple-decorator';

@simpleDecorator
@Component({
  selector: 'app-main',
  templateUrl: './main.component.html',
  styleUrls: ['./main.component.scss']
})
export class MainComponent implements OnInit {

  constructor() { }

  ngOnInit() {
    console.log((this as any).value1);
    console.log((this as any).value2);
  }

}

```

### Add arguments
```
@simpleDecorator({
  value1: 10,
  value2: 20,
})
@Component({
  selector: 'app-main',
  templateUrl: './main.component.html',
  styleUrls: ['./main.component.scss']
})
```

simple-decorator.ts
```
export function simpleDecorator(args) {
  console.log(args);

  return function(target: any) {
    console.log('Hello from Decorator');
    console.log(typeof target);
    console.log(target);

    Object.defineProperty(target.prototype, 'value1', {
      value: args.value1,
      writable: false
    });

    Object.defineProperty(target.prototype, 'value2', {
      value: args.value2,
      writable: false
    });
    // or target.prototype.value1 = 10
  };
}
```

Now if we try to add new property `value1` to class, it will throw error `Cannot assign to read only property 'value1' of object` because we set `writable: false`

```
@simpleDecorator({
  value1: 10,
  value2: 20,
})
@Component({
 ...
})
export class MainComponent implements OnInit {

  value2 = 1555; // <--- This will throw error
  constructor() { }

  ngOnInit() {
    console.log((this as any).value1);
    console.log((this as any).value2);
    console.log((this as any).test);
  }

}
```

### AutoDestroy decorator
This decorator will unsubscribe observables and complete subject on destroy. By default decorator will complete all subjects and observables or we can provide list.
```typescript
export function autoDestroy(args?: InputData) {
  const list = args?.list || [];

  return function (target: any) {
    const originalDestroy = target.prototype.ngOnDestroy;
    target.prototype.ngOnDestroy = function () {
      const props = list.length > 0 ? list : Object.keys(this);
       props.forEach((prop) => {
        if (prop) {
        // if Subject
          if (typeof this[prop].complete === 'function') {
            this[prop].next(true);
            this[prop].complete();
          }
          // Observable. e.g. http request
          if (typeof this[prop].unsubscribe === 'function') {
            this[prop].unsubscribe();
          }
        }
      });

      originalDestroy &&
        typeof originalDestroy === 'function' &&
        originalDestroy.apply(this, arguments);
    };
  };
}

```

Usage
```typescript
@autoDestroy({
  list: ['destroy$', 'interval$', 'request$'],
})
@Component({
  selector: 'app-list',
  templateUrl: './list.component.html',
})
export class ListComponent implements OnInit {
  destroy$ = new Subject<boolean>();
  interval$: any;
  request$: any;

  constructor(private http: HttpClient) {}

  ngOnInit(): void {
    this.request$ = this.http
      .get('https://jsonplaceholder.typicode.com/todos')
      .subscribe();

    interval(500).pipe(takeUntil(this.destroy$)).subscribe(console.log);

    this.interval$ = interval(700).subscribe(console.log);
  }
}
```

[Stackblitz](https://stackblitz.com/edit/angular-auto-destroy-decorator?devToolsHeight=33&file=src/app/list/list.component.ts)
