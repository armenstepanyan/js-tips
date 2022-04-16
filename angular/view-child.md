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

