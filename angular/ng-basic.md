### ngFor

```
<ul>
  <li *ngFor="let item of list; index as i; trackBy: trackByFn">
    {{ item }} index = {{ i }}
    </li>
</ul>
```
[Stackblitz example](https://stackblitz.com/edit/a-ng-basic)

### ngIf
```
<div *ngIf="condition; then thenBlock else elseBlock"></div>   
<ng-template #thenBlock>Then template</ng-template>  
<ng-template #elseBlock>Else template</ng-template> 

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

### ngSwitch 
```
<select [(ngModel)]="count">
  <option [value]="1">1</option>
  <option [value]="2">2</option>
  <option [value]="3">3</option>
  <option [value]="10">10</option>
  <option [value]="20">20</option>
</select>

<div [ngSwitch]="count">
  <ng-container *ngSwitchCase="1">{{count * 10}}</ng-container>
  <ng-container *ngSwitchCase="2">{{count * 100}}</ng-container>
  <ng-container *ngSwitchCase="(count === '10' || count === '20') ? count : ''">Multiple condition {{ count }} </ng-container>
  <ng-container *ngSwitchDefault>{{count * 1000}}</ng-container>
</div>
```

### ngSwitch with ngTemplateOutlet
```
<ng-container [ngSwitch]="count">  
    <ng-container *ngSwitchCase="10">
        <ng-container *ngTemplateOutlet="multipleSwitch"></ng-container>
    </ng-container>
   
    <ng-container *ngSwitchCase="20">
        <ng-container *ngTemplateOutlet="multipleSwitch"></ng-container>
    </ng-container>
   
   <ng-container *ngSwitchCase="1">{{ count }}</ng-container>
   <ng-container *ngSwitchCase="2">{{ count }}</ng-container>
   <ng-container *ngSwitchCase="3">{{ count }}</ng-container>
   <ng-container *ngSwitchDefault>Default {{ count }} </ng-container>
   
    <ng-template #multipleSwitch>Multiple switch if count is 10 or 20</ng-template>
</ng-container>
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

