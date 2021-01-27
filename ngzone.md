### When apps update HTML
To understand how change detection works, first consider when the application needs to update the HTML. Typically, 
updates occur for one of the following reasons:

- Component initialization. For example, when bootstrapping an Angular application, Angular
loads the bootstrap component and triggers the ApplicationRef.tick() to call change detection and View Rendering.

- Event listener. The DOM event listener can update the data in an Angular component and also trigger change detection, as in the following example.

- HTTP Data Request. You can also get data from a server through an HTTP request.
- MacroTasks, such as setTimeout() or setInterval(). You can also update the data in the callback function of a macroTask such as setTimeout(). 
- MicroTasks, such as Promise.then(). Other asynchronous APIs return a Promise object (such as fetch), so the then() callback function can also update the data.
- Other async operations. In addition to addEventListener(), setTimeout() and Promise.then(), there are other operations that can update the data asynchronously.
Some examples include WebSocket.onmessage() and Canvas.toBlob().

The preceding list contains most common scenarios in which the application might change the data. Angular runs change detection whenever
it detects that data could have changed. The result of change detection is that the DOM is updated with new data. Angular detects the changes in different ways. 
For component initialization, Angular calls change detection explicitly. For asynchronous operations, 
Angular uses a zone to detect changes in places where the data could have possibly mutated and it runs change detection automatically.

```
export class AppComponent implements OnInit {
  number = 0;
  count = 0;
  constructor(private zone: NgZone) {}

  ngOnInit() {
    this.zone.runOutsideAngular(() => {
      setInterval(() => {
        this.count++;
        // update view via zone.run
        if (this.count % 2 === 0) {
          this.zone.run(() => {
            this.number = Math.random();
          });
        }
      }, 1000);
    });
  }
}
```
Now `count` and `number` will be updated in view per 2 seconds
[Stackblitz](https://stackblitz.com/edit/a-ngzone?file=src/app/app.component.ts)

```
import { Component, NgZone, OnInit } from "@angular/core";

export class AppComponent implements OnInit {
  number = 0;
  check = 0;
  constructor() {}

  ngOnInit() { }

  ngDoCheck() {
      this.check++;
      console.log('check ', this.check)
    }

    test() {
      this.number = Math.random();
      // setTimeout
      setTimeout(() => {
        console.log('setTimeout')
      }, 1000)
    }


}
```
View
```
<button (click)="test()">Test</button>
```
After initialization we will see 2 check messages in console. After clicking on button we will see 1 check message for click, and 1 more for `setTimeOut`.
Note: even if `test` function body is empty, angular will still check it  
[Stackblitz](https://stackblitz.com/edit/a-ngzone-2?file=src/app/app.component.ts)
