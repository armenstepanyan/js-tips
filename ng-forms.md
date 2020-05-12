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
or another FormArray

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

