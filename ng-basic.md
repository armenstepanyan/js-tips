### ngFor

```
<ul>
  <li *ngFor="let item of list; index as i; trackBy: trackByFn">
    {{ item }} index = {{ i }}
    </li>
</ul>
```

### ngFor extended version

```
<ul>
  <ng-template ngFor let-item [ngForOf]="list" let-i="index" [ngForTrackBy]="trackByFn">
  <li>{{ item }}</li>
</ng-template>
</ul>
```
