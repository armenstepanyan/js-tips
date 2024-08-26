### Angular Unit Testing

Angular projects come with Jasmine and Karma pre-configured. Jasmine is a testing framework, and Karma is a test runner.

Karma is a test runner developed by the Angular team to provide an optimal testing environment. It allows developers to execute their unit tests in a real browser environment, which helps ensure that the code will behave correctly in a real-world scenario.

### Key Features of Karma

1.  **Execution in Real Browsers**:

    -   Karma runs tests in actual web browsers (Chrome, Firefox, Safari, etc.), which helps identify issues that may only appear in certain environments.

2.  **Continuous Integration (CI) Friendly**:

    -   Karma can be integrated with CI tools like Jenkins, Travis CI, or GitLab CI to automate the testing process.

3.  **Real-time Feedback**:

    -   Karma provides real-time feedback on test results, showing which tests passed or failed as you write your code.

4.  **Test Runner and Reporter**:

    -   It can be configured to run tests on file changes, which is particularly useful during development. It also provides detailed reports on test results.

### Karma Configuration (karma.conf.js)

Here's a basic overview of the karma.conf.js file:

```typescript
module.exports = function (config) {
    config.set({
        basePath: '',
        frameworks: ['jasmine', '@angular-devkit/build-angular'],
        plugins: [
            require('karma-jasmine'),
            require('karma-chrome-launcher'),
            require('karma-jasmine-html-reporter'),
            require('karma-coverage'),
            require('@angular-devkit/build-angular/plugins/karma'),
        ],
        client: {
            clearContext: false, // leave Jasmine Spec Runner output visible in browser
        },
        coverageReporter: {
            dir: require('path').join(__dirname, './coverage/my-angular-app'),
            subdir: '.',
            reporters: [{ type: 'html' }, { type: 'text-summary' }],
        },
        reporters: ['progress', 'kjhtml'],
        port: 9876,
        colors: true,
        logLevel: config.LOG_INFO,
        autoWatch: true,
        browsers: ['Chrome'],
        singleRun: false,
        restartOnFileChange: true,
    });
};
```

### Running Tests with Karma

```
ng test

```

## Jasmine

Jasmine is a behavior-driven development (BDD) framework for testing JavaScript code.

### Jasmine Basics

1.  **Specs and Suites**:

    -   **Specs**: Individual test cases are called specs.
    -   **Suites**: Groups of related specs are called suites. Suites are defined using the `describe` function.

2.  **Matchers**:

    -   Jasmine provides various matchers to check the expected outcomes of your tests (`toBe`, `toEqual`, `toContain`, etc.).

3.  **Setup and Teardown**:

    -   You can define setup and teardown logic using `beforeEach` and `afterEach`.

### Basic Jasmine Syntax

Here's an example to illustrate the basic structure:

```typescript
describe('A suite', () => {
    it('contains a spec with an expectation', () => {
        expect(true).toBe(true);
    });
});
```

### Example 1

```typescript
import { Component } from '@angular/core';

@Component({
    selector: 'app-my-component',
    template: '<p>{{ message }}</p>',
})
export class MyComponent {
    message = 'Hello, World!';
}
```

Test

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { MyComponent } from './my-component.component';

