[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Splash screen and appicon

[link : Appicon and Splash screen documentation](http://blog.ionic.io/automating-icons-and-splash-screens/)

[link : Splash screen PSD](http://code.ionicframework.com/resources/splash.psd)

Save a splash.png, splash.psd or splash.ai file within the resources directory at the root of the Cordova project. 

**Splash screen** dimensions vary for each platform, device and orientation, so a square source image is required the generate each of various sizes. The source image’s minimum dimensions should be **2208x2208** px, and its artwork should be centered within the square, knowing that each generated image will be center cropped into landscape and portrait images. 
The splash screen’s artwork should roughly fit within a center square (1200x1200 px). This Photoshop splash screen template provides the recommended size and guidelines of the artwork’s safe zone. Additionally, when the Orientation preference config is set to either landscape or portrait mode, then only the necessary images will be generated.

For the **appicon**, the minimum dimension should be 192x192 px with no rounded corner. **1024x1024**px file is better 

```
$ ionic cordova resources --splash
```

You can also run 

```
$ ionic cordova resources --icon
```

Or simply

```
$ ionic cordova resources
```

> Note : If splash screen doesn't showing, update your cordova plugin

```
ionic cordova plugin rm cordova-plugin-splashscreen
ionic cordova plugin add https://github.com/apache/cordova-plugin-splashscreen.git
```

Or create all the images with those dimensions :

```
src="resources/ios/splash/Default-568h@2x~iphone.png" width="640" height="1136"
src="resources/ios/splash/Default-667h.png" width="750" height="1334"
src="resources/ios/splash/Default-736h.png" width="1242" height="2208"
src="resources/ios/splash/Default-Landscape-736h.png" width="2208" height="1242"
src="resources/ios/splash/Default-Landscape@2x~ipad.png" width="2048" height="1536"
src="resources/ios/splash/Default-Landscape~ipad.png" width="1024" height="768" 
src="resources/ios/splash/Default-Portrait@2x~ipad.png" width="1536" height="2048"
src="resources/ios/splash/Default-Portrait~ipad.png" width="768" height="1024"
src="resources/ios/splash/Default@2x~iphone.png" width="640" height="960" 
src="resources/ios/splash/Default~iphone.png" width="320" height="480" 
```

**Remove fade-in / fade-out effect**

Add the following property to config.xml
```xml
<preference name="FadeSplashScreen" value="false"/>
```

**Remove spinner**
Add the following property to config.xml
```xml
<preference name="ShowSplashScreenSpinner" value="false"/>
```
