### customElements
We can create custom HTML elements, described by our class, with its own methods and properties, events and so on.
Once a custom element is defined, we can use it on par with built-in HTML elements.

There are two kinds of custom elements:
- Autonomous custom elements – "all-new" elements, extending the abstract HTMLElement class.
- Customized built-in elements – extending built-in elements, like a customized button, based on HTMLButtonElement etc.

```
class MyElement extends HTMLElement {
  constructor() {
    super();
    // element created
  }

  connectedCallback() {
    // browser calls this method when the element is added to the document
    // (can be called many times if an element is repeatedly added/removed)
  }

  disconnectedCallback() {
    // browser calls this method when the element is removed from the document
    // (can be called many times if an element is repeatedly added/removed)
  }

  static get observedAttributes() {
    return [/* array of attribute names to monitor for changes */];
  }

  attributeChangedCallback(name, oldValue, newValue) {
    // called when one of attributes listed above is modified
  }

  adoptedCallback() {
    // called when the element is moved to a new document
    // (happens in document.adoptNode, very rarely used)
  }

  // there can be other element methods and properties
}
```
After that, we need to register the element:
// let the browser know that <my-element> is served by our new class
customElements.define('my-element', MyElement);

**Note:** Custom element name must contain a hyphen `-`

### Create date time formatter element
```
<script>
class TimeFormatted extends HTMLElement { // (1)

  connectedCallback() {
    let date = new Date(this.getAttribute('datetime') || Date.now());

    this.innerHTML = new Intl.DateTimeFormat("default", {
      year: this.getAttribute('year') || undefined,
      month: this.getAttribute('month') || undefined,
      day: this.getAttribute('day') || undefined,
      hour: this.getAttribute('hour') || undefined,
      minute: this.getAttribute('minute') || undefined,
      second: this.getAttribute('second') || undefined,
      timeZoneName: this.getAttribute('time-zone-name') || undefined,
    }).format(date);
  }

}

customElements.define("time-formatted", TimeFormatted); // (2)
</script>

<!-- (3) -->
<time-formatted
    datetime="2020-09-22"
    year="numeric" month="long" day="numeric"
    hour="numeric" minute="numeric" second="numeric"
    time-zone-name="short"
></time-formatted>
```
[Example](https://stackblitz.com/edit/js-kmjsnt?file=index.js)

- The class has only one method `connectedCallback()` – the browser calls it when <time-formatted> element is added to page (or when HTML parser detects it),
and it uses the built-in Intl.DateTimeFormat data formatter, well-supported across the browsers, to show a nicely formatted time.
- We need to register our new element by customElements.define(tag, class).
- And then we can use it everywhere.

### Customized built-in elements
To use our custom element, insert a regular `<button>` tag, but add is="hello-button" to it:
```
<script>
// The button that says "hello" on click
class HelloButton extends HTMLButtonElement {
  constructor() {
    super();
    this.addEventListener('click', () => alert("Hello!"));
  }
}

customElements.define('hello-button', HelloButton, {extends: 'button'});
</script>

<button is="hello-button">Click me</button>
```

### Uppercase input example
```
class MyInput extends HTMLInputElement {
    constructor() {
        super();
        this.addEventListener('keyup', () => this.value = this.value.toUpperCase());
    }
}

customElements.define('uppercase-input', MyInput, {extends: 'input'});
<p>
  <input type="text" is="uppercase-input">
</p>
```
[Example](https://stackblitz.com/edit/js-kmjsnt?file=index.js)