describe('MyComponent', () => {
    let component: MyComponent;
    let fixture: ComponentFixture<MyComponent>;

    beforeEach(async () => {
        await TestBed.configureTestingModule({
            declarations: [MyComponent],
        }).compileComponents();
    });

    beforeEach(() => {
        fixture = TestBed.createComponent(MyComponent);
        component = fixture.componentInstance;
        fixture.detectChanges();
    });

    it('should create', () => {
        expect(component).toBeTruthy();
    });

    it('should have the message "Hello, World!"', () => {
        expect(component.message).toBe('Hello, World!');
    });

    it('should render the message in the template', () => {
        const compiled = fixture.nativeElement as HTMLElement;
        expect(compiled.querySelector('p')?.textContent).toContain('Hello, World!');
    });
});
```

### Key Points in the Test Suite

1.  **Configuration (`beforeEach`)**:

    -   The `TestBed.configureTestingModule` method sets up the testing module for the component.
    -   `compileComponents` compiles the component's template and CSS.

2.  **Component Creation**:

    -   The `TestBed.createComponent` method creates an instance of the component.
    -   `fixture.detectChanges` triggers change detection, ensuring the component's template is updated.

3.  **Assertions**:

    -   `expect` statements are used to assert that the component behaves as expected.

## fixture.nativeElement vs fixture.debugElement

In Angular testing, fixture.nativeElement and fixture.debugElement are used to interact with the component's DOM.

### `fixture.nativeElement`

-   `fixture.nativeElement` is a direct reference to the DOM element associated with the component.
-   It provides a raw access to the underlying DOM elements.
-   It is typically used for simpler operations where you want to interact with the DOM directly.

### `fixture.debugElement`

-   `fixture.debugElement` is an Angular-specific wrapper around the native DOM element.
-   It provides additional methods and properties to interact with the component and its elements in a more Angular-specific way.
-   `debugElement` offers more powerful querying and interaction capabilities, such as querying by directives, checking properties, and more.

```typescript
import { Component } from '@angular/core';

@Component({
    selector: 'app-example',
    template: `
        <div id="container">
            <p>{{ message }}</p>
            <button (click)="changeMessage()">Change Message</button>
        </div>
    `,
})
export class ExampleComponent {
    message = 'Hello, World!';

    changeMessage() {
        this.message = 'Message Changed!';
    }
}
```

Test

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ExampleComponent } from './example.component';
import { By } from '@angular/platform-browser';

describe('ExampleComponent', () => {
    let component: ExampleComponent;
    let fixture: ComponentFixture<ExampleComponent>;

    beforeEach(async () => {
        await TestBed.configureTestingModule({
            declarations: [ExampleComponent],
        }).compileComponents();
    });

    beforeEach(() => {
        fixture = TestBed.createComponent(ExampleComponent);
        component = fixture.componentInstance;
        fixture.detectChanges();
    });

    it('should create', () => {
        expect(component).toBeTruthy();
    });

    it('should have the initial message "Hello, World!"', () => {
        const nativeElement = fixture.nativeElement as HTMLElement;
        const p = nativeElement.querySelector('p');
        expect(p?.textContent).toBe('Hello, World!');
    });

    it('should change the message when the button is clicked (using nativeElement)', () => {
        const nativeElement = fixture.nativeElement as HTMLElement;
        const button = nativeElement.querySelector('button');
        button?.click();
        fixture.detectChanges();
        const p = nativeElement.querySelector('p');
        expect(p?.textContent).toBe('Message Changed!');
    });

    it('should have the initial message "Hello, World!" (using debugElement)', () => {
        const debugElement = fixture.debugElement;
        const p = debugElement.query(By.css('p')).nativeElement;
        expect(p.textContent).toBe('Hello, World!');
    });

    it('should change the message when the button is clicked (using debugElement)', () => {
        const debugElement = fixture.debugElement;
        const button = debugElement.query(By.css('button')).nativeElement;
        button.click();
        fixture.detectChanges();
        const p = debugElement.query(By.css('p')).nativeElement;
        expect(p.textContent).toBe('Message Changed!');
    });
});
```

-   **`nativeElement`**: Use it for simple and direct DOM interactions.
-   **`debugElement`**: Use it for more complex scenarios where you need Angular-specific querying and interaction capabilities.

