[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Capacitor

* [Capacitor 3.0](#capacitor-3-0)      
* [Capacitor and PWA](#capacitor-and-pwa)        
* [Build Android Prod version](#build-android-prod-version)       
* [App icon and Splashscreen](#app-icon-and-splashscreen)        
* [Storage and Sqlite](#storage-and-sqlite)      
* [Listening internet connection](#listening-internet-connection)     
* [Haptics](#haptics)      
* [Android permissions](#android-permissions)      
* [Photo](#photo)      

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
```npx cap init```

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

You can also simply run ```npx cap sync``` to both copy your web code and update your plugins

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

```typescript
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

## Storage adn SQLite
[Back to top](#capacitor)     

MAKE THE GOOD CHOICE : https://ionicframework.com/blog/choosing-a-data-storage-solution-ionic-storage-capacitor-storage-sqlite-or-ionic-secure-storage/

*Ionic Storage* : utilise par défaut *localStorage* et *IndexedDB* (plutôt pour une version web). 
=> type clé-valeur, on peut y insérer des objets
=> sélectionne automatiquement la mailleure solution pour le stockage : *IndexedDB* et sinon *LocalStorage*
=> fonctionne aussi avec le driver SQLite pour les app mobiles
=> pas fait pour de grosses structures de données car pas de requêtage et diminue en perf si les objets sont trop complexes

En mode web, *localStorage* => peu de données en asynchrone avec *indexedDB* suffit

*capacitor storage* : peut être gorumand en perf. Ne peut stocker QUE des chaînes. Pour stocker des objets il faut donc les stringifier avant et les parser en sortie = perte de performance

*sqlite* : seulement disponible pour les app natives avec capacitor. Permet le requêtage complexe. Certains plugins permettent également d'importer un dump JSON et de peupler la bdd à l'initialisation

*ionic secure storage (payant)* : stockage SQLite crypté

**Quel choix ?**

- petits stockages de paramètres => *capacitor storage*
- stockage d'objets un peu plus gros => *ionic storage*
- *sqlite* => uniquement sur app natives
- gros requêtage et volumétrie => *SQLite*
- 
### Ionic Storage V3

Local Storage on web and mobile with IndexedDB and CordovaSQLite driver

https://www.youtube.com/watch?v=vCfAe2esboU&ab_channel=SimonGrimm      
https://ionicacademy.com/ionic-storage-v3-with-angular/       

*Installation*

````
npm i @ionic/storage-angular
npm install cordova-sqlite-storage	// for capacitor only
ionic cordova plugin add cordova-sqlite-storage // for cordova only
npm install localforage-cordovasqlitedriver
````

*app.module.ts*

````typescript
import { IonicStorageModule } from '@ionic/storage-angular';
import * as cordovaSQLiteDriver from 'localforage-cordovasqlitedriver';
import { Drivers } from '@ionic/storage';

@NgModule({
  declarations: [AppComponent],
  entryComponents: [],
  imports: [
    BrowserModule,
    HttpClientModule,
    IonicModule.forRoot(),
    AppRoutingModule,
    IonicStorageModule.forRoot({
      name: 'localstorage-testDB',  // name of your database
      driverOrder: [cordovaSQLiteDriver._driver, Drivers.IndexedDB, Drivers.LocalStorage] // drivers used for mobile and web app (order is important)
    })],
  providers: [{ provide: RouteReuseStrategy, useClass: IonicRouteStrategy }],
  bootstrap: [AppComponent],
})
export class AppModule {}
````

*storage.service.ts*

````typescript
import { filter, switchMap } from 'rxjs/operators';
import { BehaviorSubject, from, of } from 'rxjs';
import { Injectable } from '@angular/core';
import { Storage } from '@ionic/storage-angular';
import * as cordovaSQLiteDriver from 'localforage-cordovasqlitedriver';

const STORAGE_KEY = 'myTestKey';

@Injectable({
  providedIn: 'root'
})
export class StorageService {

  private storageReady = new BehaviorSubject(false);

  constructor(private storage: Storage) {
    this.init();
  }

  async init() {
    await this.storage.defineDriver(cordovaSQLiteDriver); // For mobile target
    await this.storage.create();
    this.storageReady.next(true);
  }

  getData() {
    // Only return result if storage is initialized

    return this.storageReady.pipe(
      filter(ready => ready),
      switchMap(_ => {
        return from(this.storage.get(STORAGE_KEY)) || of([]);
      })
    );
  }

  async addData(item: any) {
    const storedData = await this.storage.get(STORAGE_KEY) || [];
    storedData.push(item);
    return this.storage.set(STORAGE_KEY, storedData);
  }

  async removeData(index) {
    let storedData = await this.storage.get(STORAGE_KEY) || [];
    storedData.splice(index, 1);
    return this.storage.set(STORAGE_KEY, storedData);
  }
}
````

*home.page.html*

````html
<ion-button (click)="addRandomData()">Add user</ion-button>
<ion-list>
    <ion-item lines="none" *ngFor="let user of listData; let i = index">
      {{ user.name }}
      <ion-button (click)="removeItem(i)" fill="clear" slot="end">
        <ion-icon name="trash" slot="icon-only"></ion-icon>
      </ion-button>
    </ion-item>
</ion-list>
````

*home.controller.ts*

````typescript
import { Subscription } from 'rxjs';
import { StorageService } from './../../shared/services/storage.service';
import { Component, OnDestroy, OnInit } from '@angular/core';

export class User {
  constructor(public name: string, public surname: string) {}
}

@Component({
  selector: 'app-localstorage',
  templateUrl: './localstorage.page.html',
  styleUrls: ['./localstorage.page.scss'],
})
export class LocalstoragePage implements OnInit, OnDestroy {

  listData = [];
  subscriptions: Subscription;
  constructor(private dataService: StorageService) { }

  ngOnInit() { this.loadData(); }

  loadData() {
    this.subscriptions = this.dataService.getData()
    .subscribe(res => {
      this.listData = res;
    })
  }

  async addRandomData() {
    await this.dataService.addData(new User('John', 'Doe'));
    this.loadData();  // pas otpimisé, il faudrait garder une copie des données dans une variable du service et faire un push
    // pour éviter de tout recharger
  }

  async removeItem(index) {
    this.dataService.removeData(index);
    this.listData.splice(index, 1);
  }

  ngOnDestroy(): void { this.subscriptions.unsubscribe(); }
}
````

### Caching Api response with storage SQLite

````Working sample code```` [here](https://github.com/gsoulie/ionic-angular-snippets/tree/master/cachingApiResponse)      

### LocalForage

[LocalForage documentation](https://github.com/localForage/localForage)      


### SQLite ionic app with capacitor

````correction needed capacitor 3.0 issue```` [Simon Grimm based Code - not functionnal](https://github.com/gsoulie/ionic2-resources/blob/master/ionic-capacitor-sqlite.md)      
````basic community working sample```` [Capacitor community based code - functionnal](https://github.com/gsoulie/ionic2-resources/blob/master/ionic-capacitor-community-sqlite.md)      
````personnal customized community based sample```` [Personnal customized community based code with helper](https://github.com/gsoulie/capacitor-sqlite)      

[other source](https://www.youtube.com/watch?v=j7hpGoODD_k&ab_channel=CodeForYou)     

````IMPORTANT```` : Works on real device (and emulator) but not on web browser because SQLite plugin is a capacitor plugin

### SQLite Queries and pre-populated database

[Simon grimm tutorial](https://www.youtube.com/watch?v=E6h8PFHMLIU&ab_channel=SimonGrimm)     
[Simon grimm code](https://devdactic.com/ionic-sqlite-queries-database/)       

## Listening Internet connection
[Back to top](#capacitor)  

https://developer.school/ionic-5-network-detection-with-capacitor/      

As an example, when a user is on a slower connection, they get an alert that performance may be degraded:

````typescript
if (navigator.connection.effectiveType != '4g') {
  console.log('slow connection!');
  // show modal dialog warning user that video will be loaded at lower resolution
}
else {
  console.log('ready to go!');
  // immediate load video file
}
````

Do not forget **Android permission** ````<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />````

### Solution with Capacitor Network API
[Back to top](#capacitor)   

Another option is Capacitor’s Network API. It extends the Network Information API to provide even more useful features for web and mobile apps, such as monitoring the network for status changes, which your app can then react to.

````
npm install @capacitor/network
npx cap sync
````

*NetworkState.service.ts*

````typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs';
import { ConnectionStatus, Network } from '@capacitor/network';
import { PluginListenerHandle } from '@capacitor/core';
import { Platform, ToastController } from '@ionic/angular';

@Injectable({
  providedIn: 'root'
})
export class NetworkStateService {

  networkStatus$: BehaviorSubject<boolean> = new BehaviorSubject(false);
  private networkListener: PluginListenerHandle;

  constructor(
    private toastController: ToastController,
    private plt: Platform) {

    this.plt.ready().then(() => {
      // initialize network listerner at platform start
      this.initializeNetworkEvents();
    });
  }

  private async initializeNetworkEvents() {
    this.networkListener = Network.addListener('networkStatusChange', (status) => {
      this.networkStatus$.next(status.connected); // update network status
      this.presentToast('You are ' + (status.connected ? 'Online' : 'Offline'));
    });

    await this.networkStatus$.next((await Network.getStatus()).connected);  // set initial state
  }

  // Return only "connected" value
  public networkIsConnected(): boolean {
    return this.networkStatus$.getValue();
  }

  /**
   * Return full ConnectionStatus object
   * @returns
   */
  public async currentNetworkStatus(): Promise<ConnectionStatus> {
    return await Network.getStatus();
  }

  logCurrentNetworkStatus = async () => {
    const status = await Network.getStatus();
    alert('Network status:' + status.connected);
    console.log('Network status:', status);
  };

  private async presentToast(message = '') {
    if (message !== '') {
      const toast = await this.toastController.create({
        message,
        duration: 3000,
        position: 'bottom'
      });
      await toast.present();
      const { role } = await toast.onDidDismiss();
    }
  }
  public removeNetworkListener() {
    this.networkListener.remove();
  }

}
````

*home.ts*

````typescript
import { Subscription } from 'rxjs';
import { NetworkStateService } from './../shared/services/network-state.service';
import { Menu } from './../shared/models/menu.model';
import { ChangeDetectorRef, Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-home',
  template: `
  <ion-content [fullscreen]="true">
    <h1>Network status: {{ networkStatus }}</h1>
    <ion-button (click)="isConnected()">Is connected ?</ion-button>
  </ion-content>
  `,
  styleUrls: ['home.page.scss'],
})
export class HomePage implements OnInit {

  subscriber$: Subscription;
  networkStatus: boolean;

  constructor(
    public network: NetworkStateService,
    private ref: ChangeDetectorRef) {}

  async ngOnInit() {

    this.subscriber$ = this.network.networkStatus$
    .subscribe(() => {
      this.networkStatus = this.network.networkStatus$.getValue();
      this.network.logCurrentNetworkStatus();
      this.ref.detectChanges(); // important to update the view
    });
  }

  isConnected(): void {
    alert(this.network.networkIsConnected());
  }
  ngOnDestroy() {
    this.subscriber$.unsubscribe();
    this.network.removeNetworkListener();
  }
}
````

### Minimal code (less cleaner than above)

````typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { Plugins, NetworkStatus, PluginListenerHandle } from '@capacitor/core';

const { Network } = Plugins;

@Component({
  selector: 'app-home',
  templateUrl: 'home.page.html',
  styleUrls: ['home.page.scss'],
})
export class HomePage implements OnInit, OnDestroy {
  networkStatus: NetworkStatus;
  networkListener: PluginListenerHandle;

  async ngOnInit() {
    this.networkListener = Network.addListener('networkStatusChange', (status) => {
      console.log("Network status changed", status);
      this.networkStatus = status;
    });

    this.networkStatus = await Network.getStatus();
  }

  ngOnDestroy() {
    this.networkListener.remove();
  }
}
````

[Back to top](#capacitor)   

## Haptics

Provide physical feeback to the user

https://capacitorjs.com/docs/apis/haptics

## Android permissions
[Back to top](#capacitor)     

[Official documentation](https://ionicframework.com/docs/native/android-permissions)       
[Tutorial](https://www.youtube.com/watch?v=ZqOiGBUmL60&ab_channel=RealApps)      

### Installation

````
npm install cordova-plugin-android-permissions
npm install @ionic-native/android-permissions
ionic build
ionic cap sync
````

### initialization

*app.module.ts*

````typescript
import { AndroidPermissions } from '@ionic-native/android-permissions/ngx';

@NgModule({
  declarations: [AppComponent,
    PhotoComponent,
    HeaderComponent
  ],
  entryComponents: [],
  imports: [...],
  providers: [{ provide: RouteReuseStrategy, useClass: IonicRouteStrategy },
    AndroidPermissions],
    bootstrap: [AppComponent],
})
export class AppModule {}

````

### Usage

*home.component.ts*

````typescript
import { AndroidPermissions } from '@ionic-native/android-permissions/ngx';

constructor(private androidPermissions: AndroidPermissions) { }

save(): void {
    this.androidPermissions.checkPermission(this.androidPermissions.PERMISSION.WRITE_EXTERNAL_STORAGE)
    .then(
      result => {
        if (result.hasPermission) {
          // do some stuff here to save image to gallery for example
        }
        else {
          this.requestPermissions();
        }
      },
      err => this.requestPermissions()
    );
  }
requestPermissions() {
    this.androidPermissions.requestPermissions([
      this.androidPermissions.PERMISSION.READ_EXTERNAL_STORAGE,
      this.androidPermissions.PERMISSION.WRITE_EXTERNAL_STORAGE])
    .then(
      res => {
        console.log('Saved image to gallery ', res);
        this.save();
      },
      err => console.log('Error saving image to gallery ', err)
    );
  }
````

## Photo
[Back to top](#capacitor)    

https://ionicframework.com/docs/angular/your-first-app/2-taking-photos

````
npm install @capacitor/camera
npm install @ionic/pwa-elements   // for PWA mode
````

> Notice : for mobile usage we need to manage android and ios permissions https://capacitorjs.com/docs/apis/camera?_gl=1*1kply45*_ga*MTY0MjE2NzMwMy4xNjA0NDgxMjA5*_ga_REH9TJF6KF*MTYzMDU4NzY5MC4xMzAuMS4xNjMwNTg3NzExLjA.

### PWA mode 

*main.ts* // for pwa support

````typescript
import { enableProdMode } from '@angular/core';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';

import { AppModule } from './app/app.module';
import { environment } from './environments/environment';

import { defineCustomElements } from '@ionic/pwa-elements/loader';

if (environment.production) {
  enableProdMode();
}

platformBrowserDynamic()
  .bootstrapModule(AppModule)
  .catch(err => console.log(err));

// Call the element loader after the platform has been bootstrapped
defineCustomElements(window);
````

*photo.component.ts*

````typescript
import { Camera, CameraResultType } from '@capacitor/camera';
import { LocalstorageService } from './../../shared/services/localstorage.service';
import { Component, OnInit, ElementRef, ViewChild } from '@angular/core';

@Component({
  selector: 'app-photo',
  templateUrl: './photo.component.html',
  styleUrls: ['./photo.component.scss'],
})
export class PhotoComponent implements OnInit {

  converted_image = '';
  constructor(private localStorage: LocalstorageService) { }

  ngOnInit() {}

  async takePicture() {
    const image = await Camera.getPhoto({
      quality: 90,
      allowEditing: false,
      resultType: CameraResultType.Base64
    });

    const base64Result = image.base64String;
    this.converted_image= "data:image/jpeg;base64,"+base64Result;
    this.localStorage.addData({image: base64Result});
  }

  resetPicture(): void {
    this.converted_image = '';
  }
}
````

*photo.component.html*

````html
<div class="container">
  <ion-toolbar>
    <ion-button (click)="takePicture()">Take picture</ion-button>
    <ion-button (click)="resetPicture()">Reset picture</ion-button>
  </ion-toolbar>
  <p>Taking photo with Camera plugin in PWA mode and store result as base64 image in Ionic Storage V3</p>
  <div class="picture">
    <img  [src]="converted_image" alt="Select Image"/>
  </div>
</div>
````

### Mobile mode

````typescript
	export class PhotoComponent implements OnInit {

  imagePath = '';
  @ViewChild('picture') imageElement: ElementRef;

  constructor(private localStorage: LocalstorageService) { }

  ngOnInit() {}

  async takePicture() {
    const image = await Camera.getPhoto({
      quality: 90,
      allowEditing: false,
      resultType: CameraResultType.Uri
    });

    // image.webPath will contain a path that can be set as an image src.
    // You can access the original file using image.path, which can be
    // passed to the Filesystem API to read the raw data of the image,
    // if desired (or pass resultType: CameraResultType.Base64 to getPhoto)

    const imageUrl = image.webPath;

    // Can be set to the src of an image now
    this.imageElement.nativeElement.src = imageUrl;
    this.imagePath = imageUrl;

    this.localStorage.addData({imageUrl: imageUrl});
  }

  resetPicture(): void {
    this.imagePath = '';
    this.imageElement.nativeElement.src = '';
  }

  checkPermission(): void {
    Camera.checkPermissions()
    .then((res) => {
      alert(JSON.stringify(res));
    });
  }

  requestPermission() {
    Camera.requestPermissions()
    .then(() => {});

  }
}
````

*photo.component.html*

````html
...
<div class="picture">
    <img #picture>
  </div>
````

[Back to top](#capacitor)     
