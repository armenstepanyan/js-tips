An Attribute directive changes the appearance or behavior of a DOM element.

### Directives overview
There are three kinds of directives in Angular:

- Components — directives with a template.
- Structural directives — change the DOM layout by adding and removing DOM elements.
- Attribute directives — change the appearance or behavior of an element, component, or another directive.

`Components` are the most common of the three directives.
`Structural Directives` change the structure of the view. Two examples are `NgFor` and `NgIf`.
`Attribute directives` are used as attributes of elements. The built-in `NgStyle` directive in the Built-in directives guide, 
for example, can change several element styles at the same time.

An attribute directive minimally requires building a controller class annotated with `@Directive`, which specifies the selector that identifies the attribute. 
The controller class implements the desired directive behavior.

```
ng generate directive bold
```

Will generate this file `src/app/bold.directive.ts` 

```typescript
import { Directive } from '@angular/core';

@Directive({
  selector: '[appBold]'
})
export class BoldDirective {
  constructor() { }
}
```

Add `bold.directive` to `app.module`
```typescript
import { BoldDirective} from './bold.directive';
.....

@NgModule({
  imports:      [ BrowserModule],
  declarations: [ AppComponent, BoldDirective ],
  bootstrap:    [ AppComponent ]
})
export class AppModule { }
```

Import ElementRef which will give us reference to element 
```
    constructor(private elementRef: ElementRef){
         
        this.elementRef.nativeElement.style.fontWeight = "bold";
    }
```

Now we can use `appBold` directive in `app.component.html`.
```html
<p appBold>
  This text will be bold
</p>
```

To customize element styles we can use `Renderer2` 

```typescript
import {Directive, ElementRef, Renderer2} from '@angular/core';
 
@Directive({
    selector: '[bold]'
})
export class BoldDirective{
     
    constructor(private elementRef: ElementRef, private renderer: Renderer2){
         
        this.renderer.setStyle(this.elementRef.nativeElement, "font-weight", "bold");
    }
}
```

### Interacting with user
Our directive can interact with user with `HostListener` and `HostBinding`.
The `@HostListener` decorator lets you subscribe to events of the DOM element that hosts an attribute directive, the `<p>` in this case.

Of course you could reach into the DOM with standard JavaScript and attach event listeners manually. There are at least three problems with that approach:

- You have to write the listeners correctly.
- The code must detach the listener when the directive is destroyed to avoid memory leaks.
- Talking to DOM API directly isn't a best practice.


```typescript
import { HostListener } from '@angular/core';
.....

export class BoldDirective {
.......
@HostListener("mouseenter") onMouseEnter() {
        this.setFontWeight("bold");
    }
 
    @HostListener("mouseleave") onMouseLeave() {
        this.setFontWeight("normal");
    }
 
    private setFontWeight(val: string) {
        this.renderer.setStyle(this.element.nativeElement, "font-weight", val);
    }
}
```
Now element will get bold style only on mouse over. 

### HostBinding
Decorator that marks a DOM property as a host-binding property and supplies configuration metadata. 
Angular automatically checks host property bindings during change detection, and if a binding changes it updates the host element of the directive.
```typescript
private fontWeight = "normal"; 

@HostBinding("style.fontWeight") get getFontWeight(){
         
        return this.fontWeight;
    }
     
    @HostBinding("style.cursor") get getCursor(){
        return "pointer";
    }
```
Now on mouse over on element cursor will be changed to `pointer`. 
`@HostBinding("style.fontWeight") get getFontWeight()` links with  `style.fontWeight` property, which return getter  `getFontWeight`. 
And this getter return value `fontWeight` which is changing on mouse over.

Changing element color on init
```typescript
  @HostBinding("style.color") get getColor() {
    return "red";
  }

```

[Example](https://stackblitz.com/edit/a-directive)

### Host property
Instead of using `HostListener` and `HostBinding` decorators to interact with user, we can simply define `host` property in `@Directive` decorator

```typescript
import {Directive, ElementRef, Renderer2} from '@angular/core';
  
@Directive({
    selector: '[bold]',
    host: {
        '(mouseenter)': 'onMouseEnter()',
        '(mouseleave)': 'onMouseLeave()'
    }
})
export class BoldDirective{
      
    constructor(private element: ElementRef, private renderer: Renderer2){
          
        this.renderer.setStyle(this.element.nativeElement, "cursor", "pointer");
    }
     
    onMouseEnter(){
        this.setFontWeight("bold");
    }
    onMouseLeave(){
        this.setFontWeight("normal");
    }
    private setFontWeight(val: string) {
        this.renderer.setStyle(this.element.nativeElement, "font-weight", val);
    }
}
```

### Get input params in directive
```typescript
export class BoldDirective {
  @Input() selectedSize = "18px";
  @Input() defaultSize = "16px";

  constructor(private element: ElementRef, private renderer: Renderer2) {
    this.renderer.setStyle(this.element.nativeElement, "cursor", "pointer");
  }

  onMouseEnter() {
    this.setFontWeight("bold");
    this.renderer.setStyle(this.element.nativeElement, "font-size", this.selectedSize);
  }
  onMouseLeave() {
    this.setFontWeight("normal");
    this.renderer.setStyle(this.element.nativeElement, "font-size", this.defaultSize);
  }
  private setFontWeight(val: string) {
    this.renderer.setStyle(this.element.nativeElement, "font-weight", val);
  }
}
```

app.component.html
```html
<p appBold selectedSize="20px" [defaultSize]="'14px'">
  Start editing to see some magic happen :)
</p>
```

Now on mouse over `font-size` will be '20px', and on leave default - `14px`
[Example](https://stackblitz.com/edit/a-directive-2)

### Example expand-text directive
```typescript
import { Directive, ElementRef, AfterViewInit, Input } from '@angular/core';

@Directive({
  selector: '[appHeaderExpand]',
  host: {
    '(mouseenter)': 'expandText()',
    '(mouseleave)': 'collapseText()',
  },
})
export class HeaderExpandDirective implements AfterViewInit {
  initialText = '';
  textLength = 0;
  // is no input value was provided - use default
  @Input() allowedTextLength = 64;
  constructor(private element: ElementRef) {}

  ngAfterViewInit() {
    this.initialText = this.element.nativeElement.innerText;
    this.textLength = (this.element.nativeElement.innerText || '').length;
    const neddToExpand = this.textLength < this.allowedTextLength;
    this.toggleText(neddToExpand);
  }

  private toggleText(needExpand: boolean) {
    if (this.textLength <= this.allowedTextLength) {
      return;
    }

    this.element.nativeElement.innerText = needExpand
      ? this.initialText
      : this.initialText.substr(0, this.allowedTextLength) + '...';
  }

  expandText() {
    this.toggleText(true);
  }
  collapseText() {
    this.toggleText(false);
  }
}
```
Usage
```html
<p appHeaderExpand [allowedTextLength]="20">
  Lorem ipsum dolor sit amet consectetur adipisicing elit. Consectetur nihil,
  molestias obcaecati doloribus ducimus enim numquam ab dolores necessitatibus
  itaque.
</p>

<p appHeaderExpand>
  Lorem ipsum dolor sit amet consectetur adipisicing elit. Officia a eligendi ab
  accusamus aperiam veniam nobis libero similique, praesentium voluptatem nemo
  dolorum? Delectus nesciunt esse numquam, temporibus 
</p>
```
[Stackblitz example](https://stackblitz.com/edit/a-angular-expand-directive?file=src/app/header-expand.directive.ts)
