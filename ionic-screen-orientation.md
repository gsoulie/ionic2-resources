[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Screen Orientation

### Plugin installation

```
$ ionic cordova plugin add cordova-plugin-screen-orientation
$ npm install --save @ionic-native/screen-orientation
```

*app.module.ts*
```javascript
import { ScreenOrientation } from '@ionic-native/screen-orientation';
...

@NgModule({
  declarations: [
    MyApp,
    HomePage
  ],
  imports: [
    BrowserModule,
    IonicModule.forRoot(MyApp)
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp,
    HomePage
  ],
  providers: [
    StatusBar,
    SplashScreen,
    ScreenOrientation,
    {provide: ErrorHandler, useClass: IonicErrorHandler}
  ]
})
export class AppModule {}
```

### Usage

*home.ts*

```javascript
import { Component, OnInit } from '@angular/core';
import { NavController, NavParams } from 'ionic-angular';
import { ScreenOrientation } from '@ionic-native/screen-orientation';

@Component({
  selector: 'page-chart',
  templateUrl: 'chart.html',
})
export class ChartPage implements OnInit{

  orientationPortrait: boolean = true;

  constructor(public navCtrl: NavController, 
    public navParams: NavParams, 
    private screenOrientation: ScreenOrientation) {
  }

  ngOnInit(){
    this.screenOrientation.onChange().subscribe(
      () => {
          this.orientation = this.screenOrientation.type
          switch(this.screenOrientation.type){
            case this.screenOrientation.ORIENTATIONS.LANDSCAPE :
            case this.screenOrientation.ORIENTATIONS.LANDSCAPE_PRIMARY :
            case this.screenOrientation.ORIENTATIONS.LANDSCAPE_SECONDARY :
              this.orientationPortrait = false;
              break;
            case this.screenOrientation.ORIENTATIONS.PORTRAIT :
            case this.screenOrientation.ORIENTATIONS.PORTRAIT_PRIMARY :
            case this.screenOrientation.ORIENTATIONS.PORTRAIT_SECONDARY :
              this.orientationPortrait = true;
              break;
            default:
              this.orientationPortrait = true;
              break;
          }
      }
   );
  }
}
```

*home.html*

```html
<ion-header>
  <ion-navbar color="dark">
    <ion-title>
      Screen Orientation
    </ion-title>
  </ion-navbar>
</ion-header>
<ion-content padding>
  <div *ngIf="orientationPortrait">
    <h3>PORTRAIT MODE</h3>
  </div>
  <div *ngIf="!orientationPortrait">
    <h3>LANDSCAPE MODE</h3>
  </div>
</ion-content>

```