Also, check this [PR](https://github.com/angular/angular/pull/5513/files)

```typescript
fixture.componentInstance == fixture.debugElement.componentInstance;
fixture.nativeElement == fixture.debugElement.nativeElement;
```

### Scenarios Where `debugElement` Is More Powerful

1.  **Querying by Angular Directives**:

    -   You can use `debugElement` to query elements based on directives, which is not possible with `nativeElement`.

2.  **Traversing Component Trees**:

    -   `debugElement` allows you to traverse the component tree, which is useful for testing interactions between nested components.

3.  **Querying Across Component Boundaries**:

    -   You can use `debugElement` to find elements outside of the immediate component context, which is not feasible with `nativeElement`.

Example

```typescript
import { Directive, ElementRef, Renderer2 } from '@angular/core';

@Directive({
    selector: '[appHighlight]',
})
export class HighlightDirective {
    constructor(el: ElementRef, renderer: Renderer2) {
        renderer.setStyle(el.nativeElement, 'backgroundColor', 'yellow');
    }
}

//-----------------------------------------//

@Component({
    selector: 'app-example',
    template: `
        <div id="container">
            <p appHighlight>{{ message }}</p>
            <button (click)="changeMessage()">Change Message</button>
        </div>
    `,
})
export class ExampleComponent {
    message = 'Hello, World!';

    changeMessage() {
        this.message = 'Message Changed!';
    }
}
```

Testing

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ExampleComponent } from './example.component';
import { HighlightDirective } from './highlight.directive';
import { By } from '@angular/platform-browser';

describe('ExampleComponent', () => {
    let component: ExampleComponent;
    let fixture: ComponentFixture<ExampleComponent>;

    beforeEach(async () => {
        await TestBed.configureTestingModule({
            declarations: [ExampleComponent, HighlightDirective],
        }).compileComponents();
    });

    beforeEach(() => {
        fixture = TestBed.createComponent(ExampleComponent);
        component = fixture.componentInstance;
        fixture.detectChanges();
    });

    it('should create', () => {
        expect(component).toBeTruthy();
    });

    it('should find element with appHighlight directive (using debugElement)', () => {
        const debugElement = fixture.debugElement;
        const highlightElement = debugElement.query(By.directive(HighlightDirective));
        expect(highlightElement).not.toBeNull();
        expect(highlightElement.nativeElement.style.backgroundColor).toBe('yellow');
    });

    it('should change the message when the button is clicked', () => {
        const debugElement = fixture.debugElement;
        const button = debugElement.query(By.css('button')).nativeElement;
        button.click();
        fixture.detectChanges();
        const p = debugElement.query(By.css('p')).nativeElement;
        expect(p.textContent).toBe('Message Changed!');
    });
});
```

### Key Points in the Example

1.  **Querying by Directive**:

    -   `debugElement.query(By.directive(HighlightDirective))` allows us to find the element with the `appHighlight` directive, which is not possible with `nativeElement`.

2.  **Checking Directive Effects**:

    -   We can verify that the `appHighlight` directive applied the correct style (`backgroundColor: 'yellow'`).

### Summary

-   **`nativeElement`** is limited to basic DOM manipulation and cannot handle Angular-specific queries or traverse component trees.
-   **`debugElement`** is essential for advanced Angular testing scenarios, such as querying by directives, components, and traversing component trees.

## Spying

Spying on component functions is an essential part of unit testing in Angular. Jasmine provides methods like spyOn and spyOnProperty to spy on and control the behavior of functions and properties within your components. A spy can stub any function and tracks calls to it and all arguments. A spy only exists in the describe or it block in which it is defined, and will be removed after each spec.

### Spying on Component Functions with spyOn

`spyOn` allows you to spy on a method of an object. This is useful for checking if a method was called, controlling its return value, and more.

```typescript
import { Component } from '@angular/core';

@Component({
    selector: 'app-example',
    template: `
        <div id="container">
            <p>{{ message }}</p>
            <button (click)="onClick()">Click Me</button>
        </div>
    `,
})
export class ExampleComponent {
    message = 'Hello, World!';

    onClick() {
        this.changeMessage();
    }

