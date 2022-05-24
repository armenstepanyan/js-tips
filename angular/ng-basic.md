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

### Use emitEvent: false
lets assume we have form and on form field value change we send save request
```
this.submitForm = this.fb.group({
    title: [''],
    description: [''],
})
```
Create subscrition on form value change
```
this.submitForm
    .valueChanges
    .pipe(debounceTime(500))
    .subscribe(data => {
        this.sendRequestToBackend();
    });
```
Now if we set form value with `this.submitForm.setValue({ ... })` it will trigger `this.submitForm.valueChanges` subscription again. To avoid this behavior need to pass `{emitEvent: false}` as argument

```
this.myService
    .loadSettings()
    .pipe(takeUntil(this.destroy$))
    .subscribe(resp => {
        this.submitForm.setValue({
                title: resp.title,
                description: resp.description
            },
            // skip emit on valueChanges subscriber
            {
                emitEvent: false,
                onlySelf: true
            },
        )
    });
```

### Get query params
Listen query params change and make request to get single post by id
```
import {  ActivatedRoute,  Router } from '@angular/router';
import { switchMap, takeUntil } from 'rxjs/operators';

export class EditPostComponent implements OnInit {

    destroy$ = new Subject();
    post: Post;
    constructor( private route: ActivatedRoute ) {}

    ngOnInit(): void {

        this.route.paramMap.pipe(
            switchMap((params) => {
                const id = params.get('id');
                return this.myService.loadPostById(id)
            }),
            takeUntil(this.destroy$)
        ).subscribe(data => {
            this.post = data;
        })

    }

    ngOnDestroy() {
        this.destroy$.next();
        this.destroy$.complete();
    }

}
```

### ng-container
```
<ng-container *ngIf="!(isAuthenticated$ | async); else logout">
  <li class="nav-item">
    <a class="nav-link" href="#" [routerLink]="['/auth']">Login</a>
  </li>
  <li class="nav-item">
    <a class="nav-link" href="#" [routerLink]="['/auth/signup']">Signup</a>
  </li>

</ng-container>
<ng-template #logout>
  <li class="nav-item">
    <a class="nav-link" href="#" [routerLink]="['/']">Logout</a>
  </li>
</ng-template>
```

