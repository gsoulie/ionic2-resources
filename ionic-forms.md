[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Forms

* [NgModel Forms](#ngmodel-forms)    
* [Form in Ionic 4](#form-in-ionic-4)     

## NgModel Forms
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

## Form in ionic 4
[Back to top](#forms)    

Ionic 4 makes changes with FormGroup

First, you need to import FormsModule and ReactiveFormsModule in your *app.module.ts* file
*app.module.ts*

```
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { RouteReuseStrategy } from '@angular/router';

import { IonicModule, IonicRouteStrategy } from '@ionic/angular';
import { SplashScreen } from '@ionic-native/splash-screen/ngx';
import { StatusBar } from '@ionic-native/status-bar/ngx';
import { IonicStorageModule } from '@ionic/storage';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { ReactiveFormsModule, FormsModule} from '@angular/forms';

@NgModule({
  declarations: [AppComponent],
  entryComponents: [],
  imports: [
    BrowserModule, IonicModule.forRoot(), AppRoutingModule,
    IonicStorageModule.forRoot(),
    FormsModule,
    ReactiveFormsModule
  ],
  providers: [
    StatusBar,
    SplashScreen,
    { provide: RouteReuseStrategy, useClass: IonicRouteStrategy }
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}

```

*view file of your form page*
```
<!-- if[UserInterface]-->
<ion-content no-padding class="window">
    <form [formGroup]="loginForm" (submit)="loginUser()" novalidate>
      <ion-item class="formItem">
        <ion-input #email formControlName="email" type="email" aria-labelledby="lbl-email" placeholder="Your email address"
          [class.invalid]="!loginForm.controls.email.valid">
        </ion-input>
      </ion-item>
      <ion-item class="formItem">
        <ion-input #password formControlName="password" type="password" aria-labelledby="lbl-password" placeholder="Your password"
            [class.invalid]="!loginForm.controls.password.valid">
        </ion-input>
      </ion-item>
      <ion-button fill="clear" class="formSubmitButton" type="submit">Login</ion-button>
    </form>
</ion-content>
```
you can also import FormsModule and ReactiveFormsModule to yourFormPage.page.ts

```
import { FormGroup, FormArray, FormBuilder, Validators,ReactiveFormsModule } from '@angular/forms';
```

Then you must import FormsModule and ReactiveFormsModule to *yourFormPage.module.ts* file

*yourFormPage.module.ts
```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { Routes, RouterModule } from '@angular/router';
import { FormGroup, FormArray, FormBuilder, Validators,ReactiveFormsModule } from '@angular/forms';
import { IonicModule } from '@ionic/angular';

import { LoginPage } from './login.page';

const routes: Routes = [
  {
    path: '',
    component: LoginPage
  }
];

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    ReactiveFormsModule,
    IonicModule,
    RouterModule.forChild(routes)
  ],
  declarations: [LoginPage]
})
export class LoginPageModule {}

```
