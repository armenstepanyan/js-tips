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
    if (this.submitForm.invalid) {
      return;
    }
    console.log(this.submitForm.value);
  }

}
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

