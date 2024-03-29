**ViewChild** is an Angular decorator that allows a component to access a child component, directive, or element in its template. It provides a way for a parent component to interact with its child components or elements.

In simple words, ViewChild helps a parent component "see" or "get a reference to" a specific element or component that is declared in its template.

Using `ViewChild` to access DOM elements
```typescript
import { Component, ViewChild, ElementRef, AfterViewInit } from '@angular/core';

@Component({
  selector: 'app-parent',
  template: `
    <div #myDiv>
      This is a div element.
    </div>
    <button (click)="changeText()">Change Text</button>
  `
})
export class ParentComponent implements AfterViewInit {
  @ViewChild('myDiv', { static: false }) myDiv: ElementRef;

  ngAfterViewInit() {
    // The DOM element is now accessible after the view has been initialized
    this.myDiv.nativeElement.style.backgroundColor = 'lightblue';
  }

  changeText() {
    this.myDiv.nativeElement.textContent = 'Text changed!';
  }
}
```
Note: The second argument `{ static: false }` is used because we want to access the element after the view has been initialized.

#### Using `ViewChild` to access angular elements
```typescript
import { Component, ViewChild } from '@angular/core';
import { ChildComponent } from './child.component';

@Component({
  selector: 'app-parent',
  template: `
    <app-child></app-child>
  `
})
export class ParentComponent {
  @ViewChild(ChildComponent) childComponent: ChildComponent;

  ngAfterViewInit() {
    this.childComponent.doSomething();
  }
}

```

### Example
Access child component function with template variable

Child component
```typescript
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
```html
<app-child #counter></app-child>

<button (click)="counter.increment()">+</button>
<button (click)="counter.decrement()">-</button>
```

### ViewChild
```typescript
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
```html
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
```html
<p #nameText></p>
<p>{{nameText.textContent}}</p>
<button (click)="change()">Change</button>
```

Change text
```typescript
@ViewChild("nameText", { static: false }) nameParagraph:  ElementRef | undefined;

  change() {
    if (this.nameParagraph !== undefined) {
      console.log(this.nameParagraph.nativeElement.textContent);
      this.nameParagraph.nativeElement.textContent = "hello";
    }
  }
```

### ContentChild
In case if child component has `ng-content` to access text content inside ng-content we need to use `ContentChild`. ViewChild will return empty because template is outside of child component.

`ContentChild` is an Angular decorator similar to ViewChild, but instead of querying for elements or components in the component's own template, it allows you to query for elements or components that are projected into the component via content projection.

In simpler terms, `ContentChild` lets you access elements or components that are passed into a component as part of its content from the parent component.

Child component view
```html
<ng-content></ng-content>
<button (click)="change()">Change</button>
```

Parent
```html
<app-child >
  <h3 #headerContent>Welcome {{name}}!</h3>
</app-child>
```

Child.component.ts
```typescript
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
```html
<app-parent>
  <h1>...</h1>
  <app-child></app-child>
</app-parent> 
```
Now to get reference of `app-child` we need to use `ContentChild`. For this example `ViewChild` will return undefined.In simple words ViewChild can get reference to elements that is inside component html

Parent component usage
```html
<app-content>
  <app-date></app-date>
</app-content>
```

Content.ts
```typescript
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
```html
<p> Content works! </p>
<ng-content></ng-content> <---- here will be displayed projection content

```

DateComponent
```typescript
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

### static: true
This allows to access template in `ngOnInit` function before changedetection cycle runs
```typescript
@ViewChild('el', { static: true })
```

### ContentChildren
Use to get the `QueryList` of elements or directives from the content DOM. Any time a child element is added, removed, or moved, the query list will be updated, and the changes observable of the query list will emit a new value

```typescript
@ContentChildren(DateComponent) dateCompList: QueryList<DateComponent>;

  ngAfterContentInit(): void {
    console.log(this.dateCompList.toArray())
  }  
```
View
```html
<app-content>
  <app-date></app-date>
  <app-date></app-date>
  <app-date></app-date>
</app-content>
```

### Content projection select
```html
<app-content>
  <h1 header>This is header</h1>

  <app-date></app-date>
  <app-date></app-date>
  <app-date></app-date>
  <h3 footer>This is footer</h3>
</app-content>
```

Content.html
```html
<ng-content select="[header]"></ng-content>
<p>content works!</p>

<ng-content></ng-content>
<p>Some info</p>
<ng-content select="[footer]"></ng-content>
```


