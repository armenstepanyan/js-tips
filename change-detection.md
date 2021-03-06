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

For `OnPush` strategy need to call `markForCheck` function, that explicitly marks the view as changed so that it can be checked again.
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
