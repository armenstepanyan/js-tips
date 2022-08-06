## Reactive forms
At first we need to import  ReactiveFormsModule to our module

module.ts

``` import { ReactiveFormsModule } from '@angular/forms' ```


component.ts
```
import { FormGroup, FormBuilder, Validators } from '@angular/forms';

export class ConfigComponent implements OnInit {
  submitForm: FormGroup;

  constructor(private fb: FormBuilder) {}

  ngOnInit() {
    this.submitForm = this.fb.group({
      name: ['', [Validators.required, Validators.pattern(/^[a-zA-Z0-9-]+$/), Validators.maxLength(15)]],
    });


  }

  save() {
    this.submitForm.markAllAsTouched();
    if (this.submitForm.invalid) {
      return;
    }
    console.log(this.submitForm.value);
  }

}
```

html

```
<form [formGroup]="submitForm" >
 <input type="text" formControlName="name">
</form>
```

Display errors in html

```
<p *ngIf="submitForm.get('name').hasError('required')">Field is required</p>
<p *ngIf="submitForm.get('name').hasError('pattern')">Disallowed characters</p>
<p *ngIf="submitForm.get('name').hasError('maxlength')">Max length is 15</p>
```

Set form values. For `setValue` need to provide all keys.

```
this.submitForm.setValue({ name1: 'value1', name2: 'value2' });
```

To update only specefic keys use `patchValue`
Patches the value of the `FormGroup`. It accepts an object with control names as keys, and does its best to match the values to the correct controls in the group.
```
submitForm.patchValue({first: 'name1'});
```

## FormArray
A FormArray is responsible for managing a collection of AbstractControl, which can be a FormGroup, a FormControl, 
or another FormArray. [Example on Stackblitz](https://stackblitz.com/edit/a-form-array-group)

```
export class AppComponent implements OnInit {
  submitForm: FormGroup;

  constructor(private fb: FormBuilder) {}

  ngOnInit() {
    this.submitForm = this.fb.group({
      name: ["", [Validators.required]],
      mapping: this.fb.array([
        this.fb.group({
          id: ["", Validators.required],
          label: ["", Validators.required]
        })
      ])
    });

```

Set default one item in formArray

```
    const mapping = [
      this.fb.group({
        id: ["111", Validators.required],
        label: ["Label 1", Validators.required]
      })
    ];
    this.submitForm.setControl("mapping", this.fb.array(mapping));
```

Create `getter` to access `mapping` control in view
```
  get mapping(): FormArray {
    return this.submitForm.get('mapping') as FormArray;
  }
```

Handle add/remove events
```
  addRow() {
    const mapping = this.submitForm.controls.mapping as FormArray;
    mapping.push(
      this.fb.group({
        id: ['', Validators.required],
        label: ['', Validators.required],
      }),
    );
  }

  removeRow(index: number) {
    const mapping = this.submitForm.controls.mapping as FormArray;
    mapping.removeAt(index);
  }
```

Html
```
<form [formGroup]="submitForm">
  <mat-form-field>
    <input type="text" matInput formControlName="name" placeholder="Name"/>
    <mat-error *ngIf="submitForm.get('name').hasError('required')">
      Field is required
    </mat-error>

  </mat-form-field>

  <div>
    <ul formArrayName="mapping">
      <li *ngFor="let row of mapping.controls; let actionIndex = index; let first = first; let last = last">
        <div [formGroupName]="actionIndex">
          <mat-form-field>
            <input type="text" matInput formControlName="id" placeholder="ID"/>
            <mat-error
              *ngIf="submitForm.get('mapping').controls[actionIndex].get('id').hasError('required')">
              Field is required
            </mat-error>
          </mat-form-field>

          <mat-form-field>
            <input type="text" matInput placeholder="Label" formControlName="label"/>
            <mat-error
              *ngIf="submitForm.get('mapping').controls[actionIndex].get('label').hasError('required')">
              Field is required
            </mat-error>
          </mat-form-field>

          <button (click)="removeRow(actionIndex)" *ngIf="!(first && last)"> Delete </button>
          <button (click)="addRow()" *ngIf="last"> Add </button>
        </div>
      </li>
    </ul>
  </div>

  <p>
    <button mat-button (click)="save()">Save</button>
  </p>
</form>
```
To display error of formArray controls we need to get current control which return list of `FormControl` (id, label)
```
submitForm.get('mapping').controls[actionIndex].get('id').hasError('required')
```

## FormArray checkbox
```
values = ['Value 1', 'Value 2', 'Value 3'];
  ngOnInit() {
    this.myForm = this.fb.group({
      color: this.fb.array([], [Validators.required])
    });
  }
```

On change function need to add or remove `FormControl()`
```
  updateChkbxArray(id, isChecked, key) {
    const chArray = this.myForm.get(key) as FormArray;
    if (isChecked) {
      chArray.push(new FormControl(id));
    } else {
      const idx = chArray.controls.findIndex(x => x.value === id);
      chArray.removeAt(idx);
    }
  }
```
Html
```
<mat-checkbox
  *ngFor="let item of values"
   (change)="updateChkbxArray(item, $event.checked, 'color')"
   [checked]="myForm.get('color')?.value.includes(item)"
   value="item"
></mat-checkbox>

```
Init with default selected values
```
  this.myForm = this.fb.group({
    color: this.fb.array([ new FormControl('Value 2') ], [Validators.required])
  });
```

### Template driven forms
```
<form #fieldKeyForm="ngForm">
  <input
    placeholder="Field Key"
    [ngModel]="fieldKey"
    (ngModelChange)="save($event)"
    name="fieldKey"
    #name="ngModel"
    required
  />

  <p *ngIf="name?.errors?.required">Field is required</p>
</form>
<h3>Value is: {{ fieldKey }}</h3>

```
component.ts
```
import { Component, Output, EventEmitter, ViewChild } from '@angular/core';
import { NgForm } from '@angular/forms';

@Component({
  selector: 'my-app',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
})
export class AppComponent {
  @ViewChild('fieldKeyForm') currentForm: NgForm;
  @Output() valueChange = new EventEmitter<any>();
  fieldKey = '';

  save($event) {
    this.fieldKey = $event;
    if (this.currentForm.form.get('fieldKey').hasError('required')) {
      return;
    }

    this.valueChange.emit(this.fieldKey);
  }
}
```
[Example](https://stackblitz.com/edit/a-template-form?file=src/app/app.component.html)

### Typed Forms
As of Angular **14**, reactive forms are strictly typed by default.
You might wonder: why does the type of this control include null? This is because the control can become null at any time, by calling reset:
```
const email = new FormControl('a@gmail.com');
email.reset();
console.log(email.value); // null
```
TypeScript will enforce that you always handle the possibility that the control has become `null`. If you want to make this control non-nullable, you may use the `nonNullabl`e option. This will cause the control to reset to its **initial** value, instead of null
```
const email = new FormControl('a@gmail.com', {nonNullable: true});
email.setValue('b@gmail.com')
email.reset();
console.log(email.value); // a@gmail.com <-- initial value
```

### FormBuilder and NonNullableFormBuilder
An additional builder is available: `NonNullableFormBuilder`. This type is shorthand for specifying `{nonNullable: true}` on every control, and can eliminate significant boilerplate from large non-nullable forms
```
const fb = new FormBuilder();
const login = fb.nonNullable.group({
    email: 'a@gmail.com',
    password: '123',
});
login.setValue({
    email: 'b@gmail.com',
    password: '567'
    });

login.reset();
console.log(login.value) // {email: 'a@gmail.com', password: '123'}
```
