[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Prerequisites

* [Update environment npm / nodejs](#update-environment)      
* [Chrome inspect device](#chrome-inspect-device)  
* [Using gulp](#using-gulp)   
* [Android debugging with logcat](#android-debugging-with-logcat)    
* [Using multiple version of Node with nvm](#using-multiple-version-of-node-with-nvm)     
* [Switching to ESLint](#switching-to-eslint)        
* [Google and Apple account creation](#google-and-apple-account-creation)       

## Update environment

### npm

````
sudo npm install npm@latest -g
//or
sudo npm install npm@6.8.9 -g // for specific version
````

### nodejs

````
sudo npm cache clean -f
sudo npm install -g n
sudo n stable
````

### ionic

````
npm install @ionic/angular@6
sudo npm i -g @ionic/cli
````

### angular

````
npm install -g @angular/cli
````

### migrating from Angular 8.3 to Angular 10 + Ionic 5

````
ng update @angular/cli@8 @angular/core@8 --allow-dirty
npm install @ionic/angular@latest --save
ng update @angular/core @angular/cli --allow-dirty --force
````

## (OLD ?) Configure Android environment variable

```
nano ~/.bash_profile
```

```
export ANDROID_HOME=/Users/gsoulie/Library/android-sdk-macosx
export PATH=${PATH}:/Users/gsoulie/Library/android-sdk-macosx/tools:/Users/gsoulie/Library/android-sdk-macosx/platform-tools
```

**refresh bash_profile**

```
$ source .bash_profile
```

Then, restart your computer and test ANDROID_HOME ```$ANDROID_HOME``` don't forget **$** on the command

## Chrome inspect device

After app is running on device, you can inspect it with chrome inspect device

https://ionicframework.com/blog/debugging-tips-for-your-ionic-app/?utm_campaign=content_community&utm_medium=email&_hsmi=208481150&_hsenc=p2ANqtz-991Rh_XKw-9tlFMr1NVHDRcNhNBZfYYmiX8ItOwgqOHKIJzt0y8wWUT8Q__AV0hCTKwufM2dR8BRrzgqXOaLHPH1Tptg&utm_content=69738787390&utm_source=hubspot

[chrome://inspect/#devices](chrome://inspect/#devices)    

![alt tag](https://s-media-cache-ak0.pinimg.com/originals/8e/52/17/8e5217a74089f046435655d0e0477517.png)


## Using Gulp

Gulp is a packet manager for JS libraries. You can use it to add libraries to your Ionic app or to update the Ionic JavaScript library itself.

```
$ sudo npm install -g bower
```
## Android debugging with logcat
[Back to top](#prerequisites) 

You can visualize android logcat console by using **adb**. Open a terminal from */<your_android_sdk_path>/platform-tools/* and run :

````
$<path-to-android-sdk>/platform-tools adb logcat
````

## Using multiple version of node with nvm
[Back to top](#prerequisites) 

https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/

## Switching to ESLint
[Back to top](#prerequisites) 

Since 2019 TSLint is deprecated, so it's time to switch for ESLint

[Ionic blog](https://ionicframework.com/blog/eslint-for-ionic-angular/)       

````
git add .
git commit -m "pre eslint"
git checkout -b feat-eslint

ng add @angular-eslint/schematics

// convert current tslint to eslint file
ng g @angular-eslint/schematics:convert-tslint-to-eslint {{YOUR_PROJECT_NAME_GOES_HERE}}

// once it's done you can run eslint and remove old tslint file and dependcies
rm tslint.json
npm uninstall tslint
````

## Google and Apple account creation

création compte google : https://fr.goodbarber.com/blog/comment-ouvrir-un-compte-developer-google-play/

création compte apple : https://fr.goodbarber.com/blog/comment-publier-votre-application-sur-l-app-store-les-etapes-a914/

différence entre compte apple developer et compte apple entreprise : https://accella.net/knowledgebase/difference-between-apple-developer-and-enterprise-accounts/
