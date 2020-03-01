[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Publishing app

[link : publishing app](http://ionicframework.com/docs/guide/publishing.html)    

First of all, check your config.xml file and modify **id, author, name, description etc...**. Don't forget to add ```android-versionCode="xxx"``` in *config.xml* file like below :

```
<widget android-versionCode="1" version="1.0.1" id="com.mydomain.myapp"...>
```

more information about it's configuration [here](http://cordova.apache.org/docs/en/latest/config_ref/index.html)
[V1 documentation](https://ionicframework.com/docs/v1/guide/publishing.html)  

Now that we have a working app, we are ready to push it live to the world! Since the Ionic team already submitted the Todo app from this guide to the app store, chances are you’ll want to follow this chapter with a new app that you make on your own.

So first, we need to generate a release build of our app, targeted at each platform we wish to deploy on. Before we deploy, we should take care to adjust plugins needed during development that should not be in production mode.

## Android publishing

  

To generate a release build for Android :

```
$ cordova build --release --prod android
```

This will generate a release build based on the settings in your config.xml. Your Ionic app will have preset default values in this file, but if you need to customize how your app is built, you can edit this file to fit your preferences

[Other build option](https://ionicframework.com/docs/cli/cordova/build/)    

Next, we can find our unsigned **APK** file in ```platforms/android/build/outputs/apk```

Now, we need to sign the unsigned APK and run an alignment utility on it to optimize it and prepare it for the app store. If you already have a signing key, skip these steps and use that one instead.

Let’s generate our private key using the **keytool** command that comes with the JDK.

```
$ keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000
```

You will first be prompted to create a password for the keystore. Then, answer the rest of the nice tools’s questions and when it’s all done, you should have a file called **my-release-key.keystore** created in the current directory.

**Note:** Make sure to save this file somewhere safe, if you lose it you won’t be able to submit updates to your app!

To sign the unsigned APK, run the **jarsigner** tool which is also included in the JDK:

**WARNING** you must copy your apk in the same folder that your keystore were generated ! Else you will have the following error 
```jarsigner: unable to open jar file: android-release-unsigned.apk```

```
$ jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore my-release-key.keystore HelloWorld-release-unsigned.apk alias_name
```

This signs the apk in place. Finally, we need to run the zip align tool to optimize the APK. The **zipalign** tool can be found in **/path/to/Android/sdk/build-tools/VERSION/zipalign**. For example, on OS X with Android Studio installed, **zipalign is in ~/Library/Android/sdk/build-tools/VERSION/zipalign:**

```
$ zipalign -v 4 HelloWorld-release-unsigned.apk HelloWorld.apk
```

Now we have our final release binary called HelloWorld.apk and we can release this on the Google Play Store


# Full example

**Compile in distribution mode**
 
```ionic cordova build --release --prod android```
 
This will generate an unsigned APK which will be located in *platforms/android/app/build/outputs/apk*

**Generate keystore (to do only the first time)**
 
```keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000```

> my-release-key is the name you want to give to your keystore

Then answer to asked questions :
```
What is your first and last name? :  xxxxxx
What is the name of your organizational unit? :  xxxxxxxx
What is the name of your organization? :  xxxxxxxxx
What is the name of your City or Locality? :  xxxxxxx
What is the name of your State or Province? :  xxxxx
What is the two-letter country code for this unit? :  xxx
``` 

**APK Signin**

Now you need to sign your unsigned apk with your fresh keystore. To achieve this, we use **jarsigner** which is available with JDK tools.

**Be careful**, you must copy your unsigned apk in the same directory where the keystore was generated. Else you will encounter an error like *unable to open jar file: android-release-unsigned.apk)*
 
Use the command bellow to sign your apk :
 
```
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore <you_keystore_name>.keystore app-release-unsigned.apk you_app_alias
Passphrase : <your_passphrase>
```
 
**Optimize apk**

The final step is to use *zipalign* tool in order to optimize apk size. You will normally find *zipalign* tool in the Android SDK directory */path/to/Android/sdk/build-tools/VERSION/zipalign* for example on mac :

```
/Users/Username/Library/Android/sdk/build-tools/28.0.1/zipalign)
```
 
Use the command : 
```
zipalign -v 4 HelloWorld-release-unsigned.apk HelloWorld.apk
```

Example with full path :
```
/Users/formation/Library/Android/sdk/build-tools/28.0.3/
zipalign -v 4 /Users/Username/Documents/ionic-workspace/MyProject/MyApp/deploiement/app-release-unsigned.apk myapp.apk
```
 
Or :
```
$ cd /Users/Username/Library/Android/sdk/build-tools/28.0.3/
$ ./zipalign -v 4 /Users/Username/Documents/ionic-workspace/MyProject/myApp/deploiement/app-release-unsigned.apk /Users/Username/Documents/ionic-workspace/MyProject/myApp/deploiement/myApp.apk
```

