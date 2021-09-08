Creating `file-drop` directive. [Stackblitz](https://stackblitz.com/edit/a-drag-drop-directive?file=src/app/app.component.html)

```
import { HostListener, ElementRef, Directive, Output, EventEmitter, Input } from '@angular/core';

@Directive({
  selector: '[appFileDrop]'
})
export class FileDropDirective {
  @Input() dropClassName = '';
  @Output() fileDropped = new EventEmitter<any>();
  constructor(private elementRef: ElementRef) {}

  @HostListener('dragenter', ['$event']) public dragEnter(event) {
    event.stopPropagation();
    event.preventDefault();
  }

  @HostListener('dragover', ['$event']) public dragover(event) {
    event.stopPropagation();
    event.preventDefault();
    this.elementRef.nativeElement.classList.add(this.dropClassName);
  }

  @HostListener('dragleave') public dragleave() {
    this.elementRef.nativeElement.classList.remove(this.dropClassName);
  }

  @HostListener('drop', ['$event']) public drop(event) {
    event.stopPropagation();
    event.preventDefault();

    const files = event.dataTransfer.files;

    this.fileDropped.emit(files);
    this.elementRef.nativeElement.classList.remove(this.dropClassName);
  }
}

```

Html
```
<div
  appFileDrop
  class="dropzone"  
  (fileDropped)="fileDropped($event)"
  [dropClassName]="'active'"
>
  <p>
    Drop files here
  </p>

</div>
```

