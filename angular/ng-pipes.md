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

### Create Safe Html pipe
```
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
```
<div [innerHtml]="message | safeHtml"></div>
```
### Pure
By default pipes as pure (pure=true). For primitive types it will works on data change and for object types only if reference is changed.
```
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

