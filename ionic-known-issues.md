[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Known issues

* [Clicking item does not work](#)     
* [Slow tap on component](#slow-tap-on-component-and-element)     
* [Conflict with "this" on callback](#conflict-with-"this"-on-callback)    
* [Scrolling when entering input field](#scrolling-when-entering-input-field)    
* [Display carriage return](#display-carriage-return)    
* [Remove iOS cache](#remove-ios-cache)    
* [Gradle build failure](#gradle-build-failure-app:debugCompileClasspath)    
* [iOS 12 keyboard issue](#ios-12-keyboard-issue)    
* [iOS 12 WebView issue](#ios-12-webview-issue)    
* [Typescript error](#typescript-error)     
* [iOS xcassets error - Distill failed for unknown reasons](#xcassets-error)    
* [Android build error In <declare-styleable>](#declare-stylable-error)     
* [DexArchiveMergerException](#dexarchivemergerexception)    
* [CORS issues](#cors-issues)    
* [HTTP query issues](#http-query-issues)    
* [Android build error](#android-build-error)    
* [Android failed to execute aapt](#android-failed-to-execute-aapt)    
* [ViewDestroyError](#viewdestroyerror)    


### Clicking in list item in simulator sometimes(!) doesn’t work on device
[Back to top](#known-issues)    

Sometimes,  on the emulator or on device, 2 out of 3 clicks it doesn't fire the click function. To solve the issue, change ```<ion-item>``` to ```<button>``` with the styling of ```ion-item```.

```xml
<ion-list>
	<ion-item *ngFor="let audience of audience" (click)="pushProfilesList(audience)">
		<h2 class="list-title">{{audience.segment}}</h2>
		<span class="list-info">{{audience.amount}}</span>
	</ion-item>
</ion-list>
```

### Slow tap on component and element
[Back to top](#known-issues)    

If you create custom components or html element (div or ion-card) and then use click handler like ```(click)="itemTap()"``` you may notice a delay if the element are not an anchor tag or button.
To fix it, add **tappable** as an attribute to you element or component like ```(click)="itemTap($event)" tappable``` or add it on your view file like ```<ion-card tappable="true" (click)="onOpen()">```

see also : button role on non button clickable elements ```role="button"```

### Conflict with "this" on callback
[Back to top](#known-issues)    

refering to **this** has traditionally been a pain point in JavaScript. Fat arrows fix it by capturing the meaning of this from the surrounding context. So it is very useful in case of calling asynchronous callback after a window closing event.

**use case :** We want to refresh a list of items after adding a new item on it (via a "new item" modal)

*List.ts*

```javascript
...
// Refresh item list
refreshRouteList(){
    this.Db.getRoutesList().then((data)=> this.routesList = data);
}

//Open new item modal
newItem(){
    // Open DetailPage with callback function wich refresh the list after adding a new item
    /* below : traditional callback syntax --> does not work because 'this' conflict
    this.navCtrl.push(DetailPage,{callback: function(){
            this.refreshRouteList();
        }
    });*/
    
    // New syntax with Arrow function
    this.navCtrl.push(DetailPage,{callback:
        () => {this.refreshRouteList();}
    });
 }
...
```

An other solution consist in declare a variable wich contain **this** :

*List.js*

```javascript
var myThis = this;

newItem(){
    // Open DetailPage with callback function wich refresh the list after adding a new item
      
    this.navCtrl.push(DetailPage,{callback:function(){
        myThis.refreshRouteList();
    });
 }
...
```


*DetailPage.js*

```javascript
...
save(){
    this.callback(); // refreshRouteList() callback
}
...
```

### Scrolling when entering input field
[Back to top](#known-issues)    

To remove scrolling when entering input field focus, add the code below in you *app.component.ts*

```javascript
 imports: [
    BrowserModule,
    IonicModule.forRoot(MyApp,{
      scrollAssist: false, 
      autoFocusAssist: false
    })    
  ],
  ```
  
  ### Display carriage return
  [Back to top](#known-issues)    
  
  To insert ```\r\n``` tags, use that syntax :
  
  ```
  <p  class="pClass">{{someTextWithCarriageReturn}}</p>
  ```
  
  ```
  .pClass{
        white-space: pre-line;
        overflow: auto;
  }
  ```
  
  ### Remove iOS cache
  [Back to top](#known-issues)    
  
  **iOS**
  
  ```javascript
  import { File } from '@ionic-native/file';
  
  if(platform.is('ios')){
      this.file.removeRecursively(this.file.cacheDirectory, "Webkit").catch(err => console.log(err));
  }
  ```
  
  **Android**
  
  For Android, use *disable-http-cache-cache* plugin
  
### Gradle build failure app:debugCompileClasspath
  
```
FAILURE: Build failed with an exception.
 
* What went wrong:
Could not resolve all files for configuration ':app:debugCompileClasspath'.
> Could not find support-v4.aar (com.android.support:support-v4:27.1.1).
  Searched in the following locations:
      https://jcenter.bintray.com/com/android/support/support-v4/27.1.1/support-v4-27.1.1.aar
```

To fix it, modify your */platforms/android/build.gradle* to reflects the following :

```javascript
 allprojects {
    repositories {
    	// JUST REPLACE repositories content by the following
	mavenLocal()
	maven { url 'https://maven.google.com' }
	jcenter()
	maven {
	// All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
	url "$rootDir/../node_modules/react-native/android"
	}
	maven {
		url 'https://maven.google.com/'
		name 'Google'
	} 
    }
    project.ext{
	defaultBuildToolsVersion="25.0.2"
	defaultMinSdkVersion=19
	defaultTargetSdkVersion=26
	defaultCompileSdkVersion=26
    }

}
```

## iOS 12 plugin issue
[Back to top](#known-issues) 

According 

Since iOS 12, you may get the following error :```API error returned 0 width, assuming UIViewNoIntrinsicMetric```

This affect the keyboard usage, this is due to the ```ionic-plugin-keyboard``` which is deprecated. So you must remove this plugin and install ```cordova-plugin-ionic-keyboard```

https://forum.ionicframework.com/t/api-error-returned-0-width-assuming-uiviewnointrinsicmetric/110655/26
https://forum.ionicframework.com/t/important-all-ionic-users-please-update-your-keyboard-plugin/46889
  
## iOS 12 Webview issue
[Back to top](#known-issues) 

Since iOS 12 there is a bug on WKWebView, Touch-Areas are offset after keyboard is hidden.

[related post 1](https://github.com/ionic-team/capacitor/issues/814)
[related post 2](https://github.com/ionic-team/cordova-plugin-ionic-webview/pull/201)
[related post 3](https://github.com/apache/cordova-ios/issues/417)
[related post 4](https://github.com/ionic-team/cordova-plugin-ionic-webview/issues/176)

One ionic 3 app, one possible solution is to add the code below in the constructor of the *app.component.ts*

```
let html = document.getElementsByTagName("html")[0];
window.addEventListener('native.keyboardshow', (e) => {
	console.log("show2");
	html.style.height = "auto";
	//this.renderer2.setStyle(html, 'height', '101vh');
}); 
window.addEventListener('native.keyboardhide', () => {
	console.log("hide2");
	html.style.height = "101vh";
	//this.renderer2.setStyle(html, 'height', 'auto');
});
```


## Typescript error

Sometimes, you can get typescript error when you try to build / run with *--prod* flag

```
[13:48:44]  typescript error
            'ion-icon' is not a known element: 1. If 'ion-icon' is an Angular component, then verify that it is part of
            this module. 2. If 'ion-icon' is a Web Component then add 'CUSTOM_ELEMENTS_SCHEMA' to the
            '@NgModule.schemas' of this component to suppress this message. (" <ion-navbar color="secondary"> <button
            ion-button (click)="dismiss()"> [ERROR ->]<ion-icon name="md-arrow-back"></ion-icon> </button>
            <ion-title>Device List</ion-title> ")

[13:48:44]  typescript error
            'ion-title' is not a known element: 1. If 'ion-title' is an Angular component, then verify that it is part
            of this module. 2. If 'ion-title' is a Web Component then add 'CUSTOM_ELEMENTS_SCHEMA' to the
            '@NgModule.schemas' of this component to suppress this message. (" <ion-icon
            name="md-arrow-back"></ion-icon> </button> [ERROR ->]<ion-title>Device List</ion-title> </ion-navbar>
            </ion-header> ")

[13:48:44]  typescript error
            'ion-navbar' is not a known element: 1. If 'ion-navbar' is an Angular component, then verify that it is part
            of this module. 2. If 'ion-navbar' is a Web Component then add 'CUSTOM_ELEMENTS_SCHEMA' to the
            '@NgModule.schemas' of this component to suppress this message. ("<ion-header> [ERROR ->]<ion-navbar
            color="secondary"> <button ion-button (click)="dismiss()"> <ion-icon name="md-a")
```

**This is due to a bad module import. **

For example, if you create a page with ```ionic -g page <my-page>``` a new *my-page.component.ts* file is created.
If you forget to delete it, you will get the error above when you will try to build / run with prod mode.

## xcassets error

[back to top](#common-errors)    

Sometimes iOS build failed with ```Asset catalog - xcassets error``` and XCode throw a ```Distill failed for unknown reasons``` error.

This error seems to be caused by ios assets files

To solve this error 

```
$ sudo xcode-select -switch /Applications/Xcode.app/Contents/Developer
$ ionic cordova platfom rm ios
// Then remove all ios assets in the resources folder
$ ionic cordova platform add ios@latest
$ ionic cordova resources --splash
$ ionic cordova resources --icon
```

Finally, remove all references of icon and splash from the config.xml

```
<platform name="ios">
       <allow-intent href="itms:*" />
       <allow-intent href="itms-apps:*" />
       <icon height="57" src="resources/ios/icon/icon.png" width="57" />
       <icon height="114" src="resources/ios/icon/icon@2x.png" width="114" />
       <icon height="40" src="resources/ios/icon/icon-40.png" width="40" />
       <icon height="80" src="resources/ios/icon/icon-40@2x.png" width="80" />
       <icon height="120" src="resources/ios/icon/icon-40@3x.png" width="120" />
       <icon height="50" src="resources/ios/icon/icon-50.png" width="50" />
       <icon height="100" src="resources/ios/icon/icon-50@2x.png" width="100" />
       <icon height="60" src="resources/ios/icon/icon-60.png" width="60" />
       <icon height="120" src="resources/ios/icon/icon-60@2x.png" width="120" />
       <icon height="180" src="resources/ios/icon/icon-60@3x.png" width="180" />
       <icon height="72" src="resources/ios/icon/icon-72.png" width="72" />
       <icon height="144" src="resources/ios/icon/icon-72@2x.png" width="144" />
       <icon height="76" src="resources/ios/icon/icon-76.png" width="76" />
       <icon height="152" src="resources/ios/icon/icon-76@2x.png" width="152" />
       <icon height="167" src="resources/ios/icon/icon-83.5@2x.png" width="167" />
       <icon height="29" src="resources/ios/icon/icon-small.png" width="29" />
       <icon height="58" src="resources/ios/icon/icon-small@2x.png" width="58" />
       <icon height="87" src="resources/ios/icon/icon-small@3x.png" width="87" />
       <icon height="1024" src="resources/ios/icon/icon-1024.png" width="1024" />
       <splash height="1136" src="resources/ios/splash/Default-568h@2x~iphone.png" width="640" />
       <splash height="1334" src="resources/ios/splash/Default-667h.png" width="750" />
       <splash height="2208" src="resources/ios/splash/Default-736h.png" width="1242" />
       <splash height="1536" src="resources/ios/splash/Default-Landscape@2x~ipad.png" width="2048" />
       <splash height="2048" src="resources/ios/splash/Default-Landscape@~ipadpro.png" width="2732" />
       <splash height="768" src="resources/ios/splash/Default-Landscape~ipad.png" width="1024" />
       <splash height="2048" src="resources/ios/splash/Default-Portrait@2x~ipad.png" width="1536" />
       <splash height="2732" src="resources/ios/splash/Default-Portrait@~ipadpro.png" width="2048" />
       <splash height="1024" src="resources/ios/splash/Default-Portrait~ipad.png" width="768" />
       <splash height="960" src="resources/ios/splash/Default@2x~iphone.png" width="640" />
       <splash height="480" src="resources/ios/splash/Default~iphone.png" width="320" />
       <splash height="2732" src="resources/ios/splash/Default@2x~universal~anyany.png" width="2732" />
       <splash height="1242" src="resources/ios/splash/Default-Landscape-736h.png" width="2208" />
   </platform>
```

To

```
<platform name="ios">
       <allow-intent href="itms:*" />
       <allow-intent href="itms-apps:*" />
</platform>
```

After that, iOS build should be ok

## declare-stylable error
[back to top](#common-errors) 

Sometimes, Android build failed with 

```
In <declare-styleable> FontFamilyFont, unable to find attribute android:fontVariationSettings
In <declare-styleable> FontFamilyFont, unable to find attribute android:font
In <declare-styleable> FontFamilyFont, unable to find attribute android:fontVariationSettings
ERROR: In FontFamilyFont, unable to find attribute android:font
ERROR: In FontFamilyFont, unable to find attribute android:fontStyle
ERROR: In FontFamilyFont, unable to find attribute android:fontWeight
```

**the solution**

```
cordova plugin rm cordova-plugin-compat --force
cordova platform rm android
ionic cordova platform add android@6.3.0
```

## DexArchiveMergerException
[back to top](#common-errors) 

#### Error
```
* What went wrong:
Execution failed for task ':app:transformDexArchiveWithExternalLibsDexMergerForDebug'.
> java.lang.RuntimeException: java.lang.RuntimeException: com.android.builder.dexing.DexArchiveMergerException: Unable to merge dex
```

#### Fix

Remove and re-add Android platform

Sometimes need to update build.gradle with :

```
allprojects {
    repositories {
        jcenter()
        maven {
            url "https://maven.google.com"
        }
    }
    //This replaces project.properties w.r.t. build settings
    project.ext {
      defaultBuildToolsVersion="28.0.1"//"25.0.2" //String
      defaultMinSdkVersion=19 //Integer - Minimum requirement is Android 4.4
      defaultTargetSdkVersion=26 //Integer - We ALWAYS target the latest by default
      defaultCompileSdkVersion=26 //Integer - We ALWAYS compile with the latest by default
    }
}
```

## CORS issues
[back to top](#common-errors) 

https://www.joshmorony.com/dealing-with-cors-cross-origin-resource-sharing-in-ionic-applications/

## HTTP query issues
[back to top](#common-errors) 

Sometimes, Http GET queries with Angular can failed on iOS. To void that, it is recommanded to use ```httpIonic.get()``` instead of ```HttpAngular.get()```

For Android, ```HttpAngular.get()``` still works fine when ```httpIonic.get()``` doesn't work.

## Android Build error
[back to top](#common-errors) 

*Build Issue :app:debugCompileClasspath*
```
Could not resolve all files for configuration ':app:debugCompileClasspath'.
> Could not find play-services-basement.aar (com.google.android.gms:play-services-basement:15.0.1).
Searched in the following locations:
            https://jcenter.bintray.com/com/google/android/gms/play-services-basement/15.0.1/play-services-basement-15.0.1.aar
```

To fix it, modify your */platforms/android/build.gradle* to reflecting the following

```
allprojects {
    repositories {
            mavenLocal()
            maven { url 'https://maven.google.com' }
            jcenter()
            maven {
                // All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
                url "$rootDir/../node_modules/react-native/android"
            }
            maven {
                url 'https://maven.google.com/'
                name 'Google'
            }
    }
}
```

*Build Issue :transformClassesWithDexForDebug*
```
Error build Android: Execution failed for task':transformClassesWithDexForDebug'. 
```

To fix it :

```
cordova clean 
```

## Android failed to execute aapt
[back to top](#common-errors) 

#### Issue

```
FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':processDebugResources'.
> com.android.ide.common.process.ProcessException: Failed to execute aapt
```

#### Solution

Add the following code in the config.xml file

```
<platform name="android">
 <edit-config file="AndroidManifest.xml" mode="merge" target="/manifest/application" xmlns:android="http://schemas.android.com/apk/res/android">
    <application android:networkSecurityConfig="@xml/network_security_config" />
 </edit-config>
 <resource-file src="resources/android/xml/network_security_config.xml" target="res/xml/network_security_config.xml" />
</platform>
 ```

## ViewDestroyError
[back to top](#common-errors) 

If you are using *window.onresize* event in your page (*like below*)

````
ngOnInit() {
    this.initializeCharts();
    window.onresize = (e) => {
      this.ngZone.run(() => {
          this.generateGeoChart(window.innerHeight);
      });
   };
}
````

you will probably get the following error when you will try to switch view :

**ERROR Error: ViewDestroyedError: Attempt to use a destroyed view: detectChanges**

The reason of that error is that you are deleting the reference of your object on the change event. But the detectChange method is waiting for 250 ms before firing the change detect, at that time your view and component will be destroyed.

To prevent that issue, you need to implement the following code :

````
 ngOnDestroy() {
    window.onresize = null;
  }
````
