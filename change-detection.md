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

