[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# PWA

## Resources

[PWA stencil](https://stenciljs.com/pwa/)    
[ionic blog post](http://blog.ionic.io/how-to-make-pwas-with-ionic/)     
[link : josh morony tutorial PWA](https://www.joshmorony.com/the-bare-necessities-progressive-web-apps-in-ionic/)    
[link : josh morony tutorial](https://www.joshmorony.com/preparing-a-progressive-web-application-for-production/)   
[link : freecodecamp PWA](https://medium.freecodecamp.org/progressive-web-apps-bridging-the-gap-between-web-and-mobile-apps-a08c76e3e768)    

[discussion - firebase hosting](https://forum.ionicframework.com/t/building-for-browsers-pwa/72689)    
[discussion - building-for-browsers-pwa](https://forum.ionicframework.com/t/building-for-browsers-pwa/72689/2)    
[discussion - pwa-my-experience](https://forum.ionicframework.com/t/pwa-my-experience-creating-a-pwa-with-ionic-from-scratch-to-deployment/94541)    
[discussion - deploying-pwa](https://forum.ionicframework.com/t/deploying-pwa/73749)   
[firebase hosting](https://coryrylan.com/blog/deploy-angular-cli-apps-to-firebase)   

## Browser support

what functionalities are already supported by browser ?
[to open on wanted web browser](https://whatwebcando.today/)    

## Step 1

Add browser platform with ```ionic cordova platform add browser```    

## Step 2

Uncomment the following code in the index.html to activate service worker to enabling caching resources

```
<script>
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('service-worker.js')
      .then(() => console.log('service worker installed'))
      .catch(err => console.error('Error', err));
  }
</script>
```

And change the title of the app

```
  <title>My app</title>
```

Don't forget to change the favicon in *src/assets/ico*, notice that the favicon does not well support transparency

Finally, if you use Google map, be sure that the google map api url is in *https*

```
<script src="https://maps.google.com/maps/api/js?key=YOUR_KEY"></script>
```

#### Further Optimizations
Let’s do some other tweaks to remove unnecessary things. These steps aren’t necessary but are good to do if you are deploying your app only as a PWA.

Currently, right above that service worker script is the script to call *cordova.js*. If you are **only going to run the app as a PWA** and not a cordova app, go ahead and comment it out.
```
<!-- cordova.js required for cordova apps -->
<!--<script src="cordova.js"></script>-->
```
If your only deploying this as a PWA you can go inside *app.component.ts*, and remove the platform and plugin calls:

```
import { Component } from '@angular/core';
import { TabsPage } from '../pages/tabs/tabs';

@Component({
  templateUrl: 'app.html'
})
export class MyApp {
  rootPage: any = TabsPage;

  constructor() {}
}
```
You can also go into the *app.module.ts* file and remove the imports for the *StatusBar* and *SplashScreen* plugins.

#### Web Manifest and Service Worker
The web manifest and Service Worker are what officially make our app a PWA. Let’s take a look at these two features.

Let’s examine the web manifest and the service worker to see what’s going on. First, open src/manifest.json
```
{
  "name": "Ionic",
  "short_name": "Ionic",
  "start_url": "index.html",
  "display": "standalone",
  "icons": [{
    "src": "assets/imgs/logo.png",
    "sizes": "512x512",
    "type": "image/png"
  }],
  "background_color": "#4e8ef7",
  "theme_color": "#4e8ef7"
}
```
This is the standard web manifest that comes with the app. Let’s go through each option:

```"name": "Ionic"```, => This is the name of our application.
```"short_name": "Ionic"```, => This is our short name, used when there’s not enough space for the name. It is most commonly used when the user adds the application to the home screen.
```"start_url": "index.html"```, => This is the URL the app is going to open to.
```"display": "fullscreen"```, => Defines the developer’s preferred display mode for the web application. When using fullscreen all of the available display area is used, and no browser chrome is shown.
The icons object lets you set the icons your app will use depending on the resolution of the screen. The highest resolution icon (512×512) will also be shown on the splash screen of the application
```"icons": [{
  "src": "assets/imgs/logo.png",
  "sizes": "512x512",
  "type": "image/png"
}]
```
```"background_color": "#4e8ef7"```, => Defines the expected background color for the web application.
```"theme_color": "#4e8ef7"``` => Defines the default theme color for an application. This will affect things such as the notification bar color on Android.
Make sure to add your preferred logo to src/assets/img/logo.png. This is the logo the PWA will display when the user adds it to the home screen, and it will show it on the splash screen when the user opens the app through the homescreen or app drawer in Android.

## Step 3

If you need to caching some resources, add them in ```service-worker.js```

## Step 4

Run with ```ionic cordova run browser``` to test the site

**favicon** the favicon must be placed in the src/assets/icon/ folder, then use ionic cordova resources --icon

Compile PWA with ``` ionic cordova build browser --minifyjs --minifycss```

## Step 5 Hosting and deploying on Firebase

The first thing you need to do is to **create a new project in Firebase**, then navigate in the Hosting option and click on **Get started**. You will be promped to install *firebase-tool* 

```
npm install -g firebase-tools
```

And follow the steps below :

```
firebase login

firebase init
--> select "hosting" option and make sure to hit SPACE then ENTER !!

What do you want to use as your public directory ?
--> platforms/browser/www

Configure as a single-page app ?
--> Yes

File www/index.html already exists. Overwrite ?
--> No

// Build your application : NOTE : you can add --minifyjs --minifycss options
ionic cordova build browser --prod 

// OR : npm run ionic:build -- --prod

firebase deploy
```

## Using cordova plugin

### Using Camera plugin in PWA 

https://forum.ionicframework.com/t/ionic-pwa-cordova/104700/4

```
ionic cordova platform add browser
ionic cordova plugin add cordova-plugin-camera
npm install --save @ionic-native/camera
ionic cordova build browser --prod
```

Or simply use : 

```
<ion-content padding>
  <input type="file" accept="image/*" capture="camera" />
</ion-content>
```

## Analysing PWA performance with Lighthouse

After you deploy your PWA, it is recommanded to audit your PWA with Lighthouse chrome extension. You just need to install lighthouse chrome extension, then running your PWA and launch lighthouse audit on your app.

You will get an audit report which indicates some optimizations.

## Optimize PWA

Here are some step to improve your PWA performance :

Replace the ```<meta>``` tag by the following :

```
<meta name="viewport" content="viewport-fit=cover, width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=2.0">
```

Remove all unused css, colors variables, unused plugins
