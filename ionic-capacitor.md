[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Capacitor

## What is capacitor
Capacitor is the new Ionic's native web app container that runs your web app natively in iOS, Android, Electron, PWA...
Capacitor is the successor of Cordova, it containerizes your web app and puts it into a managed native web view, then it exposes nativefunctionality to your web app in cross-platform way.

[Documentation](https://capacitor.ionicframework.com/docs/getting-started/with-ionic/)    

### Requirements

* XCode 10 minimum
* Java 8 SDK (Java 9 does not work at the moment)

Capacitor uses the WKWebView

### Usage
[Back to top](#capacitor)     

**Create new ionic capacitor project**

```ionic start myApp tabs --capacitor```

**Add capacitor in existing project**

```ionic integrations enable capacitor```

**initialize capacitor with app information** 
```npx cap init [appName] [appId]```

**You must build your ionic app at least once before adding any native platforms**
```ionic build```

**add platforms**

```npx cap add ios / npx cap add android```

**copy code**
Once web code is built (or if you make changes in your web-based code), it needs to be copied to each native project

```
npx cap copy
npx cap copy ios // to specify the native platform
```

If some plugins were modified, you need to run 

```
npx cap update
```

You can also simply run ```npx sync``` to both copy your web code and update your plugins

**open native IDE to build, run, and deploy**

```
npx cap open ios // open Xcode to run on ios
npx cap open android // open android studio to run on android
npx cap serve // to run as PWA
```

**capacitor maintenance**
Capacitor needs periodic maintenance such as updating dependencies and installing new plugins

```npx cap update```

You can install new plugins 
```
npm install cool-plugin
npx cap update
```

## Capacitor and PWA
[Back to top](#capacitor)     

To add PWA support to your app, you'll need an App manifest *manifest.json* that sits alongside your *index.html*. Next you just need to enable service worker.

### Check if plugin is available

```
import { Capacitor } from '@capacitor/core';

const isAvailable = Capacitor.isPluginAvailable('Camera');

if(!isAvailable) {

} else {
  Camera.getPhoto();
}
```
## Build Android Prod version
[Back to top](#capacitor)     

From Android studio : Build > Generate Signed APK

Then, Make sure to choose the **release** build type, and you should also tick both of the signature options **V1** and **V2**

## App icon and Splash screen

[Generate appicon and splashscreen](https://www.joshmorony.com/adding-icons-splash-screens-launch-images-to-capacitor-projects/)       
[Configure splashscreen](https://capacitor.ionicframework.com/docs/apis/splash-screen/)         
[9 patch splashscreen](https://www.joshmorony.com/creating-a-dynamic-universal-splash-screen-for-capacitor-android/)         

## Local storage
[Back to top](#capacitor)     
```
import { Plugins } from '@capacitor/core';

const { Storage } = Plugins;
initializeApp() {
  Storage.get({key: 'first_launch'}).then((val) => {
        if (val.value !== null) {
          // not first launch
          this.router.navigateByUrl('/home');
        } else {
          // first launch
          Storage.set({key: 'first_launch',
          value: 'done'});
          this.router.navigateByUrl('/tutorial');
        }
     });
}
```
