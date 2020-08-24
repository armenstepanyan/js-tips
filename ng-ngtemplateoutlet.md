### NgTemplateOutlet
Inserts an embedded view from a prepared TemplateRef.

`ngTemplateOutletContext` A context object to attach to the EmbeddedViewRef. This should be an object, the object's keys
will be available for binding by the local template let declarations. Using the key $implicit in the context object will set its value as default.


```
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
```
<div [ngTemplateOutlet]="templateRef" [ngTemplateOutletContext]="menu[0]"></div>
<div [ngTemplateOutlet]="templateRef" [ngTemplateOutletContext]="menu[1]"></div>

<ng-template #templateRef let-label="label" let-url="url">
	<div><a href="{{url}}">{{label}}</a></div>
</ng-template>

```

Create wrapper component
```
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
```
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


