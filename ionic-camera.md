[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Camera

* [Taking photo with capacitor on mobile and PWA](#taking-photo-with-capacitor-on-mobile-and-pwa)     
* [Using cordova](#using-cordova)    

## Taking photo with capacitor on mobile and PWA

[Taking Photos with the Camera](https://ionicframework.com/docs/angular/your-first-app/2-taking-photos)    

First, create a photo service ````ionic g service services/photo````

*photo-service.ts*

````typescript
import { Plugins, CameraResultType, Capacitor, FilesystemDirectory, 
         CameraPhoto, CameraSource } from '@capacitor/core';

const { Camera, Filesystem, Storage } = Plugins;

interface Photo {
  filepath: string;
  webviewPath: string;
  base64?: string;
}

export class PhotoService {
  public photos: Photo[] = [];
  
  public async addNewToGallery() {
      // Take a photo
      const capturedPhoto = await Camera.getPhoto({
        resultType: CameraResultType.Uri, 
        source: CameraSource.Camera, 
        quality: 100 
      });
      
      this.photos.unshift({
        filepath: "soon...",
        webviewPath: capturedPhoto.webPath
      });
    }
}
````

*home.page.ts*

````typescript
import { PhotoService } from '../services/photo.service';

export class HomePage {
    photos = this.photoService.photos;
    
    constructor(public photoService: PhotoService) { }

    addPhotoToGallery() {
      this.photoService.addNewToGallery();
    }
}
````

*home.page.html*

````html
<ion-content>
   <ion-grid>
    <ion-row>
    <ion-col size="6" 
      *ngFor="let photo of photos; index as position">
        <ion-img src="{{ photo.webviewPath }}"></ion-img>
    </ion-col>
    </ion-row>
  </ion-grid>
  
  <ion-fab vertical="bottom" horizontal="center" slot="fixed">
    <ion-fab-button (click)="addPhotoToGallery()">
      <ion-icon name="camera"></ion-icon>
    </ion-fab-button>
  </ion-fab>
</ion-content>
````


## Using Cordova

[See documentation here](https://ionicframework.com/docs/developer-resources/guides/first-app-v4/ios-android-camera)    

**plugin installation**

```
npm install @ionic-native/camera
ionic cordova plugin add cordova-plugin-camera
```

**iOS requirements**

The next step is only required for iOS users. As of iOS 10, developers must provide a reason for why the app wishes to access the device camera. Add this to the bottom of config.xml:

```html
<!-- Required for iOS 10: Camera permission prompt -->
<edit-config file="*-Info.plist" mode="merge" target="NSCameraUsageDescription">
    <string>Used to take pictures</string>
</edit-config>
```

**Add plugin to app module**

```typescript
import { Camera } from '@ionic-native/camera/ngx';

Then, add it as a Provider:

providers: [
    StatusBar,
    SplashScreen,
    Camera,
    {provide: ErrorHandler, useClass: IonicErrorHandler}
  ],
```

**Implementation**

*View.html*

```html
<ion-fab vertical="bottom" horizontal="center" slot="fixed">
  <ion-fab-button (click)="takePicture()">
    <ion-icon name="camera"></ion-icon>
  </ion-fab-button>
</ion-fab>
<img [src]="currentImage" *ngIf="currentImage">
```

*Controller.ts*

```typescript
import { Camera, CameraOptions } from '@ionic-native/camera/ngx';

export class Tab2Page {
  currentImage: any;

  constructor(private camera: Camera) { }

  takePicture() {
    const options: CameraOptions = {
      quality: 100,
      destinationType: this.camera.DestinationType.DATA_URL,
      encodingType: this.camera.EncodingType.JPEG,
      mediaType: this.camera.MediaType.PICTURE
    }

    this.camera.getPicture(options).then((imageData) => {
      this.currentImage = 'data:image/jpeg;base64,' + imageData;
    }, (err) => {
     // Handle error
     console.log("Camera issue:" + err);
    });
  }
}
```
