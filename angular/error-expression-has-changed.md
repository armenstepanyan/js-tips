### NG0100: Expression has changed after it was checked

Angular throws an `ExpressionChangedAfterItHasBeenCheckedError` when an expression value has been changed after change detection has completed. 
Angular only throws this error in development mode
Angular tries to keep code file and template file values synced and for that it runs cycles.

### How to reproduce
#### Example 1
Change variables in lifecycle hooks.
```
import { Component, OnInit, AfterViewInit } from '@angular/core';

@Component({
  selector: "app-first",
  template: `
    <ng-container *ngIf="loading; else loaded"> Loading... </ng-container>
    <ng-template #loaded> Data loaded </ng-template>
  `
})
export class FirstComponent implements OnInit, AfterViewInit {
  loading = true;
  constructor() {}

  ngAfterViewInit(): void {
    this.loading = false; // <-- change variable value again
  }

  ngOnInit() {}
}


```

Before `AfterViewInit` angular prepared template file, created the View and in the end of it we try to change variable again.

**Note** this error appears only in local development because it has double check to find this kind of error. In prod mode it is not exists.

### Solution
- move variable change to `ngOnInit`
```
  ngAfterViewInit(): void {
  }
  
  ngOnInit() {
    this.loading = false;
  }
```

- Use Macro tasks after which change detection should run again 
```
  ngAfterViewInit(): void {
    setTimeout(() => {
      this.loading = false;
    }, 0)
  }  
```
OR by using `Promise.resolve()`
```
  ngAfterViewInit(): void {
    Promise.resolve().then(() => {
      this.loading = false;
    })
  }
```
- using ChangeDetectorRef. Forcing angular detect changes.
```
constructor(private cd: ChangeDetectorRef) {}

ngAfterViewInit(): void {
 this.loading = false;
 this.cd.detectChanges();
}
```
#### Example 2
Emit event from child component.
We need to create child component which will emit value change on `ngOnInit`
```
import { Component, OnInit, AfterViewInit } from '@angular/core';

@Component({
  selector: "app-first",
  template: `
    <ng-container *ngIf="loading; else loaded"> Loading... </ng-container>
    <ng-template #loaded> Data loaded </ng-template>
    <app-child (loadingChange)="loadingChange($event)"></app-child>
  `
})
export class FirstComponent implements OnInit, AfterViewInit {
  loading = true;
  constructor() {}

  loadingChange(newVal: boolean) {
    this.loading = newVal;
  }
}

```
Child component
```
import { Component, OnInit, Output, EventEmitter } from '@angular/core';


@Component({
  selector: 'app-child',
  templateUrl: './child.component.html'
})
export class ChildComponent implements OnInit {

  @Output() loadingChange = new EventEmitter<boolean>();
  constructor() { }

  ngOnInit() {
    this.loadingChange.emit(false);
  }
}
```
This happens because child component initialization happens here. Parent already updated 

![Lifecycle](/angular/lifecycle.png?raw=true)

We can solve this error by creating service and using service variable in parent and child components.
```
import { Component, OnInit, Output, EventEmitter } from '@angular/core';
import { MyService } from '../my.service';

@Component({
  selector: 'app-child',
  templateUrl: './child.component.html'
})
export class ChildComponent implements OnInit {

  @Output() loadingChange = new EventEmitter<boolean>();
  constructor(private service: MyService) { }

  ngOnInit() {
    this.service.updateLoading(false)
  }

}
```
Parent
```
import { Component, OnInit, AfterViewInit, ChangeDetectorRef } from '@angular/core';
import { MyService } from '../my.service';

@Component({
  selector: "app-first",
  template: `
    <ng-container *ngIf="service.loading; else loaded"> Loading... </ng-container>
    <ng-template #loaded> Data loaded </ng-template>
    <app-child></app-child>

  `
})
export class FirstComponent implements OnInit, AfterViewInit {
  constructor(private cd: ChangeDetectorRef, public service: MyService) {}

}

```


#### Example 3
Using getter
```
import { Component, OnInit, AfterViewInit } from '@angular/core';

@Component({
  selector: "app-first",
  template: `
    <p>{{ randomValue }}</p>
  `
})
export class FirstComponent implements OnInit, AfterViewInit {
  constructor() {}
  get randomValue() {
    return Math.random();
  }

}
```

Solution is to move randomValue in ngOnInit
```
ngOnInit() {
    this.randomValue = Math.random()
  }
```

