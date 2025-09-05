### ChangeDetectionStrategy
The strategy that the default change detector uses to detect changes. When set, takes effect the next time change detection is triggered.

Component
```typescript
export class CardOverviewExample {
  list = [
    { id: 1, name: "Data 1", isActive: false },
    { id: 2, name: "Data 2", isActive: true },
    { id: 3, name: "Data 3", isActive: false }
  ];

  setAllActive() {
    this.list.map(item => (item.isActive = true));
  }

  // for onPush strategy need to change item reference
  setAllActiveOnPush() {
    this.list = this.list.map(item => {
      return { ...item, isActive: true };
    });
  }
}
```

Html 
```html
<p>
  Default
</p>
<app-detail [data]="item" *ngFor="let item of list"></app-detail>
<p>
  On Push
</p>
<app-detail-push [data]="item" *ngFor="let item of list"></app-detail-push>

```

On Push component
```typescript
@Component({
  selector: "app-detail-push",
  template: `
    <mat-card>
      <mat-grid-list cols="8" rowHeight="50px">
        <mat-grid-tile colspan="2"> {{ data.name }}</mat-grid-tile>
        <mat-grid-tile colspan="6">
          <button
            mat-mini-fab
            [color]="data.isActive ? 'primary' : 'secondary'"
          >
            <mat-icon>{{ data.isActive ? "done_all" : "help" }}</mat-icon>
          </button>
        </mat-grid-tile>
      </mat-grid-list>
    </mat-card>
  `,
  styles: [
    "button {float: right; padding: 2px}",
    "mat-card {margin-bottom: 15px}"
  ],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class DetailOnPushComponent implements OnInit {
  @Input() data: DetailItem;
  ngOnInit() {}
}
```

