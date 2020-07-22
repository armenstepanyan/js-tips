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

```
import { Directive } from '@angular/core';

@Directive({
  selector: '[appBold]'
})
export class BoldDirective {
  constructor() { }
}
```

Add `bold.directive` to `app.module`
```
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
```
<p appBold>
  This text will be bold
</p>
```

To customize element styles we can use `Renderer2` 

```
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
Our directive cn interact with user with `HostListener` and `HostBinding`.
The `@HostListener` decorator lets you subscribe to events of the DOM element that hosts an attribute directive, the <p> in this case.

Of course you could reach into the DOM with standard JavaScript and attach event listeners manually. There are at least three problems with that approach:

- You have to write the listeners correctly.
- The code must detach the listener when the directive is destroyed to avoid memory leaks.
- Talking to DOM API directly isn't a best practice.


```
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
```
private fontWeight = "normal"; 

@HostBinding("style.fontWeight") get getFontWeight(){
         
        return this.fontWeight;
    }
     
    @HostBinding("style.cursor") get getCursor(){
        return "pointer";
    }
```
Now on mouve over on element cursor will be changed to `pointer`. 
`@HostBinding("style.fontWeight") get getFontWeight()` links with  `style.fontWeight` property, which return getter  `getFontWeight`. 
And this getter return value `fontWeight` which is changing on mouse over.

Changing element color on init
```
  @HostBinding("style.color") get getColor() {
    return "red";
  }

```

[Example](https://stackblitz.com/edit/a-directive)

### Host property
Instead of using `HostListener` and `HostBinding` decorators to interact with user, we can simply define `host` property in `@Directive` decorator

```
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
```
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
```
<p appBold selectedSize="20px" [defaultSize]="'14px'">
  Start editing to see some magic happen :)
</p>
```

Now on mouse over `font-size` will be '20px', and on leave default - `14px`
[Example](https://stackblitz.com/edit/a-directive-2)
