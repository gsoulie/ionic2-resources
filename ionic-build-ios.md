# Build for iOS

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
