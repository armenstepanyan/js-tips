### Bubbling
When an event happens on an element, it first runs the handlers on it, then on its parent, then all the way up on other ancestors.
Let’s say we have 3 nested elements `FORM > DIV > P` with a handler on each of them:
```
<form onclick="alert('form')">FORM
  <div onclick="alert('div')">DIV
    <p onclick="alert('p')">P</p>
  </div>
</form>
```
A click on the inner `<p>` first runs onclick:

- On that `<p>`
- Then on the outer `<div>`
- Then on the outer `<form>`

And so on upwards till the document object. So if we click on `<p>`, then we’ll see 3 alerts: `p → div → form`
The process is called “bubbling”, because events `bubble` from the inner element up through parents like a bubble in the water.

### event.target
A handler on a parent element can always get the details about where it actually happened. 
The most **deeply** nested element that caused the event is called a target element, accessible as event.target.

**Note**  the differences from `this` (=event.currentTarget):
- `event.target` – is the “target” element that initiated the event, it doesn’t change through the bubbling process.
- `this` – is the “current” element, the one that has a currently running **handler** on it.

For instance, if we have a single handler form.onclick, 
then it can “catch” all clicks inside the form. No matter where the click happened, it bubbles up to `<form>` and runs the handler.

In form.onclick handler:

- `this (=event.currentTarget)` is the `<form>` element, because the handler runs on it.
- `event.target` is the actual element inside the form that was clicked.

It’s possible that event.target could equal this – it happens when the click is made directly on the `<form>` element

[Example](https://jsfiddle.net/armen_stepanyan/c79qy3et/)

**Stopping bubbling** with `event.stopPropagation()`
#### event.stopImmediatePropagation()
If an element has multiple event handlers on a single event, then even if one of them stops the bubbling, the other ones still execute.

In other words, `event.stopPropagation()` stops the move upwards, but on the current element all other handlers will run.

To stop the bubbling and prevent handlers on the current element from running, there’s a method `event.stopImmediatePropagation()`. After it no other handlers execute.

### Capturing

There’s another phase of event processing called “capturing”. It is rarely used in real code, but sometimes can be useful.

The standard DOM Events describes 3 phases of event propagation:

- Capturing phase – the event goes down to the element.
- Target phase – the event reached the target element.
- Bubbling phase – the event bubbles up from the element

To catch an event on the capturing phase, we need to set the handler `capture` option to `true`
```
elem.addEventListener(..., {capture: true})
// or, just "true" is an alias to {capture: true}
elem.addEventListener(..., true)
```

There are two possible values of the capture option:

- If it’s false (default), then the handler is set on the bubbling phase.
- If it’s true, then the handler is set on the capturing phase.

[Example](https://jsfiddle.net/armen_stepanyan/50dt68vb/3/)

```
var el = document.getElementById("message");

function capturingOnClick1(event) {
    el.innerHTML += "DIV event capture<br>";
}

function capturingOnClick2(event) {
    el.innerHTML += "Button event capture<br>";
}

var useCapture  = false;
document.getElementById('div1').addEventListener("click", capturingOnClick1, useCapture);
document.getElementById('btn1').addEventListener("click", capturingOnClick2, useCapture);
```
Now if we click on `button` the otput will be 
```
Button event capture
DIV event capture
```

If we change `useCapture` to `true` and click again on `button` output will be 
```
DIV event capture
Button event capture
```

**Note** that while formally there are 3 phases, the 2nd phase (`target phase`: the event reached the element) 
is not handled separately: handlers on both capturing and bubbling phases trigger at that phase.

```
<form>FORM
  <div>DIV
    <p>P</p>
  </div>
</form>

<script>
  for(let elem of document.querySelectorAll('*')) {
    elem.addEventListener("click", e => alert(`Capturing: ${elem.tagName}`), true);
    elem.addEventListener("click", e => alert(`Bubbling: ${elem.tagName}`));
  }
</script>
```

The code sets click handlers on every element in the document to see which ones are working.

If you click on `p`, then the sequence is:
- `HTML → BODY → FORM → DIV` (capturing phase, the first listener):
- `P` (target phase, triggers **two times**, as we’ve set two listeners: capturing and bubbling)
- `DIV → FORM → BODY → HTML` (bubbling phase, the second listener).

[Example](https://jsfiddle.net/armen_stepanyan/tnxh5bag/1/)

There’s a property `event.eventPhase` that tells us the number of the phase on which the event was caught. But it’s rarely used, because we usually know it in the handler.

### Summary
When an event happens – the most nested element where it happens gets labeled as the `target element` (event.target).

- Then the event moves down from the document `root` to `event.target`, calling handlers assigned with `addEventListener(..., true)` on the way (true is a shorthand for `{capture: true}`).
- Then handlers are called on the target element itself.
- Then the event bubbles up from `event.target` to the `root`, calling handlers assigned using on<event>, HTML attributes and addEventListener without the 3rd argument or with the 3rd argument false/`{capture:false}`.

Each handler can access event object properties:

- `event.target` – the deepest element that originated the event.
- `event.currentTarget` (=this) – the current element that handles the event (the one that has the handler on it)
- `event.eventPhase` – the current phase `(capturing=1, target=2, bubbling=3)`.
