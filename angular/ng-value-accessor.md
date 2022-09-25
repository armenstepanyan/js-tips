`NG_VALUE_ACCESSOR` allows angular component or directive to behave as a form control. We need to implement all functions from `ControlValueAccessor` 
interface and tell angular if someone want to access `NG_VALUE_ACCESSOR` need to use our custom class - `CustomCheckboxComponent`
```typescript
import { Component, OnInit } from '@angular/core';
import { ControlValueAccessor, NG_VALUE_ACCESSOR } from '@angular/forms'

@Component({
  selector: 'app-custom-checkbox',
  templateUrl: './custom-checkbox.component.html',
  providers:[
    {
      provide: NG_VALUE_ACCESSOR,
      useExisting: CustomCheckboxComponent,
      multi: true
    }
  ]
})
export class CustomCheckboxComponent implements OnInit, ControlValueAccessor {

  value = false;
  disabled = false;
  onChange!:  (val: boolean) => void;
  onTouched!: () => void;

  constructor() { }

  writeValue(obj: boolean): void {
    this.value = obj;
  }
  registerOnChange(fn: any): void {
    this.onChange = fn;
  }
  registerOnTouched(fn: any): void {
    this.onTouched = fn;
  }
  setDisabledState?(isDisabled: boolean): void {
    this.disabled = isDisabled;
  }

  ngOnInit(): void {
  }

  setValue(){
    if(this.disabled){
      return;
    }
    this.value = !this.value;
    this.onChange(this.value);
    this.onTouched();
  }

}
```

Usage

```
<form [formGroup]="submitForm" >
  <input type="text" formControlName="name">
  <app-custom-checkbox 
      formControlName="checkbox"
  ></app-custom-checkbox>

  <p>
    <button (click)="save()" [disabled]="!submitForm.touched">Save</button>
  </p>
 </form>
```
Component
```typescript

@Component({
  selector: 'app-submit-form',
  templateUrl: './submit-form.component.html',
  styleUrls: ['./submit-form.component.scss']
})
export class SubmitFormComponent implements OnInit {

  public submitForm!: FormGroup;
  constructor(private fb: FormBuilder) { }

  ngOnInit(): void {
    this.submitForm = this.fb.group({
      name: ['', Validators.required],
      checkbox: [{ value: false, disabled: false }]
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