[Stackblitz](https://stackblitz.com/edit/a-detect-changes?file=src/app/detail-on-push.component.ts)

### Default strategy
In this case view wiil be updated automatically by angular
```typescript
@Component({
  selector: "app-first",
  template: `
    <h3>Number {{ numberOfTicks }}!</h3>
  `,
})
export class FirstComponent {
  numberOfTicks = 0;

  constructor(private ref: ChangeDetectorRef) {
    setInterval(() => {
      this.numberOfTicks++;
    }, 1000);
  }
}
```

For `OnPush` strategy need to call `markForCheck` function (or `detectChanges`), that explicitly marks the view as changed so that it can be checked again.
```typescript
@Component({
  selector: "app-first",
  template: `
    <h3>Number {{ numberOfTicks }}!</h3>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class FirstComponent {
  numberOfTicks = 0;

  constructor(private ref: ChangeDetectorRef) {
    setInterval(() => {
      this.numberOfTicks++;
      // require view to be updated
      this.ref.markForCheck();
      // or this.ref.detectChanges()
    }, 1000);
  }
}
```
If we have object in our coponent and we are using `ChangeDetectionStrategy.OnPush` strategy, changing object reference **will not update** the view
```typescript
@Component({
  selector: "app-first",
  template: `
    <h3>Number {{ obj.a }}</h3>
  `,
  styles: [],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class FirstComponent {

  obj = {a: 1};

  constructor(private ref: ChangeDetectorRef) {
    setTimeout(() => {
    // NOTE: reference is changed but change-detection will not be triggered!
      this.obj = {a: 56}
    }, 1000);
  }
}

```

[Stackblitz](https://stackblitz.com/edit/angular-change-detector?file=src/app/first.component.ts)

The following example defines a component with a large list of read-only data that is expected to change constantly, many times per second. To improve performance, we want to check and update the list less often than the changes actually occur. To do that, we detach the component's change detector and perform an explicit local check every five seconds. 
```typescript
import { Component, ChangeDetectorRef } from "@angular/core";

@Component({
  selector: "app-second",
  template: `
    <li *ngFor="let d of data">Data {{ d }}</li>
    <p><button (click)="addToData()">Add value</button></p>
  `,
  styles: []
})
export class SecondComponent {
  _data = [1, 2, 3, 4, 5]
  constructor(private ref: ChangeDetectorRef) {
    ref.detach();
    setInterval(() => {
      this.ref.detectChanges();
    }, 3000);
  }

  get data() {
    console.log("get data list");
    return this._data;
  }

  set data(val) {
    console.log(val)
    this._data = val
  }

  addToData() {
    this.data = [...this.data, (this.data.length + 1)]
  }
}

```
Without ` ref.detach` the  `get data` getter wil be called on any change (click, mouse move..). When we click on `Add value` button, `view` will be updated after 3 seconds.

If inside view used `async` pipe, on subsription view will updated.

```typescript

@Component({
  selector: "app-first",
  template: `
    <p>{{ value$ | async }} </p>
  `,
  styles: [],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class FirstComponent {
  numberOfTicks = 0;
  value$ = new Subject();

  constructor(private ref: ChangeDetectorRef) {    
    setInterval(() => {
      this.numberOfTicks++;
      this.value$.next(this.numberOfTicks);
    }, 1000);
    
  }

}
```
In this example if we remove `async` from html, view will not updated. For example if subscription made in component it will not update the view.
```typescript
// this will not update view
    this.value$.subscribe(v => {
      console.log(v)
    })

    setInterval(() => {
      this.numberOfTicks++;
     this.value$.next(this.numberOfTicks);
    }, 1000);
```

### Summary
Angular change detection is a built-in framework feature that ensures the automatic synchronization between the data of a component and its HTML template view.

Change detection works by detecting common browser events like mouse clicks, HTTP requests, and other types of events, and deciding if the view of each component needs to be updated or not.
There are two types of change detection:

- Default change detection: Angular decides if the view needs to be updated by comparing all the template expression values before and after the occurrence of an event, for all components of the component tree
- OnPush change detection: this works by detecting if some new data has been explicitly pushed into the component, either via a component input or an Observable subscribed to using the async pipe

**OnPush will triger change detection if:**
- Input data is object/array and value is changed by reference
- Input data is primitive value and value was changed
- Observable was subscribed using the `async` pipe
- Angular event was fired.  `(click)="onClickHandle()"`
- Manual trigering: `this.ref.detectChanges()`

### Change Detection
✅ ChangeDetectionStrategy.Default

- Angular checks the component on every change detection cycle.

- This includes changes from:

- Timer (setTimeout)

  - HTTP responses

  - DOM events

  - Service updates

  - Anything async

✅ ChangeDetectionStrategy.OnPush

- Angular will only check the component when:

- An @Input() reference changes

- A DOM event is triggered inside the component

- You manually call:

  - ChangeDetectorRef.markForCheck()

  - detectChanges()
 
  #### Example Scenario:
```ts
@Component({
  selector: 'app-example',
  template: `
    <div>{{ counter }}</div>
    <button (click)="increment()">Increment</button>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ExampleComponent {
  counter = 0;

  increment() {
    this.counter++; // Primitive update inside component
  }
}
 ```

**Will this update the UI?**

✅ Yes, because of the (click) event, which is an event inside the component — it triggers change detection automatically.

🔄 Now, what if you update something outside the component (like from a service or setTimeout)?

```ts
ngOnInit() {
  setTimeout(() => {
    this.counter++; // Primitive value change
  }, 1000);
}
```

**Will the UI update?**

❌ No, because Angular (with OnPush) does not check this component unless it knows something changed.

Even though `counter++` changes the value, the reference doesn’t change, and Angular doesn’t know to check the component.

✅ How to Force CD in OnPush

Use ChangeDetectorRef:

```ts
constructor(private cdr: ChangeDetectorRef) {}

ngOnInit() {
  setTimeout(() => {
    this.counter++;
    this.cdr.markForCheck(); // ✅ triggers CD manually
  }, 1000);
}

```

#### Summary
With OnPush, Angular optimizes by skipping CD unless inputs change by reference or internal events occur. If I mutate a primitive or an array without changing its reference, the template won't update unless I manually trigger CD. 
That's why immutability and reference updates are critical in OnPush components.

#### OnPush in the parent and Default in the child
Scenario:

- Parent component uses `ChangeDetectionStrategy.OnPush`
- Child component uses `default` change detection
- We update a primitive value inside the child after a setTimeout

```ts
// parent.component.ts
@Component({
  selector: 'app-parent',
  template: `
    <h2>Parent (OnPush)</h2>
    <app-child></app-child>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ParentComponent {}

```
Child class

```ts
// child.component.ts
@Component({
  selector: 'app-child',
  template: `
    <h3>Child (Default)</h3>
    <p>Counter: {{ counter }}</p>
  `
  // default change detection, so no changeDetection config here
})
export class ChildComponent implements OnInit {
  counter = 0;

  ngOnInit() {
    setTimeout(() => {
      this.counter++;
      console.log('Child counter updated:', this.counter);
      // No manual change detection call here
    }, 1000);
  }
}

```

#### What will happen?
1. Angular runs change detection top-down starting from the root component or the component that triggered the CD cycle.
2. When the parent is `OnPush`, Angular will only run change detection on the parent and its subtree if:

    - An input to the parent changes by reference,
    - An event is fired inside the parent or its subtree,
    -  Or CD is triggered manually (e.g., via markForCheck).

3. Since the parent is `OnPush`, if none of the above happen, Angular will **NOT run change detection** for the parent or any of its children (because CD runs from the top).
4. Even though the child has default CD, it only runs if change detection runs for it, which only happens if the parent triggers CD.



