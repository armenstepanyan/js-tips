### ControlContainer
A base class for directives that contain multiple registered instances of NgControl.Here is the example how we can dynamically add form controls to parent fromGroup.

#### Person component
```typescript
@Component({
  selector: 'app-person',
  standalone: true,
  imports: [CommonModule, ReactiveFormsModule, AddressComponent],
  templateUrl: './person.component.html',
  styleUrls: ['./person.component.scss']
})
export class PersonComponent {

  form = new FormGroup({
    name: new FormControl()
  })

  submit(){
    console.log(this.form.value);
  }

}
```

Template
```html
<form [formGroup]="form" (ngSubmit)="submit()">
    <div>
        <input type="text" placeholder="Name" formControlName="name">
    </div>
    <app-address controlKey="address1"></app-address>
    
    <p>
        <button>Submit</button>
    </p>
</form>
```

#### Address component
```typescript
import { ReactiveFormsModule, ControlContainer, FormGroup, FormControl } from '@angular/forms';
import { Component, inject, Input } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-address',
  standalone: true,
  imports: [CommonModule, ReactiveFormsModule],
  viewProviders: [
    {
      provide: ControlContainer,
      useFactory: () => inject(ControlContainer, { skipSelf: true }) // the parent control will be used
    }
  ]
})
export class AddressComponent {

  @Input() controlKey: string = '';
  // instance of [formGroup] directive
  parentContainer = inject(ControlContainer);
  
  get parentFormGroup(){
    return this.parentContainer.control as FormGroup;
  }

  ngOnInit(){
    this.parentFormGroup.addControl(this.controlKey, new FormGroup({
      zipCode: new FormControl(''),
      street: new FormControl('')
    }))
  }

  ngOnDestroy(){
    this.parentFormGroup.removeControl(this.controlKey)
  }
}
```

Address template
```html
<div [formGroupName]="controlKey">
    <p>
        <input type="text" placeholder="zipCode" formControlName="zipCode">
    </p>
    <p>
        <input type="text" placeholder="street" formControlName="street">
    </p>
</div>
```
Without changing `ControlContainer` in address component we will get this error:
```console
Error: NG01053: formGroupName must be used with a parent formGroup directive.  You'll want to add a formGroup
    directive and pass it an existing FormGroup instance (you can create one in your class).
```

Output
```javascript
{
  address1: {zipCode: '123', street: 'Street 1'},
  name:"John"
}
```
In same way we can add second address with a different control key name
```html
<form [formGroup]="form" (ngSubmit)="submit()">
    <div>
        <input type="text" placeholder="Name" formControlName="name">
    </div>
    <app-address controlKey="address1"></app-address>
    <app-address controlKey="address2"></app-address>
    
    <p>
        <button>Submit</button>
    </p>
</form>
```
```javascript
{
  address1: {zipCode: '123', street: 'Street 1'},
  address1: {zipCode: '456', street: 'Street 2'},
  name:"John"
}
```
