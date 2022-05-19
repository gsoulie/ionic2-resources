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
* [Azure pipeline](#azure-pipeline)      
* [Live reload](#live-reload)     
* [Azure msal authentication with Capacitor MS auth](#azure-msal-authentication-with-capacitor-ms-auth)       
* [Azure msal authentication with Capacitor OAuth 2 client plugin](#azure-msal-authentication-with-capacitor-oauth-2-client-plugin)      

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

### French version

Création d'un splash screen 9-patch

1 - supprimer tous les répertoires landscape

Aller dans le répertoire ````app > src > main > res > drawable```` et supprimer tous les répertoires ````drawable-land-xxxx```` qui ne seront plus utiles.

Ensuite renommer tous les répertoires ````drawable-port-xxxx```` en ````drawable-hdpi, drawable-xhdpi, ...````

2 - Créer les splash screen

Pour chaque répertoire *drawable-xxxx* crééer un splash screen ayant la même résolution que le splash screen par défaut

3 - Création des fichiers 9-patch

Depuis Android Studio, se positionner sur ````app > src > main > res > drawable```` et dans chaque répertoire, pour chaque fichier *splash.png* faire un *clic droit > create 9-patch*.

**Attention :** Ne surtout pas renommer les fichier 9 patchs

4 - Définir les zones extensibles

Ouvrir chaque fichier 9-patch (1 à la fois) pour définir les zones extensibles. Sélectionner l'onglet *9-patch* et cocher la case **Show patches**. Ceci aura pour effet d'afficher votre splash tout rose.
Cela signifie que pour l'instant, toute la zone est extensible.
Le but va être de déplacer les limites horizontales et verticales de manière à obtenir le logo sur fond blanc (non extensible), des zones vertes en milieu-haut, gauche-milieu, droite-milieu et bas-milieu,
des zones roses (extensibles) dans les 4 coins.

**TRES IMPORTANT** : à la fin, on doit aussi avoir des lignes noires (1px) sur le coin haut-gauche, haut-droit et bas-gauche (ci-dessous matérialisées par les **étoiles**)

R : Rose
V : Vert
L : Logo

````
*******------********
*     |	     |      |
*  R  |	 V   |	R   |
*     |	     |	    |
*-----|------|------|
|     |      |      |
|  V  |	 L   |	V   |
|     |	     |	    |
|     |	     |	    |
*-------------------|
*     |      |      |
*  R  |	 V   |	R   |
*     |	     |	    |
*--------------------
````

**Cas des images trop grandes pour être affichées dans l'éditeur** : Si l'image est trop grande pour être affichée dans l'éditeur, l'astuce consiste à zoomer au maximum (800%) et de se positionner
dans un des coins de l'image pour pouvoir ajouter une nouvelle zone flexible

5 - Supprimer les fichiers *splash.png* de chaque répertoire pour ne garder que les 9-patch


## Storage and SQLite
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

### Mobile Cordova camera plugin

Full sample here : https://github.com/gsoulie/capacitor-camera       

### Camera on PWA

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

## Azure pipeline

https://sahansera.dev/multi-stage-builds-with-azure-pipelines-ionic/      
complete CI/CD pipeline : https://www.codewithkarma.com/2020/10/ionic-capacitor-cicd-using-new-yaml.html      

Create a new *pipelines* folder at the root of your project, then create the following pipeline files for a basic Android build (not signed apk) :

> WARNING : be extremely careful with the syntax of yaml files !

*azure-pipelines.yml*

````typescript
trigger:
- develop

variables:
  vmImageName: 'windows-latest'
  projectName: 'MyCoolApp'

stages:
  - stage: Build
    displayName: Build Ionic - Android projects
    jobs:
      # Debug build
      - job: Build_Ionic_Android_Debug
        variables:
          - name: buildConfiguration
            value: Debug
        displayName: Build Debug
        pool:
          vmImage: $(vmImageName)
        steps:
          - template: ionic-android-debug-build.yml

      # Release build
      #- job: Build_Ionic_Android_Release
      #  variables:
      #    - name: buildConfiguration
      #      value: Release
      #  displayName: Build Release
      #  pool:
      #    vmImage: $(vmImageName)
      #  steps:
      #    - template: ionic-android-release-build.yml

````

*ionic-android-debug-build.yml*

````typescript
steps:
  - script: npm install -g @ionic/cli
    displayName: 'Install Ionic CLI'

  - task: Npm@1
    inputs:
      workingDir: '$(Build.SourcesDirectory)'
      command: install
    displayName: 'NPM Install'

  - powershell: |
      ionic info
      npx cap -V
      ls
    workingDirectory: $(Build.SourcesDirectory)
    displayName: 'Get environment infos'

  - powershell: |
      ionic build --configuration=production
      npx cap copy android
      npx cap sync
      cd android
      ./gradlew assemble$(buildConfiguration)
    workingDirectory: $(Build.SourcesDirectory)
    displayName: 'Build Android Project'

  - task: CopyFiles@2
    inputs:
      SourceFolder: '$(Build.SourcesDirectory)/android/app/build/outputs/apk/$(buildConfiguration)'
      contents: "**/app-$(buildConfiguration).apk"
      targetFolder: "$(Build.ArtifactStagingDirectory)/$(projectName)"
    displayName: "Copy unsigned APK to staging directory"

  - task: PublishBuildArtifacts@1
    inputs:
      PathtoPublish: "$(Build.ArtifactStagingDirectory)"
      ArtifactName: "$(projectName)"
      publishLocation: "Container"
    displayName: "Publish artifacts"
````

Then in azure, got to pipeline and create a new pipeline *use the classic editor* > select your branch > *Configuration as code YAML* > select your yml file > *Save & Queue*

[Back to top](#capacitor)    

### Variable replacement before build process

https://www.youtube.com/watch?v=_iPfzH3ENAk&ab_channel=LKGforIT         

Sometimes we want to change some variables like api endpoints when publishing the app with CI/CD.

Steps to follow :

**1 create a config json file in you *assets* folder** which will be loaded with a factory and *APP_INITAILIZER*

*assets/config.json*
````typescript
{
	"api": {
		"endpoint": "httl://localhost:4200/api"
	},
	"app": {
		"infos": {
			"name": "My cool app",
			"version": "1.0"
		}
	}
}
````

**2 Modify your pipeline files**

This step consist in using a new tas **FileTransform@1** which gonna modify the *config.json* file by replacing all the variables you want.

Add the variables you want to replace (do not forget to add a dot for each level off the json hierarchy) in the pipeline file

````
"app": {
		"infos": {
			"name": "My cool app", // gives => app.infos.name
````

*pipelines/azure-pipeline.yml*

````typescript
trigger:
- develop
- master

variables:
  vmImageName: 'windows-latest'
  projectName: 'azurePipeline'
  api.endpoint: http://my-prod-server/api
  app.infos.name: my cool production app
  app.infos.version: v1.0

stages:
  - stage: Build
    displayName: Build Ionic - Android projects
    jobs:
      # Debug build
      - job: Build_Ionic_Android_Debug
        variables:
          - name: buildConfiguration
            value: Debug
        displayName: Build Debug
        pool:
          vmImage: $(vmImageName)
        steps:
          - template: ionic-android-debug-build.yml

````

*ionic-android-debug-build.yml*

````typescript
steps:
  # NEW TASK TO REPLACE VARIABLES IN CONFIG.JSON
  - task: FileTransform@1
    inputs:
      folderPath: '$(Build.SourcesDirectory)'
      targetFiles: '**/config.json'
      fileType: json
    displayName: 'File transformation: config.json'

  # BUILD
  - script: npm install -g @ionic/cli
    displayName: 'Install Ionic CLI'

  - task: Npm@1
    inputs:
      workingDir: '$(Build.SourcesDirectory)'
      command: install
    displayName: 'NPM Install'

  - powershell: |
      ionic info
      npx cap -V
      ls
    workingDirectory: $(Build.SourcesDirectory)
    displayName: 'Get environment infos'

  - powershell: |
      ionic build --configuration=production
      npx cap copy android
      npm install jetifier
      npx jetify
      npx cap sync
    workingDirectory: $(Build.SourcesDirectory)
    displayName: 'Ionic Build'

  - task: Gradle@2
    inputs:
      workingDirectory: '$(Build.SourcesDirectory)/android'
      gradleWrapperFile: '$(Build.SourcesDirectory)/android/gradlew.bat'
      tasks: 'build'
      publishJUnitResults: false
      javaHomeOption: 'JDKVersion'
      jdkVersionOption: '1.11'
      gradleOptions: '-Xmx3072m'
    displayName: 'Build Gradle'

  - task: CopyFiles@2
    inputs:
      SourceFolder: '$(Build.SourcesDirectory)/android/app/build/outputs/apk/$(buildConfiguration)'
      contents: "**/app-$(buildConfiguration).apk"
      targetFolder: "$(Build.ArtifactStagingDirectory)/$(projectName)"
    displayName: "Copy unsigned APK to staging directory"

  - task: PublishBuildArtifacts@1
    inputs:
      PathtoPublish: "$(Build.ArtifactStagingDirectory)"
      ArtifactName: "$(projectName)"
      publishLocation: "Container"
    displayName: "Publish artifacts"

````

That's all. After the pipeline is finished you can verify by downloading the apk and running it on a device

## Live reload
[Back to top](#capacitor)    

Using livereload with capacitor 3 (with capacitor.config.ts)

### Configuration

Modify your *ionic.config.json*

````typescript
{
  "name": "myApp",
  "integrations": {
    "capacitor": {}
  },
  "type": "angular"
}
````

### Usage

run ````ionic capacitor run android -l --external --open```` (select your network)

it will open android studio then select your device and run the app.

After that, livereload is running

### Notice

During the run on mobile, *android.manifest.xml* file is automatically modified, so be aware to not commit it on your git when the app is running.
After stopping the app, the manifest file will be automatically reverted.

## Azure msal authentication with Capacitor MS auth
[Back to top](#capacitor)    

plugin source : https://github.com/recognizegroup/capacitor-plugin-msauth        

### installation 

https://www.npmjs.com/package/@recognizebv/capacitor-plugin-msauth     

````
npm i @recognizebv/capacitor-plugin-msauth
npx cap sync
````

[Back to top](#capacitor) 

### Azure portal configuration

1 - Go to https://portal.azure.com      
2 - Go to Azure Active Directory (use the searchbar if needed)      
3 - Go to App registrations > New registrations      
4 - Fill the app name and select an account type      
5 - Add an redirection URI of type SPA (for Angular / ionic web) with the value http://localhost:4200 or http://localhost:8100 (ionic)      
5b - For a mobile app, you need to add a new mobile app and fill your appid (com.xxx.xxx) and your hash code generated from your keystore (you will need to install keytool and openssl)      
6 - once it is finished, you can get the following infos (tenantId, clientId, redirect URI...)      

*openssl* : https://www.ssl.com/fr/comment/installer-openssl-sur-windows-avec-cygwin/ next you **must** generate your hash from the cygwin console

[Back to top](#capacitor) 

### Ionic Android app configuration

**Android**

*android/build.gradle*

````typescript
buildscript {
    repositories {
        google()
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:7.1.2'
        classpath 'com.google.gms:google-services:4.3.10'
        classpath 'com.microsoft.identity.client:msal:0.3.+'	// <--- Add this
    }
}

// <--- Add : start 
allprojects {
  repositories {
    google()
    jcenter()
    maven {
      url 'https://pkgs.dev.azure.com/MicrosoftDeviceSDK/DuoSDK-Public/_packaging/Duo-SDK-Feed/maven/v1'
    }
  }
}
// ---> Add : end

apply from: "variables.gradle"

task clean(type: Delete) {
    delete rootProject.buildDir
}

````
[Back to top](#capacitor) 

Add the new *BrowserTabActivity*

*AndroidManifest.xml*

````html
<application
	...
	<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
	<activity
	  android:name="com.microsoft.identity.client.BrowserTabActivity"
	  android:exported="true">
	  <intent-filter>
		<action android:name="android.intent.action.VIEW" />
		<category android:name="android.intent.category.DEFAULT" />
		<category android:name="android.intent.category.BROWSABLE" />
		<data
		  android:host="com.xxx.xxx"
		  android:path="/<your_sha1_hash>="
		  android:scheme="msauth"
		/>
		<!-- hash example 
		android:path="/sqlmdkqsf84fzerzf454azz8="
		-->
	  </intent-filter>
	</activity>
</application>
````
[Back to top](#capacitor)     

### Usage (ionic android app)

*authentication.service.ts*

````typescript
import { BehaviorSubject } from 'rxjs';
import { Injectable } from '@angular/core';
import { MsAuthPlugin  } from '@recognizebv/capacitor-plugin-msauth';
import jwt_decode from "jwt-decode";

@Injectable({
  providedIn: 'root'
})
export class AuthenticationService {

  private accessToken$ = new BehaviorSubject<string>('');

  constructor() { }

  getAccessToken() {
    return localStorage.getItem('accessToken');
  }

  async login() {
    const result = await MsAuthPlugin.login({
      clientId: <your_client_id_from_azure_portal>,
      tenant: <your_tenant_from_azure_portal>,
      scopes: [<your_scopes_azure_portal>],
      keyHash: <your_openssl_sha1_hash>,
    });

    this.accessToken$.next(result.accessToken);
    localStorage.setItem('accessToken', result.accessToken);

    return result.accessToken;
  }

  async logout() {
    const result = await MsAuthPlugin.logout({
     clientId: <your_client_id_from_azure_portal>,
      tenant: <your_tenant_from_azure_portal>,
      keyHash: <your_openssl_sha1_hash>,
    });

    this.accessToken$.next('');
    localStorage.setItem('accessToken', '');

    return '';
  }

  getTokenInfo() {
    const token = this.getAccessToken();
    let decoded: any = null;
    if (token !== null) {
      decoded = jwt_decode(token);
    }
    return decoded;
  }
}

````
[Back to top](#capacitor)     

*httpInterceptor.service.ts*

````typescript
import { AuthenticationService } from './authentication/authentication.service';
import { HttpErrorResponse, HttpEvent, HttpHandler, HttpInterceptor, HttpRequest, HttpHeaders } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Router } from '@angular/router';
import { Observable, throwError } from 'rxjs';
import { catchError, retry } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class HttpInterceptorService implements HttpInterceptor {

  constructor(
    private authService: AuthenticationService,
    private router: Router) { }

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    let authReq = req;

    const headers = new HttpHeaders({
      Authorization: `Bearer ${this.authService.getAccessToken()}`
    });
    authReq = req.clone({headers});

    return next.handle(authReq)
    .pipe(
      retry(2),
      catchError((error: HttpErrorResponse) => {
        let errorMessage = '';
        if (error.error instanceof ErrorEvent) {
          // client-side error
          errorMessage = `>>>Error: ${error.error.message}`;
        } else {
          // server-side error
          errorMessage = `>>>Error Status: ${error.status}\nMessage: ${error.message}`;
        }
        switch (error.status) {

          case 401 :
            this.authService.login();
            break;
          default:
            break;
        }
        return throwError(error);
      })
    );
  }
}
````
[Back to top](#capacitor)     

*app.module.ts*

````typescript
providers: [{ provide: RouteReuseStrategy, useClass: IonicRouteStrategy },
    {
      provide : HTTP_INTERCEPTORS,
      useClass: HttpInterceptorService,
      multi   : true,
    },
````
[Back to top](#capacitor)     

*auth.guard.ts*

````typescript
import { AuthenticationService } from './../services/core/authentication/authentication.service';
import { Injectable } from '@angular/core';
import { ActivatedRouteSnapshot, CanActivate, CanActivateChild, Router, RouterStateSnapshot, UrlTree } from '@angular/router';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate, CanActivateChild {
  constructor(private authService: AuthenticationService,
    private router: Router) {}

  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {

    const isAuthenticated = this.authService.getAccessToken() !== '' && this.authService.getAccessToken() !== null;
    if (!isAuthenticated) { this.router.navigate(['/']); }
    return isAuthenticated;

  }
  canActivateChild(
    childRoute: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {
    return this.canActivate(childRoute, state);
  }
}
````

[Back to top](#capacitor)     

## Azure msal authentication with Capacitor OAuth 2 client plugin

capacitor-oauth2 : https://github.com/moberwasserlechner/capacitor-oauth2#android-1      
capacitor secure storage : https://www.npmjs.com/package/capacitor-secure-storage-plugin      

This plugin is a **generic OAuth 2 client** plugin.

### installation

````
npm i @byteowls/capacitor-oauth2
npm install capacitor-secure-storage-plugin
npx cap sync
````
[Back to top](#capacitor)     

### Using Azure AD authentication

#### Add the following activity in your *AndroidManifest.xml*

*AndroidManifest.xml*

````html
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    ...>

    <application
        ...>

        <activity
            ...>

            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

		  <!-- Add the following intent-filter -->
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="@string/custom_url_scheme" android:host="@string/custom_host" />
          </intent-filter>
        </activity>

      <!-- Add the following activity -->
      <activity android:name="net.openid.appauth.RedirectUriReceiverActivity" android:exported="true">
        <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="@string/custom_url_scheme" android:host="@string/custom_host" />
        </intent-filter>

        <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data 
		  android:scheme="@string/azure_b2c_scheme" 
		  android:host="@string/package_name" 
		  android:path="@string/azure_b2c_signature_hash" />
        </intent-filter>
      </activity>
       
    </application>

    <!-- Permissions -->
	...
</manifest>
````
[Back to top](#capacitor)     

#### Add the following configuration in *app/src/main/res/values/string.xml*

````html
<resources>
    <string name="app_name">your app name</string>
    <string name="title_activity_main">your activity</string>
    <string name="package_name">com.myapp.name</string>
    <string name="custom_url_scheme">com.myapp.name</string>

    <string name="custom_host">foo</string><!-- any value is fine -->
    <string name="azure_b2c_scheme">msauth</string>
    <string name="azure_b2c_signature_hash">/pedl56546efAZDd5zaz4az=</string><!-- Your hask key. The leading slash is required. Copied from Azure Portal Android Config "Signature hash" field -->
</resources>
````
[Back to top](#capacitor)     

#### Add the following into your android/app/build.gradle

````
apply plugin: 'com.android.application

android {
    compileSdkVersion rootProject.ext.compileSdkVersion
    defaultConfig {
        ...
        manifestPlaceholders = [appAuthRedirectScheme: 'com.myapp.name']	// <-- add this placeholder
    }
    buildTypes {
        ...
    }
}
repositories {
    ...
}
dependencies {
    ...
}
apply from: 'capacitor.build.gradle'

````
[Back to top](#capacitor)     

### Usage

*auth.service.ts*

````typescript
import { environment } from 'src/environments/environment';
import { Injectable } from '@angular/core';
import { OAuth2AuthenticateOptions } from '@byteowls/capacitor-oauth2';

@Injectable({
  providedIn: 'root'
})
export class AuthService {

  constructor() { }

  getAzureB2cOAuth2Options(): OAuth2AuthenticateOptions {
    return {
        appId: environment.authentication.clientId,
        authorizationBaseUrl: `${environment.authentication.authority}/${environment.authentication.tenant}/oauth2/v2.0/authorize`,
        scope: environment.authentication.apiName, // See Azure Portal -> API permission
        accessTokenEndpoint: `${environment.authentication.authority}/${environment.authentication.tenant}/oauth2/v2.0/token`,
        resourceUrl: null,
        responseType: 'code',
        pkceEnabled: true,
        logsEnabled: true,
        web: {
            redirectUrl: environment.authentication.redirectUriWeb,
            windowOptions: 'height=600,left=0,top=0',
        },
        android: {
          redirectUrl: `${environment.authentication.redirectUriMsauth}`,
          responseType: 'code',
          handleResultOnNewIntent: true,
          handleResultOnActivityResult: true
          /*appId: environment.authentication.clientId,
          pkceEnabled: true,
          responseType: 'code',
          accessTokenEndpoint: `${environment.authentication.authority}/${environment.authentication.tenant}/oauth2/v2.0/token`,
   */
        },
        ios: {
            pkceEnabled: true, // workaround for bug #111
            redirectUrl: 'msauth.com.myapp.name://auth'
        }
    };
  }
}
````

*environment.ts*

````typescript
export const environment = {
  authentication: {
    clientId: 's2az5r-d1q5-s4fe8-z54r-d4d5faz5e4aee',
    redirectUri: 'com.myapp.name://dashboard',
    redirectUriMsauth: 'msauth://com.myapp.name/pedl56546efAZDd5zaz4az=',
    redirectUriWeb: 'http://localhost:8100',
    tenant: 'mycustom.onmicrosoft.com',
    apiName: 'acces_api',
    authority: 'https://login.microsoftonline.com'
  },
  production: false
};
````

*home.page.html*

````html
<ion-button (click)="login()">login</ion-button>
<ion-button (click)="refresh()">refresh</ion-button>
<ion-button (click)="logout()">logout</ion-button>

<h4>Access token</h4>
<p>{{ accessToken | slice:0:150 }}</p>

<h4>Refresh token</h4>
<p>{{ refreshToken | slice:0:150 }}</p>
````

*home.page.ts*

````typescript
import {OAuth2Client} from '@byteowls/capacitor-oauth2';
import { SecureStoragePlugin } from 'capacitor-secure-storage-plugin';
import { AuthService } from './../shared/services/core/authentication/auth.service';

accessToken = '';
refreshToken = '';

constructor(
    private documentHelper: DocumentHelperService,
    private authService: AuthService) {}

  async ngOnInit() {
    await SecureStoragePlugin.get({key: 'my_token'})
    .then(res => this.accessToken = res.value)
    .catch(err => this.accessToken = '');
  }

  login() {
    const opt = this.authService.getAzureB2cOAuth2Options();
    OAuth2Client.authenticate(
      opt
    ).then(response => {
      console.log('login response', response);

      this.accessToken = response['access_token'];
      this.refreshToken = response['refresh_token'];
      SecureStoragePlugin.set({ key: 'my_token', value: this.accessToken });

      // only if you include a resourceUrl protected user values are included in the response!
      const oauthUserId = response['id'];
      const name = response['name'];

        // go to backend
    }).catch(reason => {
        console.error('OAuth rejected', reason);
    });
  }
  logout() {
    OAuth2Client.logout(
      this.authService.getAzureB2cOAuth2Options()
    ).then(() => {
        // do something
        SecureStoragePlugin.remove({ key: 'my_token' });
        this.accessToken = '';
    }).catch(reason => {
        console.error('OAuth logout failed', reason);
    });
  }
  refresh() {
    if (!this.refreshToken) {
      console.error('No refresh token found. Log in with OAuth first.');
    }

    const opt = this.authService.getAzureB2cOAuth2Options();
    OAuth2Client.refreshToken(
      {
        appId: opt.appId,
        accessTokenEndpoint: opt.accessTokenEndpoint,
        refreshToken: this.refreshToken,
        scope: opt.scope
      }
    ).then(response => {
      this.accessToken = response['access_token'];
      // Don't forget to store the new refresh token as well!
      this.refreshToken = response['refresh_token'];
      // Go to backend
    }).catch(reason => {
        console.error('Refreshing token failed', reason);
    });
  }
````
[Back to top](#capacitor)     
