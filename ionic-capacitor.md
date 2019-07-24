# Capacitor

## What is capacitor
Capacitor is the new Ionic's native web app container that runs your web app natively in iOS, Android, Electron, PWA...
Capacitor is the successor of Cordova, it containerizes your web app and puts it into a managed native web view, then it exposes nativefunctionality to your web app in cross-platform way.

### Requirements

* XCode 10 minimum
* Java 8 SDK (Java 9 does not work at the moment)

Capacitor uses the WKWebView

### Usage

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
Once web code is built, it needs to be copied to each native project

```
npx cap copy
npx cap copy ios // to specify the native platform
```

**open native IDE to build, run, and deploy**

```
npx cap open ios // open Xcode to run on ios
npx cap open android // open android studio to run on android
npx cap serve // to run as PWA
```

**capacitor maintenance**
Capacitor needs periodic maintenance such as updating dependencies and installing new plugins

```npx cap update```

You can isntall new plugins 
```
npm install cool-plugin
npx cap update
```

## Capacitor and PWA

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
