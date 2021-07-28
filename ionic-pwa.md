[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# PWA

* [Resources](#resources)     
* [Make a PWA](#make-a-pwa)     
* [PWA Builder](#pwa-builder)      

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

## Make a PWA

### Installation
````
// Start a blank new Ionic app
ionic start ionicPwa blank --type=angular --capacitor

// Run the Angular schematic for PWAs
ng add @angular/pwa
````

If you now check out your updated *app.module.ts* you’ll see that the Service Worker will be injected into our app when built for production:

````typescript
@NgModule({
  declarations: [AppComponent],
  entryComponents: [],
  imports: [BrowserModule, IonicModule.forRoot(),
    AppRoutingModule,
    ServiceWorkerModule.register('ngsw-worker.js',
      { enabled: environment.production }
    )],
  providers: [{ provide: RouteReuseStrategy, useClass: IonicRouteStrategy }],
  bootstrap: [AppComponent],
})
export class AppModule { }
````

### Capturing Photos with PWA Elements

> Warning :  there’s no decent web UI when capturing an image, and so we install another package called PWA Elements next to the camera plugin in our app now

````
npm i @capacitor/camera

// Overlay for image capturing on the web
npm install @ionic/pwa-elements
````

To enable those elements, we need to import the defineCustomElements and call it inside our src/main.ts like this:

````typescript
import { enableProdMode } from '@angular/core';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';

import { AppModule } from './app/app.module';
import { environment } from './environments/environment';

// Add the import
import { defineCustomElements } from '@ionic/pwa-elements/loader';

if (environment.production) {
  enableProdMode();
}

platformBrowserDynamic().bootstrapModule(AppModule)
  .catch(err => console.log(err));

// Call the loader
defineCustomElements(window);
````

Now we can import everything we need from the package and call getPhoto() to capture an image and set the resulting webPath to a local variable.

*home.page.ts*
````typescript
import { Component } from '@angular/core';
import { Camera, CameraResultType, CameraSource } from '@capacitor/camera';

@Component({
  selector: 'app-home',
  templateUrl: 'home.page.html',
  styleUrls: ['home.page.scss'],
})
export class HomePage {

  myImage = null;

  constructor() {}

  async takePicture() {
    const image = await Camera.getPhoto({
      quality: 90,
      allowEditing: true,
      resultType: CameraResultType.Uri,
      source: CameraSource.Camera
    });
    this.myImage = image.webPath;
  }
````

Finally we need a simple button and ion-img to display the captured image inside our app, so change the home/home.page.html to:

````html
<ion-header>
  <ion-toolbar color="primary">
    <ion-title>
      Capacitor PWA
    </ion-title>
  </ion-toolbar>
</ion-header>

<ion-content>
  <ion-button (click)="takePicture()" expand="block">
    <ion-icon name="camera" slot="start"></ion-icon>
    Capture image
  </ion-button>

  <ion-img *ngIf="myImage" [src]="myImage"></ion-img>
</ion-content>
````

### Geolocation

To show that the usage of every Capacitor plugin is really that easy, let’s install two more plugins to get the current user location and natively share it:

````
// Install the geolocation plugin
npm i @capacitor/geolocation

// Install the share plugin
npm i @capacitor/
````

Just like before we can directly import the necessary functions from the two packages to first grab the position by calling getCurrentPosition() and then store that value so we can share it later with the share plugin.

*home.page.ts*
````typescript
import { Component } from '@angular/core';
import { Camera, CameraResultType, CameraSource } from '@capacitor/camera';
import { Geolocation, Position } from '@capacitor/geolocation';
import { Share } from '@capacitor/share';

@Component({
  selector: 'app-home',
  templateUrl: 'home.page.html',
  styleUrls: ['home.page.scss'],
})
export class HomePage {

  myImage: string = null;
  position: Position = null;

  constructor() {}

  async takePicture() { ... }

  async getCurrentPosition() {
    const coordinates = await Geolocation.getCurrentPosition();

    this.position = coordinates;
  }

  async share() {
    await Share.share({
      title: 'Come and find me',
      text: `Here's my current location: 
        ${this.position.coords.latitude}, 
        ${this.position.coords.longitude}`,
      url: 'http://ionicacademy.com/'
    });
  }
}
````

Now we need some additional buttons to trigger our new functionality and to display the result, which we can easily do inside an ion-card.

*home.page.html*
````html
<ion-header>
  <ion-toolbar color="primary">
    <ion-title>
      Capacitor PWA
    </ion-title>
  </ion-toolbar>
</ion-header>

<ion-content>
  <ion-button (click)="takePicture()" expand="block">
    <ion-icon name="camera" slot="start"></ion-icon>
    Capture image
  </ion-button>

  <ion-img *ngIf="myImage" [src]="myImage"></ion-img>

  <ion-button (click)="getCurrentPosition()" expand="block">
    <ion-icon name="locate" slot="start"></ion-icon>
    Get position
  </ion-button>

  <!-- present the geolocation information -->
  <ion-card *ngIf="position">
    <ion-card-content>
      <ion-item>
        <ion-icon name="location" slot="start"></ion-icon>
        Lat: {{ position.coords.latitude }}
      </ion-item>
      <ion-item>
        <ion-icon name="location" slot="start"></ion-icon>
        Lng: {{ position.coords.longitude }}
      </ion-item>

      <ion-button (click)="share()" expand="block" color="secondary">
        <ion-icon name="share" slot="start"></ion-icon>
        Share!
      </ion-button>
    </ion-card-content>
  </ion-card>
</ion-content>
````

With those two functionalities you might get into some trouble, because:

Only recent browser versions support the new Web Share API
Safari doesn’t capture a location on unsecure URLs (http), which localhost normally is
That means, we really need to run our PWA on a real device soon.


## PWA Builder

https://preview.pwabuilder.com/


## Browser support

what functionalities are already supported by browser ?
[to open on wanted web browser](https://whatwebcando.today/)    

## Step 1 : What about browser platform ?

"The cordova browser platform was originally built as an experiment from the cordova team and was really meant to be used by plugin developers only. Because of this it causes all kinds of issues when trying to use it as an actual production platform and is why we are going to start officially recommending people **not use it**."   

## Step 2 : Activate Service worker

Uncomment the following code in the index.html to activate service worker to enabling caching resources

```typescript
<script>
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('service-worker.js')
      .then(() => console.log('service worker installed'))
      .catch(err => console.error('Error', err));
  }
</script>
```

And change the title of the app

```html
  <title>My app</title>
```

Don't forget to change the favicon in *src/assets/ico*, notice that the favicon does not well support transparency

Finally, if you use Google map, be sure that the google map api url is in *https*

```html
<script src="https://maps.google.com/maps/api/js?key=YOUR_KEY"></script>
```

#### Further Optimizations
Let’s do some other tweaks to remove unnecessary things. These steps aren’t necessary but are good to do if you are deploying your app only as a PWA.

Currently, right above that service worker script is the script to call *cordova.js*. If you are **only going to run the app as a PWA** and not a cordova app, go ahead and comment it out.
```html
<!-- cordova.js required for cordova apps -->
<!--<script src="cordova.js"></script>-->
```
If your only deploying this as a PWA you can go inside *app.component.ts*, and remove the platform and plugin calls:

```typescript
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
```typescript
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

## Step 3 : Add resources to service worker

If you need to caching some resources, add them in ```service-worker.js```

## Step 4 : Build and Run

Run with ```ionic serve``` to test the site

**favicon** the favicon must be placed in the src/assets/icon/ folder, then use ionic cordova resources --icon

Compile PWA with ``` ionic build --prod```
Old compile method ``` ionic cordova build browser --minifyjs --minifycss```

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

What do you want to use as your public directory ? (old method : platforms/browser/www)
--> www

Configure as a single-page app ?
--> Yes

File www/index.html already exists. Overwrite ?
--> No

// Note : if build with prod failed, you can use
// old : ionic cordova buil browser --minifyjs --minifycss --optimizejs --release
ionic build --prod 

// OR : npm run ionic:build -- --prod

firebase deploy
```

## Hosting issue
When you are switching between multiple firebase account for the same project (i.e one for testing and an other for production), sometimes you may have the following error Error: Authorization failed. This account is missing the following required permissions on project <PROJECT_ID>

To fix it, run firebase use --add before running firebase deploy. You may have the same issue when deploying Firebase Cloud Functions

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
