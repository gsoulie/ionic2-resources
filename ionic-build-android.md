[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Build for Android

[link : optimizing performance](https://www.protectator.ch/post/optimizing-performance-of-an-ionic-3-application)    

## The --prod flag

Tip for get a faster boot time is to add ```--prod``` in your ```ionic cordova run android --prod```. This will uses an other build process and decrease the app boot time.

## enableProdMode()

Angular’s documentation on enableProdMode() tells us calling it “turns off assertions and other checks within the framework.” which is of course something you’d want on a production build, because these are found in a lot of places and can take quite some time. Enabling it for production is 100% recommended. To enable it, call it in your main file :

*main.ts*

```javascript
import {platformBrowserDynamic} from '@angular/platform-browser-dynamic';
import {AppModule} from './app.module';
import {enableProdMode} from "@angular/core";

enableProdMode();
platformBrowserDynamic().bootstrapModule(AppModule);
```
