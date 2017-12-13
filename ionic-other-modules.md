# Other modules

## CRC Computation

Here is a library which permit CRC computation. This lib return a JSON object which contains all type of CRC (see usage below).

[lib : CRC.ts](https://github.com/gsoulie/Mobile-App-Development/blob/master/CRC.ts)

**Usage**

*app.module.ts*

```javascript
import { crc } from './../models/crc';
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
    {provide: ErrorHandler, useClass: IonicErrorHandler},
    crc
  ]
})
export class AppModule {}

```
*Controller file*

```javascript
import { crc } from './../../models/crc';

@Component({
  selector: 'page-mode-expert',
  templateUrl: 'mode-expert.html',
})
export class ModeExpertPage {

 constructor(public navCtrl: NavController, 
              public navParams: NavParams, 
              private utils: crc) {
        
        this.utils.CRCMaster.init();  //init CRC lib
	let result = this.utils.CRCMaster.Calculate("01040706","hex");
	console.log("All results : " + JSON.stringify(result));
        console.log("CRCXmodem : " + result.crcxmodem);
  }
  
}
```

## Make PDF
[make PDF](https://gist.github.com/scalp42/7261508)  
[tutorial](http://gonehybrid.com/how-to-create-and-display-a-pdf-file-in-your-ionic-app/)

