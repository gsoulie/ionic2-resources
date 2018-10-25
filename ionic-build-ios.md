[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Build for iOS

* [Adding iOS permissions to plist automatically](#adding-ios-permissions-to-plist-automatically)    


[Build without using XCode](https://github.com/phonegap/ios-deploy)      
```
xcode-select --install
sudo npm install -g ios-deploy --unsafe-perm=true
```

*in your app folder*
```
ionic cordova platform add ios
ionic cordova build ios
```
**You may get a build error at the first time**

So, for the first time, you need to open your project in XCode. To do this, navigate into your app folder and open your *.xcodeproj* file

Then, in xcode, go to Xcode -> preferences and create or connect to your apple ID. You need to create a provisioning profile or select one in the Xcode project settings tab

*Run on ios device or simulator*
```
ionic cordova run ios
```

use *ionic cordova build ios --prod* to build in prod mode

## Adding iOS permissions to plist automatically
[back to top](#build-for-ios)

If you are using the camera or photo library on iOS you have to ask your users for permissions, otherwise your app will crash at that point or at least your app will be rejected once you submit it to iTunes Connect.

Adding those entries manually to the *plist* can't be the solution, and of course it isn't because you can add these lines to your config.xml:

*Patch your iOS Plist permissions*
```
<edit-config file="*-Info.plist" mode="merge" target="NSCameraUsageDescription">
    <string>need camera access to take pictures</string>
</edit-config>
<edit-config file="*-Info.plist" mode="merge" target="NSPhotoLibraryUsageDescription">
    <string>need photo library access to get pictures from there</string>
</edit-config>
```

This will automatically create the needed entries whenever you build your Ionic app for iOS, just make sure to add reasonable information in here. 
