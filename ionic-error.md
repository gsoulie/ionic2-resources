[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Common errors

* [Typescript error](#typescript-error)     
* [iOS xcassets error - Distill failed for unknown reasons](#xcassets-error)    
* [Android build error In <declare-styleable>](#declare-stylable-error)     

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
