[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Animation

## Resources

[link : Josh morony tutorial - animating from the void](https://www.joshmorony.com/animating-from-the-void-enter-and-exit-animations-in-ionic/)     
[link : Josh morony tutorial - add to cart animation](https://www.joshmorony.com/add-to-cart-with-the-web-animations-api-in-ionic/)    
[link : Josh morony tutorial - web animation](https://www.joshmorony.com/using-the-web-animations-api-in-ionic-2/)    
[link : Josh morony tutorial - animating login screen](https://www.joshmorony.com/create-an-animated-login-screen-in-ionic-2/)    

[link : devdactic](https://devdactic.com/animations-ionic-app/)    

## Animation with Angular approach

#### Installation
First, install *web-animation-js* polyfill

```
npm install web-animations-js @angular/animations@4.0.0 --save
```

#### Include polyfill in main.ts

Then, include the polyfill into your *main.ts* file

```javascript
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import 'web-animations-js/web-animations.min';  // web animation polyfill
import { AppModule } from './app.module';

platformBrowserDynamic().bootstrapModule(AppModule);
```

#### Import BrowserAnimationsModule

Import *BrowserAnimationsModule* in *app.module.ts*

```javascript
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';

@NgModule({
  declarations: [
    MyApp,
    HomePage,
    AccordionListPage,
    CpoFlashComponent
  ],
  imports: [
    BrowserModule,
    HttpModule,
    IonicModule.forRoot(MyApp),
    BrowserAnimationsModule // import here
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp,
    HomePage,
    AccordionListPage,
    CpoFlashComponent
  ],
  providers: [
    StatusBar,
    File,
    AppVersion,
    SplashScreen,
    {provide: ErrorHandler, useClass: IonicErrorHandler},
    UtilsProvider,
    UiProvider,
    FlashProvider
  ]
})
export class AppModule {}
```