    changeMessage() {
        this.message = 'Message Changed!';
    }
}
```

Test

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ExampleComponent } from './example.component';

describe('ExampleComponent', () => {
    let component: ExampleComponent;
    let fixture: ComponentFixture<ExampleComponent>;

    beforeEach(async () => {
        await TestBed.configureTestingModule({
            declarations: [ExampleComponent],
        }).compileComponents();
    });

    beforeEach(() => {
        fixture = TestBed.createComponent(ExampleComponent);
        component = fixture.componentInstance;
        fixture.detectChanges();
    });

    it('should create', () => {
        expect(component).toBeTruthy();
    });

    it('should call changeMessage when onClick is called', () => {
        spyOn(component, 'changeMessage');
        component.onClick();
        expect(component.changeMessage).toHaveBeenCalled();
    });

    it('should change the message when changeMessage is called', () => {
        component.changeMessage();
        expect(component.message).toBe('Message Changed!');
    });

    it('should spy on changeMessage and return a different message', () => {
        spyOn(component, 'changeMessage').and.callFake(() => {
            component.message = 'Fake Message';
        });
        component.onClick();
        expect(component.message).toBe('Fake Message');
    });
});
```

The `spyOn` method can be used with `and.callThrough()` to call the actual implementation of the spied method while still keeping track of its calls. This is useful when you want to verify that a method was called but still execute its original code

```ts
it('should call changeMessage and execute its original code', () => {
    spyOn(component, 'changeMessage').and.callThrough();
    component.changeMessage();
    expect(component.message).toBe('Message Changed!');
    expect(component.changeMessage).toHaveBeenCalled();
});

it('should call changeMessage when anotherMethod is called', () => {
    spyOn(component, 'changeMessage').and.callThrough();
    component.anotherMethod();
    expect(component.message).toBe('Message Changed!');
    expect(component.changeMessage).toHaveBeenCalled();
});
```

**Summary: `and.callThrough()` vs. `and.callFake()`**

#### `and.callThrough()`

-   **Purpose**: Calls the actual implementation of the spied method while still tracking its calls.
-   **Use Case**: Useful when you want to verify that a method is called and also want the original method logic to be executed.

#### `and.callFake()`

-   **Purpose**: Replaces the implementation of the spied method with a custom function. This custom function can simulate different behaviors or return values for the method.
-   **Use Case**: Useful when you want to control the behavior of the method during testing, such as simulating different scenarios or isolating tests from actual method logic.

```ts
it('should spy on changeMessage and return a different message', () => {
    spyOn(component, 'changeMessage').and.callFake(() => {
        component.message = 'Fake Message';
    });
    component.onClick();
    expect(component.message).toBe('Fake Message');
});
```

### Summary: spyOnProperty

`spyOnProperty` is used to spy on getter or setter properties of an object. This is particularly useful when you need to control the values returned by a property or verify that a property was accessed or modified during a test.

### Use Cases

-   **Spying on Getters**: To control the value returned by a property getter.
-   **Spying on Setters**: To verify that a property setter was called or to control the behavior when a property is set.

**Syntax**

```ts
spyOnProperty(object, 'propertyName', accessType);
```

Example

```ts
import { Component } from '@angular/core';

@Component({
    selector: 'app-example',
    template: `
        <div id="container">
            <p>{{ message }}</p>
            <button (click)="onClick()">Click Me</button>
        </div>
    `,
})
export class ExampleComponent {
    private _message = 'Hello, World!';

    get message(): string {
        return this._message;
    }

    set message(value: string) {
        this._message = value;
    }

    onClick() {
        this.message = 'Message Changed!';
    }
}
```

Test

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ExampleComponent } from './example.component';

