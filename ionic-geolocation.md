[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Geolocation

* [Angular2 Google maps integration](angular2-google-maps-integration)    
* [Customize map markers](#customize-map-markers)    
* [reverse geocoding](#reverse-geocoding)    

## See google map integration (full example)
[Full example here](https://github.com/gsoulie/ionic2-resources/blob/master/ionic-google-map-full.md)    


## Angular2 Google maps integration
[link : google map geolocation](http://www.joshmorony.com/ionic-2-how-to-use-google-maps-geolocation-video-tutorial/)

First we are going to see how to add a map on our application and add / display markers

So, first install angular2-google-maps package :

```
npm install --save angular2-google-maps
```

Then insert the *sebm-google-map* module to your view file

*View file (SetLocationPage.html)*

```xml
<ion-content>
	<sebm-google-map 
	[latitude]="location.lat" 
	[longitude]="location.lng" 
	[zoom]="16"
	(mapClick)="onSetMarker($event)">
		<sebm-google-map-marker [latitude]="marker.lat" [longitude]="marker.lng" *ngIf="marker"></sebm-google-map-marker>
	</sebm-google-map>
	<button ion-button block (click)="onConfirm()" [disabled]="!marker">Confirm</button>
</ion-content>
```
Next, add the module dependency in your *app.module.ts* file

Note that you first need to get a google map Api key 

[see angular-maps setup](https://angular-maps.com/docs/getting-started.html)    
[Get your GMAP API Key](https://developers.google.com/maps/documentation/javascript/get-api-key?hl=en#key)    

**GMAP API KEY summary**

1 - [Get your GMAP API Key here](https://developers.google.com/maps/documentation/javascript/get-api-key?hl=en#key)    
2 - Click on the *GET A KEY* button     
3 - Select your firebase project in the list or create a new    
4 - Copy the generated key    
5 - Insert the key in your AgmCoreModule configuration (like below)    

*app.module.ts*

```javascript
...
import { AgmCoreModule } from "angular2-google-maps/core";

@NgModule({
	declarations : [...],
	imports: [
		...,
		AgmCoreModule.forRoot({
			apiKey: 'qsodkpazd51a6dz5a65z1eq24drsd5sd'
		})
	],
	...
})

```

Next you must specify a height in your scss file else your view will be empty :

*Style file (SetLocationPage.scss)*
```css
page-set-location-page {
	sebm-google-map {
		height: 250px;
	}
}
```

*Controller file (SetLocationPage.ts)*

```javascript
import { NavParams, ViewController } from "ionic-angular";

export class LocationPage {
	location: any = {};	// Note : can be an object of class "Location" containing lat and lng attributes
	marker: any = {};
	
	constructor(private navParams: NavParams, private viewCtrl: ViewController) { 
		this.location = this.navParams.get("location"); 
		
		// retrieve chosen marker
		if(this.navParams.get('isSet')) {
			this.marker = this.location;
		}
	}

	// Add marker on the map
	onSetMarker(event: any){
		this.marker = {lat: event.coords.lat, lng: event.coords.lng};
	}
	
	// Confirm the location and pass the chosen location back to the page
	onConfirm(){
		// Close the modal with the chosen location
		this.viewCtrl.dismiss({location: this.marker});
	}
	
}
```

Finally, call your Location page :

*Controller file (addPlace.ts)*

```javascript

import { SetLocationPage } from "../set-location/set-location";

export class LocationPage {
	// Note : can be an object of class "Location" containing lat and lng attributes
	location: any= {
	    lat: 40.7624324,
	    lng: -73.9759827
	};
	locationIsSet = false;
	
	constructor(private modalCtrl: ModalController) {}
	
	// open map modal which permit to choose a position and get back chosen location
	onOpenMap() {
		const modal = this.modalCtrl.create(SetLocationPage, 
		{location: this.location, isSet: this.locationIsSet});
		modal.present();
		modal.onDismiss(data => {
			if(data){
				this.location = data.location;
				this.locationIsSet = true;
			}
		});
	}
	
	// Locate the current position 
	onLocate() {
		
	}
}
```


*View file (addPlace.html)*

```xml
<ion-content>
    <form #f="ngForm" (ngSubmit)="onSubmit(f)">
        <ion-list>
            <ion-item>
                <ion-label position="fixed">Title</ion-label>
                <ion-input type="text" name="title" ngModel required></ion-input>
            </ion-item>
            <ion-item>
                <ion-label position="floating">Description</ion-label>
                <ion-textarea name="description" ngModel required></ion-textarea>
            </ion-item>
        </ion-list>
        <ion-grid>
            <ion-row>
                <ion-col>
                    <ion-button expand="block" fill="outline" type="button" (click)="onLocate()">Locate me</ion-button>
                </ion-col>
                <ion-col>
                    <ion-button expand="block" fill="outline" type="button" (click)="onOpenMap()">Select on map</ion-button>
                </ion-col>
            </ion-row>
            <ion-row *ngIf="locationIsSet">
                <ion-col>
			<sebm-google-map [latitude]="location.lat" [longitude]="location.lng" [zoom]="16">
				<sebm-google-map-marker 
				[latitude]="location.lat" 
				[longitude]="location.lng">
				</sebm-google-map-marker>
			</sebm-google-map>
                </ion-col>
            </ion-row>
            <ion-row>
                <ion-col text-center>
                    <h5>Take a photo</h5>
                </ion-col>
            </ion-row>
            <ion-row>
                <ion-col>
                    <ion-button expand="block" fill="outline" type="button" (click)="onTakePhoto()">Take a photo</ion-button>
                </ion-col>
            </ion-row>
            <ion-row>
                <ion-col>
        <img [src]=""></img>
                </ion-col>
            </ion-row>
            <ion-row>
                <ion-col>
                    <ion-button color="secondary" expand="block" type="submit" 
		    [disabled]="!f.valid || !locationIsSet">Add this place</ion-button>
                </ion-col>
            </ion-row>
        </ion-grid>        
    </form>
</ion-content>
```

*Style file (addPlace.scss)*
```css
page-add-place {
	sebm-google-map {
		height: 250px;
	}
}
```

### Using native geolocation plugin
[Back to top](#geolocation) 

Now we have integrated a map on our application, we will use the native geolocation plugin to locate our current position and display it on the previous map.

So, first install cordova plugin :

```
$ ionic cordova plugin add cordova-plugin-geolocation
```

Then implement the *onLocate* function and add the import of *Geolocation* plugin. Note that we also add loading and toast components for more cool stuff.

*Controller file (addPlace.ts)*

```javascript

import { SetLocationPage } from "../set-location/set-location";
import { ModalController, LoadingController, ToastController } from "ionic-angular";
import { Geolocation } from "ionic-native";

export class LocationPage {
	// Note : can be an object of class "Location" containing lat and lng attributes
	location: any= {
	    lat: 40.7624324,
	    lng: -73.9759827
	};
	locationIsSet = false;
	
	constructor(private modalCtrl: ModalController, 
	private loadingCtrl: LoadingController, private toastCtrl: ToastController) {}
	
	// open map modal which permit to choose a position and get back chosen location
	onOpenMap() {
		...
	}
	
	// Locate the current position with native geolocation plugin
	onLocate() {
		// Initialize loader component
		const loader = this.loadingCtrl.create({
			content: 'Getting your location...'
		});
		loader.present();
		
		// Get the current position
		Geolocation.getCurrentPosition()
		.then(
			location => {
				loader.dismiss();
				this.location.lat = location.coords.latitude;
				this.location.lng = location.coords.longitude;
				this.locationIsSet = true;
			}
		)
		.catch(
			error => {
				console.log(error);
				loader.dismiss();
				const toast = this.toastCtrl.create({
					message: 'Could not get location, please pick it manually',
					duration: 2500
				});
				toast.present();
			}
		); 
	}
}
```

## Customize map markers
[back to top](#geolocation)    

[Official gogole map documentation](https://developers.google.com/maps/documentation/javascript/custom-markers)    

##Reverse geocoding
[back to top](#geolocation)   

[Reverse geocoding with ionic](http://masteringionic.com/blog/2017-06-13-geocodingreverse-geocoding-with-ionic-native/)    
