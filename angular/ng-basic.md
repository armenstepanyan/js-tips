### ngFor

```html
<ul>
  <li *ngFor="let item of list; index as i; trackBy: trackByFn">
    {{ item }} index = {{ i }}
    </li>
</ul>
```
[Stackblitz example](https://stackblitz.com/edit/a-ng-basic)

### ngIf
```html
<div *ngIf="condition; then thenBlock else elseBlock"></div>   
<ng-template #thenBlock>Then template</ng-template>  
<ng-template #elseBlock>Else template</ng-template> 

```

### ngFor extended version

```html
<ul>
  <ng-template ngFor let-item [ngForOf]="list" let-i="index" [ngForTrackBy]="trackByFn">
  <li>{{ item }}</li>
</ng-template>
</ul>
```

```typescript
  trackByFn(i, item) {
    // i- index, item - current item
    return i;
  }
```

### ngSwitch 
```html
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
```html
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

```html
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

```html
<input type="number" [(ngModel)]="border">
<div class="d1" [style.borderWidth.px]="border">
  Text
</div>
```

### ngStyle
```html
<div [ngStyle]="{'opacity': disabled ? '0.5' : '1'}"></div>
<p [ngStyle]="{'font-size':'14px', 'font-family':'Segoe Print'}">
  Text
</p>
```

### async pipe

```typescript
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
```html
 <button (click)="setData()">Set data</button>
  <p>
    {{ data$ | async }}
  </p>
```

### Use emitEvent: false
lets assume we have form and on form field value change we send save request
```typescript
this.submitForm = this.fb.group({
    title: [''],
    description: [''],
})
```
Create subscrition on form value change
```typescript
this.submitForm
    .valueChanges
    .pipe(debounceTime(500))
    .subscribe(data => {
        this.sendRequestToBackend();
    });
```
Now if we set form value with `this.submitForm.setValue({ ... })` it will trigger `this.submitForm.valueChanges` subscription again. To avoid this behavior need to pass `{emitEvent: false}` as argument

```typescript
this.myService
    .loadSettings()
    .pipe(takeUntil(this.destroy$))
    .subscribe(resp => {
        this.submitForm.setValue({
                title: resp.title,
                description: resp.description
            },
            // skip emit on valueChanges subscriber
            { emitEvent: false, onlySelf: true },
        )
    });
```

### Get query params
Listen query params change and make request to get single post by id
```typescript
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

### ngModel
One way data binding using `ngModel` and `ngModelChange`
```
<input type="text" [ngModel]="value" (ngModelChange)="setValue($event)">
<p>
  Value is {{ value }}
</p>
```
component.ts
```
export class MainComponent implements OnInit {
  value = "hello";
  
  setValue($event) {
    console.log($event)
    this.value = $event
  }

}
```

- ngModelChange with delay
```typescript
import { Component, OnInit } from '@angular/core';
import { Subject } from 'rxjs';
import { debounceTime, distinctUntilChanged } from 'rxjs/operators'

@Component({
  selector: 'app-main',
  templateUrl: './main.component.html',
  styleUrls: ['./main.component.scss']
})
export class MainComponent implements OnInit {
  value = "hello";
  valueChanged$ = new Subject<string>();
  constructor() { }

  ngOnInit() {
    // listening changes
    this.valueChanged$
    .pipe(
      debounceTime(500),
      distinctUntilChanged()
      )
    .subscribe(val => {
      console.log('valueChanged ', val);
      // update ngModel value
      this.value = val
    })
  }

  setValue($event) {
    // emit event on input value change
    this.valueChanged$.next($event);
  }

}

```

### ng-container
```html
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

### Input
We can listens `Input()` data changes in 2 ways:
- ngOnChanges hook
```typescript
export class MyInputComponent implements OnInit, OnChanges {

@Input() data!: number;
  ngOnChanges(changes: SimpleChanges): void {
    console.log(changes);
  }
}
```
Output
```json
{
  data: {
    currentValue: 2
    firstChange: false
    previousValue: 1
  },
  ...
 anotherProperty: {...}
}
```
- Using setter
```typescript
export class MyInputComponent implements OnInit, OnChanges {

  _data!: number;
  get data(): number {
    return this._data;
  }
  @Input() set data(value: number) {
    console.log(value); // <-- changes here
    this._data = value;
  }

  ngOnChanges(changes: SimpleChanges): void {
    console.log(changes); // note: we can still listen changes here
  }

}

```

### zone.runOutsideAngular
In case if we using setTimeout to change dom element behavior (size, value ...) we need to wrap it in `zone.runOutsideAngular` to skip changeDetection cycle

```html
<input #searchInput (blur)="resetFilter(searchInput)" />
```
component
```typescript
import { Component, OnInit, NgZone } from '@angular/core';

@Component({...})
export class DropdownComponent implements OnInit {

  constructor(public zone: NgZone) {}


  resetFilter(searchInput: any) {
    this.zone.runOutsideAngular(() => {
      setTimeout(() => {
        searchInput.value = '';
      }, 100);
    });
  }

}

```

