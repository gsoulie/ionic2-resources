[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Database

* [SQLite database](#sqlite-database)    
* [SQLStorage](#sqlstorage)   
* [PouchDB](#pouchdb)
	
## SQLite database

### Storage method

Storage is an easy way to store key/value pairs and JSON objects.

[documentation here](https://ionicframework.com/docs/v2/storage/)   

First, install *sqlite storage* plugin 

```
ionic cordova plugin add cordova-sqlite-storage
npm install --save @ionic-native/sqlite
```

Then add it in the imports list in *app.module.ts* file

```
import { IonicStorageModule } from '@ionic/storage';

@NgModule({
  declarations: [
    // ...
  ],
  imports: [      
    BrowserModule,
    IonicModule.forRoot(MyApp),
    IonicStorageModule.forRoot()
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    // ...
  ],
  providers: [
    // ...
  ]
})
export class AppModule {}
```

You can now use it as below

*Service file*

*@Injectable()* Do not forget this line !

```javascript
import { Storage } from "@ionic/storage";
import { Injectable } from "@angular/core";
import { Place } from "../models/place";
import { Location } from "../models/location";

@Injectable()   // IMPORTANT
export class PlacesServices {
    private places: Place[] = [];

    constructor(private storage: Storage) {}

    addPlace(title: string, description: string, location: Location, imageUrl: string){
        const place = new Place(title, description, location, imageUrl);
        this.places.push(place);    // local storage

        this.storage.set('places', this.places)    // db storage
        .then(/* nothing specially */)
        .catch(
            err => {
                this.places.splice(this.places.indexOf(place), 1);
            }
        );
    }

    // Fetch data from DB
    fetchPlaces() {
        return this.storage.get('places')
        .then(
            (places: Place[]) => {
                this.places = places != null ? places : []; // be careful to do not return null 
		return this.places;
            }
        )
        .catch(err => console.log(err);)
    }

    loadPlaces(){
        return this.places.slice(); // Important : slice() return a copy of the object
    }

    deletePlaces(index: number){
        this.places.splice(index, 1);
	
	this.storage.set('places', this.places) // Update de data in the storage with the new places array without deleted element
	.then(
		() => {
			// doing some stuff
		}
	)
	.catch(
		err => console.log(err);
	);
    }
}
```

Next, call your fetch method

*Controller file (home.ts)*

**note** : add implementation of *OnInit*

```javascript
import { NgForm } from "@angular/forms";
import { AddPlacePage } from "../add-place/add-place";
import { Place } from "../../models/place";
import { PlacesService } from "../../providers/places";

@Component({
    selector: "page-home",
    templateUrl: "home.html"
})
export class HomePage implements OnInit{
	addPlacePage = AddPlacePage;
	places: Place [] = [];
   
	constructor() {public navCtrl: NavController, private placesService: PlacesService}

	ngOnInit() {
		this.placesService.fetchPlaces()
		.then(
			(places: Place[]) => this.places = places
		);
	}
}
```

### SQLite method

[Back to top](#database)  

[link : Using Sqlite storage tutorial](https://devdactic.com/ionic-2-sqlstorage/)

There is two main solutions for data storage. The first one is the local HTML5 storage and the second is using SQLite database storage. HTML5 storage provide a key-value storage. This solution can be useful for small applications, but is not adapted for SQL querying. On the other hand, this solution is limitated to 10MB of data storage.

[link : using sqlite](https://www.thepolyglotdeveloper.com/2015/12/use-sqlite-in-ionic-2-instead-of-local-storage/)

This solution using the local device database. **You can make some complex SQL query** for retrieve data.

**Intallation**

After the project is created, intall cordova sqlite storage plugin

```
$ ionic cordova plugin add cordova-sqlite-storage
```

*Database initialization (app.js)*

```javascript
import {App, Platform, Storage, SqlStorage} from 'ionic-angular';
import {HomePage} from './pages/home/home';
import {Component} from '@angular/core';

@Component({
  templateUrl: 'build/app.html',
  config: {}
})
class MyApp {
  static get parameters() {
    return [[App], [Platform]];
  }

  constructor(app, platform) {
    // set up our app
    this.app = app;
    this.platform = platform;
    this.initializeApp();

    // make HelloIonicPage the root (or first) page
    this.rootPage = HomePage;
  }

  initializeApp() {
    this.platform.ready().then(() => {
        this.storage = new Storage(SqlStorage);
        this.storage.query('CREATE TABLE IF NOT EXISTS T_TASKS (id INTEGER PRIMARY KEY AUTOINCREMENT, title TEXT)').then((data) => {
            console.log("TABLE CREATED -> " + JSON.stringify(data.res));//alert("Table créée");
        }, (error) => {
            console.log("ERROR -> " + JSON.stringify(error.err));
        });
   
      if (window.StatusBar) {
        window.Statusbar.styleHex('#A81910');
      }
    });
  }
}
```

*Database manipulation (home.js)*

```javascript
import {App, Platform, Storage, SqlStorage} from 'ionic-angular';
import {Component} from '@angular/core';

@Component({
  templateUrl: 'build/pages/home/home.html'
})

export class HomePage {
    static get parameters() {
      return [[Platform]];
    }

    constructor(platform) {
            this.platform = platform;
            this.cards = [];
            this.platform.ready().then(() => {
                this.storage = new Storage(SqlStorage);
                this.refresh();
            })
        }
   
    add() {

        if(this.title !== ""){
            this.platform.ready().then(() => {
                this.storage.query("INSERT INTO T_TASKS (title) VALUES (?)",this.title).then((data) => {
                    console.log(JSON.stringify(data.res));
                }, (error) => {
                    console.log("ERROR -> " + JSON.stringify(error.err));
                });
            });

            refresh();
        }
    }

    refresh() {
        this.platform.ready().then(() => {
            this.storage.query("SELECT * FROM T_TASKS").then((data) => {
                this.cards = [];
                if(data.res.rows.length > 0) {
                    for(var i = 0; i < data.res.rows.length; i++) {
                        this.cards.push({title: data.res.rows.item(i).title});
                    }
                }
            }, (error) => {
                console.log("ERROR -> " + JSON.stringify(error.err));
            });
        });
    }
}
```
*Displaying data from database (home.html)*

```xml
<ion-header>
	<ion-navbar>
	    <ion-title>
	        Home
	    </ion-title>
	    <button ion-button clear (click)="refresh()">Refresh</button>
	    <button ion-button clear (click)="add()">Add</button>
	</ion-navbar>
</ion-header> 
<ion-content class="home">
    <ion-list>
        <ion-item *ngFor="let person of people">
            {{person.firstname}} {{person.lastname}}
        </ion-item>
    </ion-list>
</ion-content>
```
## SQLStorage
[Back to top](#database)  

Is recommanded using **SqlStorage**. 

This is the preferred storage engine, as data will be stored in appropriate app storage, unlike Local Storage which is treated differently by the OS.

**Usage**

```javascript
import {Injectable} from '@angular/core';
import {Http} from '@angular/http';
import {Storage, SqlStorage} from 'ionic/ionic';

@Injectable()
export class Record {
    constructor() {
        this.storage = new Storage(SqlStorage);
        this.storage.set('name','max');
        this.storage.set('lastname','dupont');
    }
    
    createDB(){
	this.storage.query('CREATE TABLE IF NOT EXISTS conso( \
	idConso INTEGER PRIMARY KEY AUTOINCREMENT,  \
	dateConso DATE, \
	tripMax INTEGER, \
	cout REAL, \
	qte REAL, \
	prix REAL, \
	year INTEGER, \
	month INTEGER, \
	dateCreation CHAR(30) \
	)');
    }
    
   addConso(_args: any){
	    
	this.storage.query("INSERT INTO conso (dateConso,tripMax,cout,qte,prix,year,month,dateCreation) VALUES (?,?,?,?,?,?,?,?)",[_args.dateConso, _args.tripMax, _args.cout, _args.qte, _args.prix, _args.year, _args.month, new Date()])
	.then((data) => {
		console.log(JSON.stringify(data.res));
	}, (error) => {
	        console.log("Erreur lors de l'ajout => " + JSON.stringify(error));
	});
  }
}

storage.get('name').then((name) => {

});

// Sql storage also exposes the full engine underneath
this.storage.query('insert into projects(name, data) values("Cool Project", "blah")');
this.storage.query('select * from projects').then((resp) => {})
```

## PouchDB
[Back to top](#database)  
First, install cordova sqlite-storage plugin

```
$ ionic cordova plugin add cordova-sqlite-storage
```

**TODO - implementing ionic 2 sample app**

#### Resources
[Back to top](#database)  
[Google chrome PouchDB inspector extension](https://chrome.google.com/webstore/detail/pouchdb-inspector/hbhhpaojmpfimakffndmpmpndcmonkfa?hl=en)    

[Ionic 2 - PouchDB tutorial](http://gonehybrid.com/how-to-use-pouchdb-sqlite-for-local-storage-in-ionic-2/)    
