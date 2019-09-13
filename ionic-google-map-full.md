
[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Google Map integration (full example)

## Create Google Map API Key

The first thing to use Google Map is to create a Google Map API Key [get api key](https://developers.google.com/maps/documentation/javascript/get-api-key).

Click on the **Obtain Key** button or :
1. Access to the *Google API Console*
2. Create or select a project
3. Click to continue to activate API and other services
4. On the Credentials page, get your API key and define the restrictions of the key

**Important** You need to activate **Google Map Javascript API** and **Google Map Geocoding API**

## Add Google Map in ionic project

Next, configure your *index.html* file to include your Google Map API Key

*index.html*

```javascript
...
<script async defer src="https://maps.googleapis.com/maps/api/js?key=<API_KEY>" type="text/javascript"></script>
<script src="cordova.js"></script>
...
```

Then install cordova plugin geolocation

```
ionic cordova plugin add cordova-plugin-geolocation --variable GEOLOCATION_USAGE_DESCRIPTION="To locate you"
npm install --save @ionic-native/geolocation
```

## Create your Map

*map.html*

```html
<ion-header>
  <ion-navbar color="dark">
    <ion-title>map</ion-title>
  </ion-navbar>
</ion-header>

<ion-content no-padding>
  <div #map id='map'></div>
</ion-content>
```

*map.scss*

```
page-map {
    #map {
		height: 100%;
	}
}
```

*map.ts*

```javascript
import { Component,ViewChild,ElementRef } from '@angular/core';
import { NavController, NavParams } from 'ionic-angular';

declare var google; // !!! IMPORTANT

@Component({
  selector: 'page-map',
  templateUrl: 'map.html',
})
export class MapPage {

  @ViewChild('map', {static: false}) mapElement: ElementRef; // Get reference to map object
  map: any;
  
  // Testing data to add multiple markers
  coordList: any[] = [
    {content:"Point 1",coords: {lat:43.760296, lng:3.872627},icon:""},
    {content:"Point 2",coords: {lat:43.757405, lng:3.878161},icon:""},
    {content:"Point 3",coords: {lat:43.756780, lng:3.873867},icon:""},
    {content:"Point 4",coords: {lat:43.753777, lng:3.873063},icon:""}];

  constructor(public navCtrl: NavController, public navParams: NavParams) {
  }

  ionViewDidLoad() {
    this.onInitializeMap();
  }

  onInitializeMap(){
    let locationOptions = {timeout: 20000, enableHighAccuracy: true};
 
    navigator.geolocation.getCurrentPosition(
 
        (position) => {
 
            let options = {
              center: new google.maps.LatLng(position.coords.latitude, position.coords.longitude),
              zoom: 16,
              mapTypeId: google.maps.MapTypeId.ROADMAP
            }
 
            this.map = new google.maps.Map(document.getElementById("map"), options);

            // Set a maker on the current position
            let markerPos = {lat:position.coords.latitude,lng:position.coords.longitude} 
            this.addMarker(markerPos,"<h4>Groupe Isia!</h4><p>Développement logiciel</p>","./assets/imgs/isiapps_icon36.JPG");
            
            // Display markers list
            this.coordList.forEach(point => {
              this.addMarker(point.coords,point.content,point.icon);
            });
        },
 
        (error) => {
            console.log(error);
        }, locationOptions
    );
    
  }

   /**
   * Add a marker on the map
   * @param{any} position : position {lat:xxxxx,lng:xxxx}
   * @param{String} content : string with html syntax
   * @param{String} icon : path to custom icon
   */
  addMarker(position,content,icon){
    let marker = new google.maps.Marker({
      map: this.map,
      animation: google.maps.Animation.DROP,
      position: position,
      icon:icon
    });
   
    let infoWindow = new google.maps.InfoWindow({
      content: content
    });
   
    google.maps.event.addListener(marker, 'click', () => {
      infoWindow.open(this.map, marker);
    });
  }

}

```
