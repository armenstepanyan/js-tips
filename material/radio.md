Angular material radio input. In html we are creating local reference to `mat-radio-button` by using `#radoBtn`, which is instance of `MatRadioButton`.

```
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
```
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
