[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Capacitor

[Capacitor 3.0](#capacitor-3-0)      
[Capacitor and PWA](#capacitor-and-pwa)        
[Build Android Prod version](#build-android-prod-version)       
[App icon and Splashscreen](#app-icon-and-splashscreen)        
[Local storage](#local-storage)      
[Listening internet connection](#listening-internet-connection)     

## Capacitor 3.0

[Ionic blog](https://ionicframework.com/blog/announcing-capacitor-3-0/)      

- Official Plugins Separated from Capacitor Core, you can use only the features you need
- lazy-load web plugins
- New CLI command : ````npx cap run [options] <platform>```` now run **copy** action before perfoming **run**
- Typescript configuration file : allow to specify different configurations based on the development environment (such as development, QA, and production)
- update persmission API 

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

## App icon and Splashscreen
[Back to top](#capacitor)  

[Generate appicon and splashscreen](https://www.joshmorony.com/adding-icons-splash-screens-launch-images-to-capacitor-projects/)       
[Configure splashscreen](https://capacitor.ionicframework.com/docs/apis/splash-screen/)         
[9 patch splashscreen](https://www.joshmorony.com/creating-a-dynamic-universal-splash-screen-for-capacitor-android/)         

### Create 9-patch Android splashscreen

#### 1 - create splashscreens

First, you have to create your splashscreen for each portrait mode resolution (see res/drawable-port-xxxx directory). You can also delete all the *drawable-land-xxxx* repository, they will be useless.

#### 2 - convert png splashscreen to 9-patch image

Then, in Android Studio, go to *app > res > drawable* and for each resolution, click on your *.png* splashscreen and do *right click > create 9-patch image*. Do **Not** rename file, keep default *splash.9* filename.

#### 3 - define stretch zones

Now, you have a 9-patch image for each resolution. Always from Android Studio, open each of your 9-patch image (one at time) and you are going to define the stretch zones.

- First, select the 9-patch tab and check the "show patches" option. This will display all your image in pink, indicates that all the image is stretchable. 

- Grab the vertical right line and drag it to the left until it touch the left side of your logo. You will see a pink column on the left of your logo.

- Then, click and drag on the grey background from the middle top to the right to create a new stretcheable zone. Adjust it to start from the right of your logo, to the right edge of the image. Now, you must have 2 pink columns on each logo's sides.

- Next, grab the bottom horizontal line and drag it until the top edge of your logo.

- Finally, click and drag to the bottom from the left grey background to create a new stretch zone. Adjust it to touch the bottom of your logo until the bottom of the image.

> IMPORTANT : You may use the zoom feature to get more easily to click and drag from the grey background.

At this point, you must have pink rectangles on the top-left, top-right, bottom-left and bottom-right of your image, green rectangles on the top-center, left-center, right-center and bottom-center of your logo.

> IMPORTANT : You must have black pixel lines only on top and left sides (top-left, top-right, bottom-left, bottom-right corners) and nowhere else ! You must not have black pixels on bottom and right edges

#### 4 - clean repository and build

The final step consists in delete all of your old splashscreens *.png* files from each directories to keep only *splash.9* files. After that *build > clean project* and you can run on your device

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

## Listening Internet connection
[Back to top](#capacitor)  

As an example, when a user is on a slower connection, they get an alert that performance may be degraded:

````
if (navigator.connection.effectiveType != '4g') {
  console.log('slow connection!');
  // show modal dialog warning user that video will be loaded at lower resolution
}
else {
  console.log('ready to go!');
  // immediate load video file
}
````


Another option is Capacitor’s Network API. It extends the Network Information API to provide even more useful features for web and mobile apps, such as monitoring the network for status changes, which your app can then react to.

````
import { Plugins } from '@capacitor/core';

const { Network } = Plugins;

let handler = Network.addListener('networkStatusChange', (status) => {
  console.log("Network status changed", status);
});

// Get the current network status
let status = await Network.getStatus();

// Example output:
{
  "connected": true,
  "connectionType": "wifi"
}
````
