## Pipes
In Angular, a **pipe** is a feature that allows you to transform or format data before displaying it in the template. It is a simple way to apply common data manipulation tasks, such as formatting dates, converting text to uppercase, or sorting arrays.

Bad code

```html
<input [(ngModel)]="value">
Square is {{ getSquare() }}
```

In component 
```typescript
export class AppComponent {
  value: number;
  getSquare() {
    return this.value * this.value;
  }
}

```

Good code using pipes
```html
<input [(ngModel)]="value">
Square is {{ value | square }}
```

square.pipe.ts
```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'square',
})
export class SquarePipe implements PipeTransform {
  transform(value: number): number {
    return value * value;
  }
}
```

Add SquarePipe to module declaration
```typescript
import { NgModule } from '@angular/core';
import { SquarePipe } from './square.pipe';

@NgModule({
  declarations: [SquarePipe],
  imports: [],
  exports: [SquarePipe],
})
export class MyModule {}
```
If SquarePipe defined in separate module(MyModule) we can use it by importing MyModule, and in MyModule we need to export that pipe.

### Create Safe Html pipe
```typescript
import { Pipe, PipeTransform } from '@angular/core';
import { DomSanitizer, SafeHtml } from '@angular/platform-browser';

@Pipe({
  name: 'safeHtml',
})
export class SafeHtmlPipe implements PipeTransform {
  constructor(private sanitizer: DomSanitizer) {}

  transform(value: string): SafeHtml {
    return this.sanitizer.bypassSecurityTrustHtml(value);
  }
}
```
Usage 
```html
<div [innerHtml]="message | safeHtml"></div>
```
### Pure
By default pipes as pure (pure=true) meaning they are stateless and their output only depends on the input. They don't have side effects. However, you can create "impure" pipes that can have side effects and are re-evaluated on every change detection cycle.  For primitive types it will works on data change and for object types only if reference is changed.
```typescript
@Pipe({
  name: 'join',
  pure: false, // triger change on adding new value
})
export class JoinPipe implements PipeTransform {
  transform(values: Array<number>): string {
    return values.join('-');
  }
}
```
[Stackblitz](https://stackblitz.com/edit/angular-ivy-k8k7yp?file=src/app/join.pipe.ts)

#### Pure pipes
Angular will only recompute the output of a pure pipe when it detects a pure change to the input value (i.e., when a new object reference is passed as input).

### Impure pipes
They are re-evaluated on every change detection cycle, regardless of whether the input value has changed.

### Async Pipes
The AsyncPipe is a special pipe that is used for working with observables or promises. It automatically subscribes to an observable and returns the latest value.

```html
{{ observable$ | async }}

```
