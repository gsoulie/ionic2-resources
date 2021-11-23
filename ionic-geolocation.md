[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Geolocation

* [Angular2 Google maps integration](angular2-google-maps-integration)    
* [Customize map markers](#customize-map-markers)    
* [Reverse geocoding](#reverse-geocoding)    
* [Geolocation on capacitor](#geolocation-on-capacitor)    
* [Using Google map navigation](#using-google-map-navigation)      
* [Alternative to Google Map with leaflet - mapbox](#alternative-to-google-map)       

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

[Official google map documentation](https://developers.google.com/maps/documentation/javascript/custom-markers)    

## Reverse geocoding
[back to top](#geolocation)   

[Official documentation](https://ionicframework.com/docs/native/native-geocoder)     
[Reverse geocoding with ionic](http://masteringionic.com/blog/2017-06-13-geocodingreverse-geocoding-with-ionic-native/)    

*Capacitor installation*
````
npm install cordova-plugin-nativegeocoder
npm install @ionic-native/native-geocoder
ionic cap sync
````

*Usage*
````typescript
import { NativeGeocoder, NativeGeocoderResult, NativeGeocoderOptions } from '@ionic-native/native-geocoder/ngx';

constructor(private nativeGeocoder: NativeGeocoder) { }

let options: NativeGeocoderOptions = {
    useLocale: true,
    maxResults: 5
};

this.nativeGeocoder.reverseGeocode(52.5072095, 13.1452818, options)
  .then((result: NativeGeocoderResult[]) => console.log(JSON.stringify(result[0])))
  .catch((error: any) => console.log(error));

this.nativeGeocoder.forwardGeocode('Berlin', options)
  .then((result: NativeGeocoderResult[]) => console.log('The coordinates are latitude=' + result[0].latitude + ' and longitude=' + result[0].longitude))
  .catch((error: any) => console.log(error));
````

## Geolocation on capacitor
[back to top](#geolocation)  
```typescript
import { Plugins } from '@capacitor/core';
const { Geolocation } = Plugins;

async getCurrentPosition() {
    const options = {
      enableHighAccuracy: true
    };
    const coordinates = await Geolocation.getCurrentPosition(options)
    .then((data) => {
      alert('location ok ' + JSON.stringify(data));
    })
    .catch(e => {
      // no location available. invite user to activate geolocation on is device
      alert('no location ? ' + JSON.stringify(e));
    });
  }
```

## Using google map navigation
[back to top](#geolocation)  

Using google map app to start navigating

[tutorial part 1](https://www.youtube.com/watch?v=gSaDDxLbKSs&ab_channel=CodeSwag)      
[tutorial part 2 - navigation](https://www.youtube.com/watch?v=N8jqxFj9als&ab_channel=CodeSwag)       



Add your Google Map Api Key to your *index.html*

````html
<script src="https://maps.googleapis.com/maps/api/js?key=<YOUR_GOOGLEMAP_API_KEY>" async defer></script>
````

*home.html*

````html
<ion-content>
	<div #map id="map"></div>
</ion-content>
````

*home.ts*

````typescript
import { ViewChild, ElementRef } from '@angular/core';

declare var google: any;

export class HomePage {

	map: any;
	@ViewChild('map', { read: ElementRef, static: false }) mapRef: ElementRef;
	infoWindows: any = [];
	markers: any = [{
		title: 'National art gallery',
		latitude: '-17.824991',
		longitude: '31.049295'
	},
	{
		title: 'Chop chop',
		latitude: '-17.819460',
		longitude: '31.053844'
	}];
	
	constructor() {}
	ionViewDidEnter() { this.showMap(); }
	
	showMap() {
		const location = new google.maps.LatLng(-17.824858, 31.053028);
		const options = {
			center: location,
			zoom: 15,
			disableDefaultUI: true
		}
		this.map = new google.maps.Map(this.mapRef.nativeElement, options);
		this.addMarkersToMap(this.markers);
	}
	
	addMarkersToMap(markers) {
		for (let marker of markers) {
			let position = new google.maps.LatLng(marker.latitude, marker.longitude);
			let mapMarker = new google.maps.Marker({
				position: position,
				title: marker.title,
				latitude: marker.latitude,
				longitude: marker.longitude
			});
			
			mapMarker.setMap(this.map);
			this.addInfoWindowToMarker(mapMarker);
		}
	}
	
	addInfoWindowToMarker(marker) {
		let infoWindowContent = `<div id="content">
		<h2 id="firstHeading" class="firstHeading">${marker.title}</h2>
		<p>latitude : ${marker.latitude}</p>
		<p>longitude : ${marker.longitude}</p>
		<ion-button>Navigate</ion-button>
		</div>`
	
		let infoWindow = new google.maps.infoWindow({content: infoWindowContent});
	
		marker.addListener('click', () => {
			this.closeAllInfoWindows();
			infoWindow.open(this.map, marker);
			
			// Open google navigation
			google.maps.event.addEventListenerOnce(infoWindow, 'domready', () => {
				document.getElementById('navigate').addEventListener('click', () => {
					window.open('https://wwww.google.com/maps/dir?api=1&destination=' + marker.Latitude + ',' + marker.longitude);
				});
			});
		});
		this.infoWindows.push(infoWindow);
	}

	closeAllInfoWindows() {
		for (let window of this.infoWindows) { window.close(); }
	}
}
````

*home.scss*

````css
#map {
	height: 100%;
}
````


## Alternative to Google map
[back to top](#geolocation)  

**Leaflet** is a javascript library which based on **Mapbox**

https://leafletjs.com/examples/quick-start/

