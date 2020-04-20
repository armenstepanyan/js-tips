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

```
  trackByFn(i, item) {
    // i- index, item - current item
    return i;
  }
```

### ngClass

```
<button (click)="activeClass=!activeClass">Toggle class</button>


<div [class.active]="activeClass">
  Text 1
</div>

<div [ngClass]="{'active': activeClass, 'disabled': !activeClass}">
  Text 2
</div>


<div [ngClass]="activeClass ? 'active' : 'disabled'">
  Text 3
</div>

```

### Style

```
<input type="number" [(ngModel)]="border">
<div class="d1" [style.borderWidth.px]="border">
  Text
</div>
```

### async pipe

```
import { Subject } from 'rxjs';

export class AppComponent  {
  data$ = new Subject<number>();

  setData() {
    const val = Math.floor(100*Math.random());
    this.data$.next(val);
  }

}

```

#### html
```
 <button (click)="setData()">Set data</button>
  <p>
    {{ data$ | async }}
  </p>
```