describe('ExampleComponent', () => {
    let component: ExampleComponent;
    let fixture: ComponentFixture<ExampleComponent>;

    beforeEach(async () => {
        await TestBed.configureTestingModule({
            declarations: [ExampleComponent],
        }).compileComponents();
    });

    beforeEach(() => {
        fixture = TestBed.createComponent(ExampleComponent);
        component = fixture.componentInstance;
        fixture.detectChanges();
    });

    it('should create', () => {
        expect(component).toBeTruthy();
    });

    it('should spy on message getter and return a different value', () => {
        spyOnProperty(component, 'message', 'get').and.returnValue('Spied Message');
        expect(component.message).toBe('Spied Message');
    });

    it('should spy on message setter and verify it is called', () => {
        const spy = spyOnProperty(component, 'message', 'set');
        component.onClick();
        expect(spy).toHaveBeenCalled();
    });

    it('should spy on message setter and control its behavior', () => {
        spyOnProperty(component, 'message', 'set').and.callFake((value: string) => {
            component['_message'] = `Fake ${value}`;
        });
        component.onClick();
        expect(component.message).toBe('Fake Message Changed!');
    });
});
```

#### spyOnProperty with observables

Incase if we have readonly observable for example and we can spy `subscribe` with a help of `spyOnProperty`

```ts
export class UserData {
    readonly user$: Observable<User>;

    constructor() {
        this.user$ = combineLatest([subject1, subject2]);
    }

    getUserData() {
        let userId = '';
        this.user$.subscribe((user) => {
            userId = user.id;
        });
    }
}
```

Testing

```ts
it('should get specnav UUID', () => {
    const mockUser = {
        id: 1,
        name: 'John',
    };
    const mockObservable = of(mockUser);

    spyOn(component.user$, 'subscribe').and.callFake((observer) => {
        return mockObservable.subscribe(observer);
    });
    const userId = component.getUserData();
    expect(userId).toEqual(1);
});
```

### Summary: Jasmine Spy Utilities

In addition to `spyOn`, `spyOnProperty`, and their variants, Jasmine provides `createSpy` and `createSpyObj` for creating mock functions and objects. These tools are useful for creating stand-alone spies or mock objects that can simulate the behavior of complex dependencies.

### `createSpy`

-   **Purpose**: Creates a standalone spy function.
-   **Use Case**: Useful when you need a mock function without an existing object or method to spy on.

#### Example

```typescript
describe('createSpy example', () => {
    it('should track calls to the spy', () => {
        const mySpy = jasmine.createSpy('mySpy');
        mySpy('arg1', 'arg2');

        expect(mySpy).toHaveBeenCalled();
        expect(mySpy).toHaveBeenCalledWith('arg1', 'arg2');
    });
});
```

### `createSpyObj`

-   **Purpose**: Creates an object with spy methods.
-   **Use Case**: Useful when you need to create a mock object that mimics the behavior of a dependency with multiple methods.

#### Example

Let's say you have a service with multiple methods, and you want to mock this service in your component tests.

#### Service Code

In `example.service.ts`:

```ts
export class ExampleService {
    getValue(): string {
        return 'real value';
    }

    setValue(value: string): void {
        // some implementation
    }
}
```

Component Code
In example.component.ts:

```ts
import { Component } from '@angular/core';
import { ExampleService } from './example.service';

@Component({
    selector: 'app-example',
    template: `
        <div>
            <p>{{ value }}</p>
            <button (click)="setValue('new value')">Set Value</button>
        </div>
    `,
})
export class ExampleComponent {
    value: string;

    constructor(private exampleService: ExampleService) {
        this.value = this.exampleService.getValue();
    }

