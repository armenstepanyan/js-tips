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
      name: ['', [Validators.required]],
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

