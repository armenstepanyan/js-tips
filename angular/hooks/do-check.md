### ngDoCheck
Detect and act upon changes that Angular can't or won't detect on its own. Called immediately after `ngOnChanges()` on every change detection run,
and immediately after `ngOnInit()` on the first run.

Here we have one parent component, 2 child ( `first` and `second`) and one (`third`) component inside `second`.

Parent
```
@Component({
  selector: "my-app",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent implements DoCheck {
  number = Math.random();

  doSomething() {
    console.log("doSomething");
    setTimeout(() => {}, 100);
  }

  updateNumber() {
    this.number = Math.random();
  }

  ngDoCheck() {
    console.log("Parent check");
  }
}
```

First Component
```
import {
  Component,
  Input,
  DoCheck,
  ChangeDetectionStrategy
} from "@angular/core";

@Component({
  selector: "first",
  template: `
    <h3>First</h3>
    <p>Number {{ number }}!</p>
    <p><button (click)="doSomething()">Do something child</button></p>
  `,
  styles: [],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class FirstComponent implements DoCheck {
  @Input() number: number;

  ngDoCheck() {
    console.log("First component check");
  }

  doSomething() {
    console.log("First component doSomething");
  }
}

```

Second component
```
import { Component, DoCheck } from "@angular/core";

@Component({
  selector: "second",
  template: `
    <h2>Second</h2>
    <p><button (click)="doSomething()">Do something child</button></p>
    <p><input [value]="'hello'" /></p>
    <hr />
    <third></third>
  `
})
export class SecondComponent implements DoCheck {
  ngDoCheck() {
    console.log("Second component check");
  }

  doSomething() {
    console.log("Second component doSomething");
  }
}

```

Third component
```
import { Component, DoCheck } from "@angular/core";

@Component({
  selector: "third",
  template: `
    <h2>Third</h2>
    <p><button (click)="doSomething()">Do something third</button></p>
    <p><button>No action</button></p>
    <p><input [value]="'hello'" /></p>
  `
})
export class ThirdComponent implements DoCheck {
  ngDoCheck() {
    console.log("Third component check");
  }

  doSomething() {
    console.log("Third component doSomething");
  }
}

```

After initialization we will see log messages (form parent to childs )
```
(1)
Parent check
First component check
Second component check
Third component check
(2)
Parent check
First component check
Second component check
Third component check
```

The (1) group log messages comes from `ngOnChanges`, and (2) group from `ngOnInit`.
If we click on `doSomething` button, no maater where, in parent, or in child, we again will see log messages from parent to child

Output
```
Parent check
First component check
Second component check
Third component check
```
[Example](https://stackblitz.com/edit/a-angular-test?file=src/app/app.component.html)

**Note** When you click on parent `doSomething` button, we will see those log messages 2 times. First comes from `click` event, and second runs after `setTimeout`. 
Angular uses `Zone.js` which checks for changes after micro/macro(setTimeout, promise...) tasks .

**Note 2** If we click on button that has no angular click handler (or write value in input which has not any handlers), `doCheck` will not triggers



