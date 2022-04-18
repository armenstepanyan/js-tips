Access child component function with template variable

Child component
```
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-child',
  template: `<p>{{ number }}</p>`,
  styleUrls: ['./child.component.scss']
})
export class ChildComponent implements OnInit {

  number = 0;
  constructor() { }

  ngOnInit() {
  }

  increment(){
    this.number++;
  }

  decrement() {
    this.number--;
   }

}
```
Parent component
```
<app-child #counter></app-child>

<button (click)="counter.increment()">+</button>
<button (click)="counter.decrement()">-</button>
```

### ViewChild
```
import { ChildComponent } from "../child/child.component";

@Component({
  selector: "app-content",
  templateUrl: "./content.component.html",
  styleUrls: ["./content.component.scss"],
})
export class ContentComponent implements OnInit {

  @ViewChild(ChildComponent, { static: false }) private counterComponent: ChildComponent | undefined;
  constructor() {}

  ngOnInit() {}

  increment() {
    this.counterComponent.increment()
  }
  decrement() {
    this.counterComponent.decrement()
  }
}
```
View
```
<app-child></app-child>
<button (click)="increment()">+</button>
<button (click)="decrement()">-</button>
```
Or we can get reference of template variable
```
@ViewChild('counter', { static: false }) private counterComponent: ChildComponent | undefined;
----
<app-child #counter></app-child>
<button (click)="increment()">+</button>
<button (click)="decrement()">-</button>

```

Get text content
```
<p #nameText></p>
<p>{{nameText.textContent}}</p>
<button (click)="change()">Change</button>
```

Change text
```
@ViewChild("nameText", { static: false }) nameParagraph:  ElementRef | undefined;

  change() {
    if (this.nameParagraph !== undefined) {
      console.log(this.nameParagraph.nativeElement.textContent);
      this.nameParagraph.nativeElement.textContent = "hello";
    }
  }
```

### ContentChild
In case if child component has `ng-content` to access text content inside ng-content we need to use `ContentChild`. ViewChild will return empty because template is outside of child component

Child component view
```
<ng-content></ng-content>
<button (click)="change()">Change</button>
```

Parent
```
<app-child >
  <h3 #headerContent>Welcome {{name}}!</h3>
</app-child>
```

Child.component.ts
```
@ContentChild("headerContent", {static:false})   header: ElementRef | undefined;

change() {
    if(this.header !==undefined){
        console.log(this.header);
        this.header.nativeElement.textContent = "Hello to world!";
    }
}
```


### ContentChild vs ViewChild
`ContentChild` search element that was projected in component. In other words it get refer to element that is inside component opening/closing tags
```
<app-parent>
  <h1>...</h1>
  <app-child></app-child>
</app-parent> 
```
Now to get reference of `app-child` we need to use `ContentChild`. For this example `ViewChild` will return undefined.In simple words ViewChild can get reference to elements that is inside component html

Parent component usage
```
<app-content>
  <app-date></app-date>
</app-content>
```

Content.ts
```
export class ContentComponent implements OnInit, AfterContentInit  {

  @ContentChild(DateComponent, { static: false }) dateComp: DateComponent;

  constructor() {}

  ngAfterContentInit(): void {
    console.log(this.dateComp)
  }


  ngOnInit() {
  // Now we can get/change public properties of DateComponent. 
    setTimeout(() => {
      this.dateComp.today = new Date();
    }, 2000)
  }

}
```

Content.html
```
<p> Content works! </p>
<ng-content></ng-content> <---- here will be displayed projection content

```

DateComponent
```
@Component({
  selector: 'app-date',
  template: `<p>Date is {{ today }}</p>`,
  styleUrls: ['./date.component.scss']
})
export class DateComponent implements OnInit {

  today = new Date();
  constructor() { }

  ngOnInit() {
  }

}

```


### ContentChildren
Use to get the `QueryList` of elements or directives from the content DOM. Any time a child element is added, removed, or moved, the query list will be updated, and the changes observable of the query list will emit a new value

```
@ContentChildren(DateComponent) dateCompList: QueryList<DateComponent>;

  ngAfterContentInit(): void {
    console.log(this.dateCompList.toArray())
  }  
```
View
```
<app-content>
  <app-date></app-date>
  <app-date></app-date>
  <app-date></app-date>
</app-content>
```

