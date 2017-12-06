# Forms
[Back to top](#ionic-2) 

Here is a basic form sample

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
                    <button ion-button block outline type="button" (click)="onLocate()">Locate me</button>
                </ion-col>
                <ion-col>
                    <button ion-button block outline type="button" (click)="onOpenMap()">Select on map</button>
                </ion-col>
            </ion-row>
            <ion-row>
                <ion-col>
                    <p>Selected place...</p>
                </ion-col>
            </ion-row>
            <ion-row>
                <ion-col text-center>
                    <h5>Take a photo</h5>
                </ion-col>
            </ion-row>
            <ion-row>
                <ion-col>
                    <button ion-button block outline type="button" (click)="onTakePhoto()">Take a photo</button>
                </ion-col>
            </ion-row>
            <ion-row>
                <ion-col>
		<img [src]=""></img>
                </ion-col>
            </ion-row>
            <ion-row>
                <ion-col>
                    <button ion-button color="secondary" block type="submit" [disabled]="!f.valid">Add this place</button>
                </ion-col>
            </ion-row>
        </ion-grid>        
    </form>
</ion-content>
```
**note** : 
- ```type="button"``` prevent the form submition when you click on the button 
- ```type="submit"``` trigger the form submition


*Controller file (addPlace.ts)*

```javascript
import { NgForm } from "@angular/forms";
import { AddPlacePage } from "../add-place/add-place";
import { Place } from "../../models/place";
import { PlacesService } from "../../providers/places";

@Component({
	selector: "page-home",
	templateUrl: "home.html"
})
export class HomePage {
	addPlacePage = AddPlacePage;
	places: Place [] = [];
	imageUrl = "";
	location: {lat: 40.564654, lng: -73.654754};
	
	constructor() {public navCtrl: NavController, private placesService: PlacesService}
		
	onLocate(...) { this.location = ... }	// See geolocation section for detail
	
	onTakePhoto(...) { this.imageUrl = "..."; }	// See Camera section for detail
	
	onSubmit(form: NgForm) {
		// submit data
		this.placesService.addPlace(form.value.title, form.value.description, this.location, this.imageUrl);
		form.reset();
		
		this.location = {lat: 40.564654, lng: -73.654754} // initial location
		this.imageUrl = "";
	}
}
```

*Controller file (home.ts)*

```javascript
import { AddPlacePage } from "../add-place/add-place";
import { Place } from "../../models/place";
import { PlacesService } from "../../providers/places";

@Component({
	selector: "page-home",
	templateUrl: "home.html"
})
export class HomePage {
	addPlacePage = AddPlacePage;
	places: Place [] = [];
		
	constructor() {public navCtrl: NavController, private placesService: PlacesService}
	
	// Load data on page opening
	ionViewWillEnter() {
		this.places = this.placesService.loadPlaces();
	}
}
```

Now to store data, we are going to create a new service (**don't forget to add it in the app.module.ts file in provider section**)

*Service file (places.ts)*

```javascript
import { Place } from "../models/place";
import { Location } from "../models/location";

export class PlacesServices {
	private places: Place[] = [];
	
	addPlace(title: string, description: string, location: Location, imageUrl: string){
		const place = new Place(title, description, location, imageUrl);
		this.places.push(place);
	}
	
	loadPlaces(){
		return this.places.slice();	// Important : slice() return a copy of the object
	}
}
```

Displaying data in the *home* page

*View file (home.html)*

```xml
<ion-content>
	<ion-card *ngFor="let place of places" (click)="openPlace(place)">
		<img [src]="place.imageUrl"></img>
		<ion-card-content>
			<ion-card-title>{{place.title}}</ion-card-title>
			<p>{{place.description}}</p>
		</ion-card-content>
	</ion-card>
</ion-content>
```