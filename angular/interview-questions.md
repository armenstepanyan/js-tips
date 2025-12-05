### 1. What is @Injectable in Angular and what happens if we mark a service class with or without @Injectable?
In Angular, `@Injectable` is a decorator used to indicate that a class can be injected as a dependency into other classes using Angular's dependency injection system. It is especially relevant for service classes, which often act as shared resources or provide business logic and data management.

#### Purpose:

The `@Injectable` decorator is used to make a class available for dependency injection. It allows Angular to instantiate the class, resolve its dependencies, and inject it wherever needed.

#### Usage:
You typically use`@Injectable` on service classes that you want to provide and inject into other components, directives, or services.

#### Providing Dependencies:
By marking a class with`@Injectable`, you can specify whether the class should be provided in a specific module, component, or the root injector (providedIn: 'root').

#### What Happens When You Mark a Service Class with @Injectable
- **Dependency Injection:**
Angular can inject the service into other classes (components, directives, etc.) because the @Injectable decorator registers the class with the Angular dependency injection system.

- **Resolving Dependencies:**
If the service itself depends on other services or resources, Angular can resolve those dependencies for it.

- **Tree-Shakable Providers:**
If `providedIn: 'root'` is specified in the `@Injectable` decorator, Angular ensures that the service is tree-shakable. It will only be included in the compiled JavaScript bundle if it's actually used in the application.

- **Scoped Providers:**
You can also specify providedIn to limit the scope of the service to a specific module or lazily loaded feature module.

#### What Happens If You Don't Use @Injectable
1. Dependency Injection Fails:

Without the `@Injectable` decorator, Angular's dependency injection system cannot inject the service into other classes. This will result in a runtime error if you attempt to inject the service.

For example:

```ts
export class MyService {
  constructor() {}
}

// Error: No provider for MyService!
@Component({
  selector: 'app-example',
  template: '<h1>Example Component</h1>'
})
export class ExampleComponent {
  constructor(private myService: MyService) {}
}
```
2. Manual Instantiation:
 If you don't use `@Injectable`, you would need to manually instantiate the service class wherever you need it, which defeats the purpose of Angular's dependency injection system.

3. Dependency Resolution Issues:
If the service class has its own dependencies (e.g., it depends on another service), Angular won't be able to resolve them.

#### @Injectable with Dependencies
If a service has dependencies, marking it with @Injectable ensures that Angular can inject those dependencies into the service class. 
For example:

```ts
@Injectable({
  providedIn: 'root'
})
export class MyService {
  constructor(private http: HttpClient) {}
}
```

Without `@Injectable`, Angular wouldn't know how to inject HttpClient into MyService, and you'd get a runtime error.

#### When @Injectable Is Not Required
If a service class doesn't have any dependencies (doesn't use the constructor for dependency injection), 
Angular can still provide it without @Injectable. However, it's a best practice to always use @Injectable for consistency and future-proofing. This way, if you later add dependencies to the service, you won't run into issues.

### 2. What is the OnPush Change Detection Strategy?
ChangeDetectionStrategy.OnPush tells Angular to run change detection for a component only when it has a strong signal that the component’s data might have changed.
This makes the component more predictable and performant.
#### ▶ How it works

With OnPush, Angular triggers change detection only when one of the following happens:

1. **An @Input() reference changes**
Angular compares the previous value and the new value using reference equality (===).

  - Primitives → value comparison
  - Objects/arrays → reference comparison

2. **An event occurs inside the component**
(e.g., button click, input type, etc.)

3. **An observable emits inside the template**
(e.g., using `async` pipe)

4. **Change detection is manually triggered**
via `ChangeDetectorRef.markForCheck()` or `detectChanges()`.

It drastically reduces unnecessary change detection runs and makes components faster, especially in large apps.

Angular’s OnPush strategy is used to optimize change detection.
With the default strategy, Angular runs change detection for a component every time any data changes anywhere in the application.

With `OnPush`, local component state **still updates normally** as long as the update occurs inside an Angular event (like `(click`)), or through an observable in the template. Angular will run the component’s change detection in those cases.

You only need to manually trigger change detection when the update happens outside Angular’s zone — for example, in a native event listener, a third-party callback, or setTimeout run outside Angular.

