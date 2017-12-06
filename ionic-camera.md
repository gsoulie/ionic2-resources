# Camera
[Back to top](#ionic-2)  

[See documentation here](https://ionicframework.com/docs/v2/native/camera/)    

First install ionic-native package if needed

```
$ npm install ionic-native --save
```

Then install cordova camera plugin

```
$ ionic cordova plugin add cordova-plugin-camera
```

Implement code

*View file (addPlace.html)*

```xml
<ion-content>
    <form #f="ngForm" (ngSubmit)="onSubmit(f)">
        <ion-list>
            <ion-item>
                <ion-label fixed>Title</ion-label>
                <ion-input type="text" name="title" ngModel required></ion-input>
            </ion-item>
            <ion-item>
                <ion-label floating>Description</ion-label>
                <ion-textarea name="description" ngModel required></ion-textarea>
            </ion-item>
        </ion-list>
        <ion-grid>
            <ion-row>
                <ion-col>
                    <button ion-button block outline type="button" (click)="onTakePhoto()">Take a photo</button>
                </ion-col>
            </ion-row>
            <ion-row *ngIf="imageUrl != ''">
                <ion-col>
		        <img [src]="imageUrl"></img>
                </ion-col>
            </ion-row>
            <ion-row>
                <ion-col>
                    <button ion-button color="secondary" block type="submit" 
		    [disabled]="!f.valid || !locationIsSet">Add this place</button>
                </ion-col>
            </ion-row>
        </ion-grid>        
    </form>
</ion-content>
```

*Controller file (addPlace.ts)*

```javascript

import { SetLocationPage } from "../set-location/set-location";
import { ModalController } from "ionic-angular";
import { Camera } from "ionic-native";

export class LocationPage {
	imageUrl = '';

	constructor(private modalCtrl: ModalController) {}
	
	onTakePhoto() {
		// See the documentation for more camera options
		Camera.getPicture({
			encodingType: Camera.EncodingType.JPEG,
			correctOrientation: true
		})
		.then(
			imageData => {
				this.imageUrl = imageData;
			}
		)
		.catch(
			err => {
				console.log(err);
				Camera.cleanup();	// Only if the image is stored in a new File
			}
		);
	}
}
```