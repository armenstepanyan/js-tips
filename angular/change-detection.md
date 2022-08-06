### ChangeDetectionStrategy
The strategy that the default change detector uses to detect changes. When set, takes effect the next time change detection is triggered.

Component
```
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
```
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
```
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
```
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
```
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
```
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
```
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

```

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
```
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
