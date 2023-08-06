### NgTemplateOutlet
Inserts an embedded view from a prepared TemplateRef.

`ngTemplateOutletContext` A context object to attach to the EmbeddedViewRef. This should be an object, the object's keys
will be available for binding by the local template let declarations. Using the key $implicit in the context object will set its value as default.


```typescript
export class AppComponent {
  menu = [
    {
      id: 1,
      label: "AngularJS",
      url: "http://www.learn-angular.fr/angularJS"
    },
    {
      id: 2,
      label: "Angular",
      url: "http://www.learn-angular.fr/angular"
    }
  ]

}
```

View
```html
<div [ngTemplateOutlet]="templateRef" [ngTemplateOutletContext]="menu[0]"></div>
<div [ngTemplateOutlet]="templateRef" [ngTemplateOutletContext]="menu[1]"></div>

<ng-template #templateRef let-label="label" let-url="url">
	<div><a href="{{url}}">{{label}}</a></div>
</ng-template>

```

Or same we can use with `*ngTemplateOutlet`
```html
<ng-container *ngTemplateOutlet="templateRef; context: menu[0]"></ng-container>
<div *ngTemplateOutlet="templateRef; context: menu[1]"></div>
```


Create wrapper component
```typescript
@Component({
  selector: 'menu',
  template: `
   <button (click)="open = !open"> {{ open ? 'Close' : 'Open' }} </button>
    <div *ngIf="open">
      <div [ngTemplateOutlet]="templateRef"></div>
    </div>
  `,
})
export class MenuComponent {
  
  @ContentChild(TemplateRef) templateRef: TemplateRef<any>;
  
  open = true;  
  constructor() { }
  
}
```

By using `ContentChild` we can get template reference to `<ng-template>` inside component

View
```html
<menu>
  <ng-template>
    <ul>
      <li>111</li>
      <li>2222</li>
    </ul>
  </ng-template>
</menu>
```

[Stackblitz](https://stackblitz.com/edit/a-ngtemplateoutlet?file=src/app/menu.component.ts)

### Input Output
```typescript
@Component({
  selector: 'app-list',
  template: `   
    <div>
      <div [ngTemplateOutlet]="templateRef"></div>
      <button (click)="addMore()"> Add more</button>
    </div>
  `,
})
export class ListComponent {
  
  @ContentChild(TemplateRef) templateRef: TemplateRef<any>;
  @Input() list: Array<string> = [];
  @Output() itemAdd = new EventEmitter<void>();

  constructor() { }

  addMore() {
    this.itemAdd.emit();
  }

  removeItem(index: number) {
    this.list.splice(index,1);
  }
  
  
}
```

View

```html
<app-list [list]="list" #wrapper (itemAdd)="addNewItem()">
  <ng-template>
    <ul>
      <li *ngFor="let item of list; let i = index">
        {{ item }}
        <button (click)="wrapper.removeItem(i)"> &times;</button>
      </li>
    </ul>
  </ng-template>
</app-list>
```
The wrapper component `app-list` receive `list` as input, handle remove and emit output event when add new item. In view to call wrapper component function, we need to make reference to it `#wrapper` and then use functions - `wrapper.removeItem(i)`