### 3. What are Feature, Lazy, Shared Modules in Angular, and how do you create them?
Feature modules are Angular modules used to organize application functionality into cohesive, focused sections.They help organize the codebase, improve maintainability, and allow features to be lazy loaded.

To create a feature module, you:

- Generate a new Angular module.
- Declare components, pipes, and services inside it.
- Optionally create a routing module for feature-specific routes.
- Import the feature module into AppModule (eager loading) or configure lazy loading in the app routing.

**Lazy-loaded** modules are modules that Angular loads only when the route is accessed. Feature modules help structure the app; lazy loading is a performance strategy. A feature module may or may not be lazy-loaded.

  
**A Shared Module** is used to re-use common components, directives, pipes, and sometimes services across multiple feature modules.
▶ Key Characteristics

1. Contains reusable components, directives, pipes
Example: CustomButtonComponent, DateFormatterPipe, TooltipDirective.
2. Imported by feature modules, not AppModule
This avoids cluttering the root module and keeps things modular.
3. Should not provide singleton services
Services in shared modules may create multiple instances if imported in multiple modules. Singleton services belong in Core Module.
4. No routing
Shared modules are purely for reusable pieces — they don’t define routes.

### 4. What is Prototpye inheritance in JS
In JavaScript, every object has a prototype, which is another object it can inherit properties and methods from. Functions have a prototype property that defines what their instances will inherit, while each object has a __proto__ property that points to its prototype. This system, called prototype inheritance, allows objects to share functionality efficiently, so if a property or method isn’t found on an object, JavaScript looks up the prototype chain to find it.
[Read More](https://github.com/armenstepanyan/js-tips/blob/master/js-basic/prototype.md)


## **Question 1 — Change Detection Trap: Microtasks vs Macrotasks**

**Q:**  
You're using `ChangeDetectionStrategy.OnPush`. In a component, you update a field inside a **Promise callback**, but the view does **not** update.  
However, if you update the same field inside an `setTimeout`, it **does** update.  
Why? Explain the difference in how Angular handles microtasks vs. macrotasks in change detection — and how Zone.js is involved.

----------

## **Answer 1**

Angular’s change detection is triggered by **Zone.js patching async APIs**.  
But **Zones treat microtasks and macrotasks differently**:

### **Microtasks** (Promise, queueMicrotask, async/await)

-   They run **before** the next event loop tick.
    
-   Zone.js _does_ patch Promises,  
    **but OnPush components won’t run change detection unless an Input changes or an event fires**.
    
-   Updating component state inside a Promise **does not mark the component dirty**.
    

So: `Promise.resolve().then(() => this.value = 1)`  
→ Angular sees no “event-like” async trigger  
→ `OnPush` component stays “clean”  
→ View does _not_ update.

### **Macrotasks** (setTimeout, setInterval, requestAnimationFrame)

-   When a macrotask completes, Zone.js triggers a macro-task event hook.
    
-   Angular runs change detection (or at least marks view dirty).
    

So: `setTimeout(() => this.value = 1)`  
→ Macro-task event  
→ Angular runs CD  
→ View updates.

### **Fixes**

1.  **Inject ChangeDetectorRef**
    
```ts
constructor(private cd: ChangeDetectorRef) {}
... Promise.resolve().then(() => { this.value = 1; this.cd.markForCheck();
});
```

2.  **Use async pipe** (preferred)
    
3.  **Run code inside NgZone**
    

```ts
this.ngZone.run(() =>  this.value = 1);
``` 



## **Question 2 — Angular DI: Multi-Provider Collision & Token Shadowing**

**Q:**  
Suppose you have three modules in your application:

-   **CoreModule** (imported once in AppModule)
    
-   **SharedModule** (imported in many feature modules)
    
-   **FeatureModule** (lazy-loaded)
    

All three provide a multi-provider for the same InjectionToken:

```ts
export const LOG_HANDLERS = new InjectionToken<LogHandler[]>('LOG_HANDLERS');

@NgModule({
  providers: [
    { provide: LOG_HANDLERS, multi: true, useClass: CoreLogHandler }
  ]
})
export class CoreModule {}

@NgModule({
  providers: [
    { provide: LOG_HANDLERS, multi: true, useClass: SharedLogHandler }
  ]
})
export class SharedModule {}

@NgModule({
  providers: [
    { provide: LOG_HANDLERS, multi: true, useClass: FeatureLogHandler }
  ]
})
export class FeatureModule {}

```

Inside a lazy-loaded component, you inject:

```ts
constructor(@Inject(LOG_HANDLERS) private handlers: LogHandler[]) {}
```

### What is the order of handlers injected?

And how does Angular decide which multi-providers are visible to a lazy-loaded module?  
Explain the resolution rules and why order and visibility can be surprising.**



## **Answer 2**

### **1. Multi-provider order is based on the DI hierarchy**

Angular resolves multi-providers by **walking up the injector tree**, and **concatenating** arrays:

1.  Component injector
    
2.  Module injector (lazy module)
    
3.  Parent module injectors
    
4.  Root injector
    

But **each level is appended in the order modules are instantiated**, not in the order they appear in code.

### **2. In a lazy-loaded module, providers from eagerly-loaded modules behave differently**

A lazy-loaded module has its **own child injector**.

So visibility becomes:

-   **FeatureModule (lazy)** → visible (highest priority)
    
-   **SharedModule** inside FeatureModule → visible **only if imported inside FeatureModule**
    
-   **SharedModule imported elsewhere** → _not visible to lazy module_
    
-   **CoreModule** → always visible (root)
    

Thus, **only SharedModule instances imported inside that lazy FeatureModule are visible**.

Most people miss this.

### **3. The resulting order**

Assuming:

-   CoreModule imported in AppModule (root)
    
-   SharedModule imported in AppModule (root)
    
-   FeatureModule lazy-loaded and imports SharedModule again
    

The handlers array inside the lazy-loaded component becomes:

```ts
[
  FeatureLogHandler,      // from FeatureModule — closest injector
  SharedLogHandler,       // from SharedModule imported inside FeatureModule
  CoreLogHandler          // from CoreModule (root injector)
]

``` 

### **4. Why this is tricky**

-   SharedModule **is not singleton**; each import creates a new injector scope.
    
-   Lazy-loaded modules get their own child injector.
    
-   Multi-providers concatenate across injector levels.
    
-   Duplicate SharedModules create multiple handler entries (one per import).
    

### **5. Bonus: How to avoid surprises**

-   Move multi-providers to CoreModule only.
    
-   Or explicitly provide in the root:
    

`{ provide: LOG_HANDLERS, useClass: SharedLogHandler, multi: true, providedIn: 'root' }` 


## **Question 3 — Angular Rendering Engine Quirk: Template Ref Preservation**

**Q:**  
Consider this parent template:

```ts
<ng-template #tmpl let-value="value">
  {{ value }}
</ng-template>

<child [template]="tmpl" [value]="42"></child>

```

And the child component:

```ts
@Component({
  selector: 'child',
  template: `
    <ng-container
      *ngTemplateOutlet="template; context: { value: value }"
    ></ng-container>
  `
})
export class ChildComponent {
  @Input() template!: TemplateRef<any>;
  @Input() value!: number;
}

```

Now the trick:

If the parent template uses **OnPush** and you change `value` in the parent _without changing the TemplateRef_, sometimes the child updates, and sometimes it doesn’t — depending on the way the update is triggered.

### Why does Angular sometimes NOT re-render a `<ng-template>` passed to a child, even when the context changes?

Explain what part of Angular’s rendering engine causes this behavior, and under which conditions Angular will or will not re-evaluate the template context.**

## **Answer 3**

This is a subtle behavior caused by how Angular **treats TemplateRef Instructions in the Ivy engine** combined with **OnPush change detection**.


### **1. TemplateRef is NOT reactive**

A `TemplateRef` is **just a static reference to compiled instructions**.  
Angular does **not track changes to its context** unless change detection runs in the child.

So if parent runs change detection but the child component is `OnPush` and does not receive a new @Input object **(TemplateRef stays the same reference)**, Angular considers:

-   `template` input: **same reference**
    
-   `value` input: **possibly same reference (if primitive but via OnPush rules CD may not run)**
    
-   => Child does **not** mark itself dirty
    

Thus **the embedded view inside ngTemplateOutlet is not updated**.

----------

### **2. Why sometimes it DOES update**

When the update originates from:

-   an **@Input change** in `value`
    
-   or any **event** inside the child
    
-   or any **macro-task** event (timeout, HTTP, click)
    

Angular schedules a CD cycle in which:

-   Child is marked dirty
    
-   The `ngTemplateOutlet` resets its context
    
-   The embedded view re-renders
    

```ts
*ngTemplateOutlet="template; context: { value: value }"
```

Even though TemplateRef is the same, the **context object literal is recreated every CD** — so the outlet re-renders **only if CD actually runs**.


### **3. Why sometimes it does NOT update**

If the parent updates `value` _in a microtask_ under OnPush:

```ts
Promise.resolve().then(() =>  this.value = 100);
```

Then:

-   Parent OnPush does **not** run CD (no event, no input change)
    
-   Child OnPush definitely does **not** run CD (no new @Input reference)
    
-   The embedded view inside `ngTemplateOutlet` _**keeps the old context**_
    

So the template continues to show the old value.


### **4. The Ivy engine detail: ViewRef caching**

Angular Ivy reuses embedded views from `ngTemplateOutlet` for performance.

A re-render only occurs if:

-   The TemplateRef reference changes, **OR**
    
-   The host component is marked dirty and CD runs
    

If neither happens → the cached view is reused unchanged.

That’s the root cause.


### **5. How to force correct behavior**

### Option A: Mark child for check when inputs change

```ts
@Input() set  value(v: number) { this._value = v; this.cdr.markForCheck();
}
``` 

### Option B: Let parent propagate CD

Parent must trigger CD itself:

`this.cdr.markForCheck();` 

### Option C: 
Use `ChangeDetectorRef.detectChanges()` _after_ updating value (only when necessary)


## **Question 4 — RxJS + Angular: Subtle Memory Leak in AsyncPipe with SwitchMap**

**Q:**  
Suppose you have this component:

```ts
@Component({
  selector: 'user-info',
  template: `
    <div *ngIf="user$ | async as user">
      {{ user.name }}
    </div>
  `
})
export class UserInfoComponent {
  user$ = this.userService.userId$.pipe(
    switchMap(id => this.userService.getUserById(id))
  );

  constructor(private userService: UserService) {}
}

```

The interviewer asks:

> “Can this code create a memory leak in certain conditions? If yes, explain why, and how to fix it.”


## **Answer 4**

### **1. The potential leak**

-   `user$` depends on `userService.userId$`.
    
-   `switchMap` subscribes to `getUserById(id)` every time a new ID comes in.
    
-   **AsyncPipe** automatically unsubscribes when the component is destroyed.
    
-   **Problem arises if `userService.userId$` is a **hot observable** that lives forever (e.g., a BehaviorSubject)**.
    

**Scenario:**

-   `userService.userId$` keeps emitting IDs even when component is destroyed.
    
-   `switchMap` inner observable is unsubscribed correctly, **but if there are side effects in `getUserById` that are not tied to subscription cancellation**, those may continue (e.g., HTTP requests started without cancellation).
    

Also, if `getUserById` internally uses `tap` to update global state or `shareReplay` without proper teardown, memory can leak.

----------

### **2. Why it’s tricky**

-   Many candidates think **AsyncPipe + switchMap = automatic cleanup**.
    
-   True, but only for **subscribed inner observables**.
    
-   Any **external side-effects not tied to the subscription** remain alive.
    

----------

### **3. How to fix / make it safer**

#### **Option A: Cancel inner observables on destroy**

```ts
user$ = this.userService.userId$.pipe(
  switchMap(id =>
    this.userService.getUserById(id).pipe(takeUntil(this.destroy$))
  )
);

``` 

-   Requires `destroy$ = new Subject<void>()` and call `destroy$.next()` in `ngOnDestroy`.
    

#### **Option B: Ensure getUserById is cold and cancellable**

```ts
getUserById(id: string) {
  return this.http.get<User>(`/users/${id}`);
   // Angular HttpClient observables are cold + cancelable
}

```

#### **Option C: Use AsyncPipe directly with `id$`**

```ts
user$ = this.userService.userId$.pipe(
  switchMap(id => this.userService.getUserById(id))
);
// AsyncPipe handles subscription/unsubscription automatically

```

-   ✅ Safe **as long as the inner observable is cold/cancelable**.
    

----------

**Key takeaway:**  
Even with AsyncPipe, **hot sources + side effects + shared/cached observables** can silently cause memory leaks. Senior devs should always check **observable lifetimes** and **side-effect cleanup**.


## **Question 5 — Angular Ivy: Template Type Checking Edge Case**

**Q:**  
Consider this Angular component:

```ts
@Component({
  selector: 'app-root',
  template: `
    <input [(ngModel)]="user.name">
    <button (click)="saveUser()">Save</button>
  `
})
export class AppComponent {
  user!: { name: string };
  saveUser() {
    console.log(this.user.name);
  }
}

```

You run **strict mode with Angular 17+ Ivy**, and the compiler shows an **error on `[(ngModel)]="user.name"` saying `Object is possibly undefined`**, even though `user` is initialized in `ngOnInit`.

-   Why does this happen?
    
-   How does Ivy’s **strict template type checking** work in this context, and what are the safe ways to fix it **without disabling strict mode**?
    
## **Answer 5**

### **1. Why the error happens**

-   `user!: { name: string };` uses the **non-null assertion operator (`!`)**.
    
-   However, **Angular Ivy template type checking** does **not trust non-null assertions in the component class**.
    
-   Ivy treats **templates as fully independent type-checked expressions**, separate from TypeScript runtime assurances.
    
-   At template compile time, Ivy assumes `user` might be `undefined` because:
    
    -   It doesn’t see guarantees from `ngOnInit`.
        
    -   The type `user!: { name: string }` is not sufficient for template safety checks.
        

This is intentional: Ivy wants to **prevent null/undefined errors at runtime** in templates.

----------

### **2. How Ivy template type checking works**

-   **Template compiler** creates a **strictly-typed context** for the component.
    
-   `[(ngModel)]` needs a **non-nullable value**.
    
-   Any property that might be undefined **according to TypeScript** triggers an error.
    
-   Non-null assertions (`!`) **only affect TypeScript**, not template type checker.
    

----------

### **3. How to fix it safely**

#### **Option A: Initialize `user` directly**

`user: { name: string } = { name: '' };` 

✅ This ensures the property is never undefined, satisfies Ivy.

#### **Option B: Use optional chaining in template**

`<input [(ngModel)]="user?.name">` 

⚠️ This works but may break two-way binding if `user` is undefined.

#### **Option C: Use a getter that returns a default**

```ts
get userSafe(): { name: string } {
  return this.user ?? { name: '' };
}

```

#### **Option D: Use `!` in template**

`<input [(ngModel)]="user!.name">` 

-   Template non-null assertion (`!`) tells Ivy you are sure it's defined.
    
-   ✅ Safe if you truly guarantee `user` is initialized before template renders.
    

----------

**Key takeaway:**  
Ivy template type checking is **stricter than TypeScript**.  
Non-null assertions in the class (`!`) are **ignored in templates**, so senior Angular developers must either **initialize properties** or use **template-safe techniques**.



## **Question 6 — RxJS + Angular Change Detection Race Condition**

**Q:**  
Consider this component:
```ts
@Component({
  selector: 'app-race',
  template: `
    <div *ngIf="value">{{ value }}</div>
  `
})
export class RaceComponent implements OnInit {
  value?: string;

  ngOnInit() {
    fromEvent(document, 'click')
      .pipe(
        switchMap(() =>
          timer(0).pipe(
            map(() => 'Clicked!')
          )
        )
      )
      .subscribe(v => (this.value = v));
  }
}

```

When you click, sometimes the `<div>` does **not** update, depending on how you click (fast, slow, or via keyboard).

**Explain why this happens. Why does the view sometimes fail to update even though `value` is set, and how can you fix it without changing the `*ngIf` logic?**



## **Answer 6**

This is a **classic change detection race condition** in Angular combined with **RxJS microtasks/macrotasks**.

----------

### **1. What’s happening**

-   `fromEvent(document, 'click')` → triggers on **DOM events**, patched by Zone.js → CD runs.
    
-   `switchMap` → `timer(0)` → executes **as a macrotask**.
    
-   When the timer emits, it runs **outside of Angular’s zone** if the event or async chain loses Zone context (rare, but can happen if certain operators or libraries are used).
    
-   Angular **does not automatically detect changes** for assignments done outside the zone.
    

So `this.value = v` runs **but CD doesn’t know** → view may not update.

----------

### **2. Why it seems intermittent**

-   Fast clicks may queue multiple timers → some emit **inside the zone**, some **outside**.
    
-   Some browsers or async scheduling may change task priority → race condition for CD.
    

----------

### **3. How to fix**

#### **Option A: Ensure update runs inside Angular zone**

```ts
constructor(private ngZone: NgZone) {}

ngOnInit() {
  fromEvent(document, 'click')
    .pipe(
      switchMap(() =>
        timer(0).pipe(
          map(() => 'Clicked!')
        )
      )
    )
    .subscribe(v => {
      this.ngZone.run(() => (this.value = v));
    });
}

```

#### **Option B: Manually trigger change detection**

```ts
constructor(private cd: ChangeDetectorRef) {}

.subscribe(v => {
  this.value = v;
  this.cd.detectChanges();
});

```

#### **Option C: Use `async` pipe instead of manual subscription**

```ts
value$ = fromEvent(document, 'click').pipe(
  switchMap(() => timer(0).pipe(map(() => 'Clicked!')))
);

```

```html
<div *ngIf="value$ | async as value">{{ value }}</div>
```

-   AsyncPipe automatically triggers CD.
    

----------

## **Question 7 — Advanced Structural Directive & Embedded View Edge Case**

**Q:**  
You create a custom structural directive:

```ts
@Directive({
  selector: '[myIf]'
})
export class MyIfDirective {
  private hasView = false;

  constructor(
    private templateRef: TemplateRef<any>,
    private viewContainer: ViewContainerRef
  ) {}

  @Input() set myIf(condition: boolean) {
    if (condition && !this.hasView) {
      this.viewContainer.createEmbeddedView(this.templateRef);
      this.hasView = true;
    } else if (!condition && this.hasView) {
      this.viewContainer.clear();
      this.hasView = false;
    }
  }
}

```

When you use it:

`<div *myIf="value">Hello</div>` 

-   If `value` changes very quickly in succession, sometimes the view **flashes**, sometimes it disappears permanently.
    
-   Explain **why this happens**, considering **Angular’s embedded view lifecycle**, **change detection**, and **microtask scheduling**.
    
-   How can you make this directive **more robust** for rapid input changes?
    

## **Answer 7**

### **1. What’s happening**

-   `createEmbeddedView` and `viewContainer.clear()` **mutate the view tree immediately**.
    
-   If `value` toggles rapidly:
    
    1.  `myIf(true)` → creates view
        
    2.  `myIf(false)` → clears view
        
    3.  **Angular CD may not have run yet**, so the view may not render visually before it’s cleared.
        
-   The `hasView` flag is **just a boolean**. It assumes **synchronous rendering**, but in Angular Ivy, **embedded views are processed asynchronously in the rendering pipeline** (especially when combined with microtasks/macrotasks).
    

This causes:

-   **Flashes**: view created and cleared in the same CD cycle.
    
-   **Disappearing view**: view cleared after a CD cycle that skipped rendering.
    

----------

### **2. Why Angular does this**

-   `ViewContainerRef.createEmbeddedView()` returns a **ViewRef**, which Angular schedules for rendering **at the end of the CD cycle**.
    
-   Rapid toggles **invalidate the previously scheduled view**, but `hasView` may already be `true` or `false`.
    
-   Angular does **not queue multiple embedded views** for the same container; clearing the container immediately removes the scheduled view too.
    

----------

### **3. How to make it robust**

#### **Option A: Always track the created ViewRef**

```ts
private viewRef?: EmbeddedViewRef<any>;

@Input() set myIf(condition: boolean) {
  if (condition && !this.viewRef) {
    this.viewRef = this.viewContainer.createEmbeddedView(this.templateRef);
  } else if (!condition && this.viewRef) {
    this.viewContainer.clear();
    this.viewRef = undefined;
  }
}

```

-   Keeps a **direct reference** instead of a boolean flag.
    
-   Prevents mismatches when CD cycles are delayed.
    


#### **Option B: Schedule operations in next microtask**

```ts
@Input() set myIf(condition: boolean) {
  Promise.resolve().then(() => {
    if (condition && !this.viewRef) {
      this.viewRef = this.viewContainer.createEmbeddedView(this.templateRef);
    } else if (!condition && this.viewRef) {
      this.viewContainer.clear();
      this.viewRef = undefined;
    }
  });
}

```

-   Avoids **race conditions with rapid changes**.
    

----------

### **4. Key takeaways**

-   Embedded views are **asynchronously scheduled** in Angular Ivy.
    
-   Boolean flags (`hasView`) are **not always safe** for rapid toggles.
    
-   Tracking **ViewRef objects** and/or **delaying DOM operations slightly** prevents race conditions.
    
-   This is exactly the kind of subtle bug senior Angular devs are expected to understand and fix.
    

## **Question 8 — Advanced Router Outlet + Lazy Loading Lifecycle Trap**

**Q:**  
Consider a lazy-loaded feature module with this setup:

```ts
// app-routing.module.ts
const routes: Routes = [
  { path: 'feature', loadChildren: () => import('./feature/feature.module').then(m => m.FeatureModule) }
];

// feature-routing.module.ts
const routes: Routes = [
  { path: '', component: FeatureComponent }
];

```
`FeatureComponent` injects a service:

```ts
@Component({ ... })
export class FeatureComponent {
  constructor(private svc: FeatureService) {
    console.log('FeatureService instance:', svc.id);
  }
}

```

`FeatureService` is provided in the `FeatureModule`:

```ts
@NgModule({
  providers: [FeatureService]
})
export class FeatureModule {}

```

Now, imagine:

1.  User navigates to `/feature` → `FeatureComponent` renders.
    
2.  User navigates **away** to another route.
    
3.  User navigates **back** to `/feature`.
    

-   Sometimes, the console shows a **new FeatureService instance**; other times, it shows the **same instance**.
    

**Explain why the service instance sometimes persists across lazy-loaded module destruction. What part of Angular’s DI + router lifecycle is causing this, and how can you reliably control service lifetimes in lazy modules?**



## **Answer 8**

### **1. Lazy-loaded modules have their own injector**

-   Each lazy-loaded module creates a **child injector** of the root injector.
    
-   Providers declared **in the lazy module** live **as long as the module is loaded**.
    

### **2. When does the module get destroyed?**

-   **Angular currently does not destroy lazy-loaded module injectors** automatically when navigating away.
    
-   Once the module is loaded, Angular **caches the module instance**.
    
-   Navigating back **reuses the cached module and its providers**.
    
-   Hence, `FeatureService` **may persist** across navigations.
    

> Only if you manually unload the module (rare) or use custom route reuse strategies will the injector be destroyed.

### **3. Why it seems inconsistent**

-   If the module is **already loaded**, Angular does **not create a new injector**.
    
-   If the module is being loaded for the **first time**, a new injector is created → new service instance.
    

This explains why you sometimes see **new vs old instances**.

----------

### **4. How to control service lifetime**

#### **Option A: Provide service in root**

`@Injectable({ providedIn: 'root' }) export  class  FeatureService {}` 

-   Service is singleton app-wide.
    
-   No surprises with lazy-loaded module caching.
    

#### **Option B: Use a **module-level factory that resets on destroy**

-   Track service manually and destroy state when navigating away.
    
-   Requires listening to `Router` events.
    

#### **Option C: Avoid multiple instances**

-   Only provide in lazy module if you **want per-module instance**, but accept caching behavior.
    
-   Understand that **Angular does not destroy lazy module injectors** automatically.
    

----------

### **5. Key takeaway**

-   Lazy-loaded modules **create child injectors once**, which Angular caches.
    
-   Module providers **may persist across navigation**.
    
-   Senior devs must understand **DI scoping vs router lifecycle** to avoid subtle bugs in stateful services.
    

## **Question 9 — Angular Signals + OnPush Tricky Interaction**

**Q:**  
Angular 17 introduced **Signals**. Consider this component:

```ts
@Component({
  selector: 'app-counter',
  template: `
    <button (click)="increment()">Increment</button>
    <p>{{ count() }}</p>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class CounterComponent {
  count = signal(0);

  increment() {
    this.count.set(this.count() + 1);
  }
}

```

-   You notice that in some scenarios, `count()` **does not update the view** even though the signal changes.
    
-   Explain **why OnPush can sometimes block Signal propagation** in Angular.
    
-   How should you properly write a component using Signals with OnPush to guarantee the view always updates?
    

----------

## **Answer 9**

### **1. How Signals work**

-   Signals are **reactive primitives** in Angular 17+.
    
-   When you call `signal.set()` or `signal.update()`, Angular **marks the component dirty automatically**, but only **if the component is rendered within the same reactive context**.
    

----------

### **2. Why OnPush sometimes blocks updates**

-   OnPush components **only check for updates when**:
    
    -   An @Input reference changes
        
    -   An event originates from the component or a child
        
    -   A signal is **used in a reactive context** properly
        
-   If a signal is **updated outside Angular’s zone** (e.g., from a `setTimeout`, `Promise`, or external library callback), the **component may not be marked dirty** automatically.
    
-   This leads to **view not updating**, even though the signal value changes.
    

----------

### **3. How to fix**

#### **Option A: Use `effect` to mark dirty manually**

```ts
constructor(private cd: ChangeDetectorRef) {
  effect(() => {
    this.count(); // read signal
    this.cd.markForCheck(); // notify Angular to run CD
  });
}

```

#### **Option B: Update signals inside Angular zone**

```ts
constructor(private ngZone: NgZone) {}

increment() {
  this.ngZone.run(() => this.count.set(this.count() + 1));
}

```
## **Question 10 — Advanced ViewContainerRef + Detached Embedded View Trap**

**Q:**  
Consider this component:

```ts
@Component({
  selector: 'app-detach',
  template: `<ng-template #tmpl>Detached View!</ng-template>`
})
export class DetachComponent implements AfterViewInit {
  @ViewChild('tmpl') tmpl!: TemplateRef<any>;

  private viewRef?: EmbeddedViewRef<any>;

  constructor(private vc: ViewContainerRef) {}

  ngAfterViewInit() {
    this.viewRef = this.vc.createEmbeddedView(this.tmpl);
    // Detach immediately
    this.vc.detach(this.vc.indexOf(this.viewRef));
  }

  attachLater() {
    if (this.viewRef) {
      this.vc.insert(this.viewRef);
    }
  }
}

``` 

**Scenario:**

-   After calling `attachLater()`, the view sometimes **does not render** or causes Angular to throw errors like “view already attached.”
    

**Explain why detaching and re-inserting embedded views can fail, what Angular internally tracks, and how to safely manage detached views.**

----------

## **Answer 10**

### **1. How Angular tracks embedded views**

-   `ViewContainerRef` keeps an **internal array of embedded views**.
    
-   Each `EmbeddedViewRef` has a **state**:
    
    -   Attached → part of container
        
    -   Detached → not rendered, but still tracked
        
-   `detach(index)` **removes the view from the container**, returning it but keeping it in memory.
    
-   `insert(view)` **inserts a view** back into a container at a given index.
    

----------

### **2. Why re-insertion fails**

-   `vc.detach(vc.indexOf(viewRef))` may return `null` if the index calculation is wrong.
    
-   After detach, `vc.indexOf(viewRef)` **returns -1**, because the view is no longer in the container.
    
-   If you call `insert(viewRef)` without specifying the correct index, Angular may:
    
    -   Insert at wrong position
        
    -   Throw error if view is already attached elsewhere
        
-   Rapid detach/insert or using the same view across multiple containers can cause **“view already attached”** errors.
    

----------

### **3. Safe pattern for detached views**

#### **Option A: Track index at detach**

```ts
ngAfterViewInit() {
  this.viewRef = this.vc.createEmbeddedView(this.tmpl);
  this.detachedIndex = this.vc.indexOf(this.viewRef);
  this.vc.detach(this.detachedIndex);
}

attachLater() {
  if (this.viewRef) {
    this.vc.insert(this.viewRef, this.detachedIndex);
  }
}

```

#### **Option B: Always insert at the end**

`this.vc.insert(this.viewRef); // appends safely` 

#### **Option C: Don’t detach immediately if not needed**

-   Sometimes a **detached view** is unnecessary.
    
-   Simply remove/clear views when needed.
    

----------

### **4. Key subtleties**

-   Embedded views are **not automatically tracked by the component** once detached; you must **keep a reference**.
    
-   Index tracking is critical if you plan to **reinsert in the original position**.
    
-   Using the same view in **multiple containers simultaneously** is **not allowed**.
    

