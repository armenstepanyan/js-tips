## Pipes
Bad code

```
<input [(ngModel)]="value">
Square is {{ getSquare() }}
```

In component 
```
export class AppComponent {
  value: number;
  getSquare() {
    return this.value * this.value;
  }
}

```

Good code using pipes
```
<input [(ngModel)]="value">
Square is {{ value | square }}
```

square.pipe.ts
```
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
```
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



