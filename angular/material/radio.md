Angular material radio input. In html we are creating local reference to `mat-radio-button` by using `#radoBtn`, which is instance of `MatRadioButton`.

```html
<mat-radio-group [(ngModel)]="favoriteSeason">
	
  <mat-radio-button 
    *ngFor="let season of seasons" 
    [value]="season" 
    #radoBtn
    (click)="radioClick($event,radoBtn)"
>
		{{season}}
	</mat-radio-button>
  
</mat-radio-group>
<div>Your favorite season is: {{favoriteSeason}}</div>
```

Controller
```ts
export class RadioNgModelExample {
  favoriteSeason: string;
  seasons: string[] = ["Winter", "Spring", "Summer", "Autumn"];

  constructor() {
    // init radio with default selected value
    this.favoriteSeason = this.seasons[2];
  }

  radioClick($event: MouseEvent, radoBtn: MatRadioButton) {
   // uncheck current radio if already is selected
    if (this.favoriteSeason === radoBtn.value) {
      // stop event here
      $event.preventDefault();
      // uncheck radio
      radoBtn.checked = false;
      this.favoriteSeason = "";
    }
  }
}
```
[Stackblitz](https://stackblitz.com/edit/angular-mat-radio?file=src/app/radio-ng-model-example.ts)

### Set checked from component
html
```html
  <mat-radio-button
    *ngFor="let item of values"
    #radioBtn
    (click)="radioClick($event)"
    [value]="item.label"
    [name]="id"
  >
    {{ item.label }}
  </mat-radio-button>
```
Controller
```ts
export class RadioItemComponent implements OnInit, AfterViewInit {
  @Input() values;

  @ViewChildren('radioBtn') radioBtnList: QueryList<MatRadioButton>;
  constructor() {}

  ngOnInit() {}

  ngAfterViewInit() {
    setTimeout(() => {
      this.setChecked();
    }, 100);
  }

  setChecked() {
    const elements = this.radioBtnList.toArray();
    elements.map(el => {
      if (el.value === this.group.get(this.field.id).value) {
        el.checked = true;
      }
    });
  }

}
```