    setValue(value: string): void {
        this.exampleService.setValue(value);
        this.value = value;
    }
}
```

Test Code
In example.component.spec.ts:

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ExampleComponent } from './example.component';
import { ExampleService } from './example.service';

describe('ExampleComponent', () => {
    let component: ExampleComponent;
    let fixture: ComponentFixture<ExampleComponent>;
    let exampleServiceSpy: jasmine.SpyObj<ExampleService>;

    beforeEach(async () => {
        const spy = jasmine.createSpyObj('ExampleService', ['getValue', 'setValue']);
        await TestBed.configureTestingModule({
            declarations: [ExampleComponent],
            providers: [{ provide: ExampleService, useValue: spy }],
        }).compileComponents();

        exampleServiceSpy = TestBed.inject(ExampleService) as jasmine.SpyObj<ExampleService>;
    });

    beforeEach(() => {
        fixture = TestBed.createComponent(ExampleComponent);
        component = fixture.componentInstance;
        exampleServiceSpy.getValue.and.returnValue('mock value');
        fixture.detectChanges();
    });

    it('should create', () => {
        expect(component).toBeTruthy();
    });

    it('should display value from the service', () => {
        expect(component.value).toBe('mock value');
    });

    it('should call setValue on the service when setValue is called', () => {
        component.setValue('test value');
        expect(exampleServiceSpy.setValue).toHaveBeenCalledWith('test value');
        expect(component.value).toBe('test value');
    });
});
```

### Use Cases

`createSpy`: Useful for simple, standalone functions that need to be tracked or controlled in tests.
`createSpyObj`: Ideal for mocking complex dependencies with multiple methods, such as services or APIs.

### Spy Types and calls.reset()

#### **`spyOn`**

-   **Usage**: `spyOn(object, 'methodName')`
-   **Resetting**: You can use `spy.calls.reset()` with spies created using `spyOn`.

```ts
it('should reset spy calls', () => {
    const spy = spyOn(component, 'someMethod');
    component.someMethod();
    expect(spy).toHaveBeenCalled();

    spy.calls.reset();
    expect(spy).not.toHaveBeenCalled();
});
```

#### . **`createSpy`**

-   **Usage**: `jasmine.createSpy('name')`
-   **Resetting**: You can use `spy.calls.reset()` with spies created using `createSpy`.

```ts
it('should reset spy created with createSpy', () => {
    const spy = jasmine.createSpy('mySpy');
    spy();
    expect(spy).toHaveBeenCalled();

    spy.calls.reset();
    expect(spy).not.toHaveBeenCalled();
});
```

#### **`createSpyObj`**

-   **Usage**: `jasmine.createSpyObj('name', ['method1', 'method2'])`
-   **Resetting**: You can use `spy.calls.reset()` with spies created using `createSpyObj`.

```ts
it('should reset spies in a spy object', () => {
    const spyObj = jasmine.createSpyObj('myObj', ['method1', 'method2']);
    spyObj.method1();
    expect(spyObj.method1).toHaveBeenCalled();

    spyObj.method1.calls.reset();
    expect(spyObj.method1).not.toHaveBeenCalled();
});
```

### Summary

-   **`calls.reset()`** is applicable to:

    -   Spies created with `spyOn`.
    -   Spies created with `createSpy`.
    -   Spies created with `createSpyObj` (individual methods).

-   **`calls.reset()`** is **not** applicable to:

    -   Spies created with `spyOnProperty` (for properties).

### OnPush detection

When using Angular's `OnPush` change detection strategy, you need to manually trigger change detection in your unit tests to ensure that the view is updated.

```ts
import { ChangeDetectorRef } from '@angular/core';

describe('should disable button', () => {
    let changeDetectorRef: ChangeDetectorRef;

    beforeEach(() => {
        changeDetectorRef = fixture.debugElement.injector.get(ChangeDetectorRef);
    });

    it('should disable brush tool', () => {
        component.isBtnDisabled = true;
        changeDetectorRef.detectChanges(); // Manually trigger change detection
        const btnEl = fixture.nativeElement.querySelector('#btn');
        expect(btnEl.getAttribute('disabled')).toEqual('true');
    });
});
```

#### spying with ngMock

```ts
const afterOpenedSubject = new Subject<void>();
const afterClosedSubject = new Subject<void>();
beforeEach(() => {
    ngMocks.stubMember(matDialog, 'open', () =>
        MockService(MatDialogRef, {
            afterOpened: () => afterOpenedSubject.asObservable(),
            afterClosed: () => afterClosedSubject.asObservable(),
        })
    );
    spyOn(matDialog, 'open').and.callThrough();
});
```
