### Create custom validator for reactive form

This custom validations will automatically run every time when control value must change

Create `letter-validator.ts` that will validate only letters

```typescript
import { AbstractControl } from "@angular/forms";

export default function LetterValidator(control: AbstractControl): { [key: string]: any } | null {
  const reg = new RegExp("^[a-zA-Z]+$");
  return reg.test(control.value) ? null : { invalidSymbols: true };
}

```

App component
```typescript
import { Component } from "@angular/core";
import LetterValidator from "./validators/letter-validator";
import { FormControl } from "@angular/forms";

@Component({
  selector: "my-app",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  control = new FormControl("Value", [LetterValidator]);
  constructor() {}
}
```

View
```html
<input [formControl]="control" type="text" />
<div style="color: red" *ngIf="control.hasError('invalidSymbols')">
  Only letters allowed!
</div>
```
[Stackblitz](https://stackblitz.com/edit/a-custom-validator?file=src/app/app.component.html)
