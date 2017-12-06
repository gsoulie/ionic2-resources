# Backends
## Strongloop
[Back to top](#ionic-2) 

[link : Strongloop and bluemix](http://www.raymondcamden.com/2015/10/29/strongloop-ionic-and-ibm-bluemix/)    

### Network app sample

[link : ionic 2 oauth social with firebase](http://www.gajotres.net/ionic-2-succesfull-oauth-social-login-with-firebase/)
[link : ionic framework and firebase](https://www.toptal.com/front-end/building-multi-platform-real-time-mobile-applications-using-ionic-framework-and-firebase)

**cordova plugin installation**

```
cordova plugin add cordova-plugin-whitelist
cordova plugin add cordova-plugin-inappbrowser@1.1.0
```

*app.html configuration*

```html
<meta http-equiv="Content-Security-Policy" content="default-src *; script-src 'self' 'unsafe-inline' 'unsafe-eval' *; style-src  'self' 'unsafe-inline' *">
```

## Firebase with angularfire2
[Back to top](#ionic-2) 

[link : Josh morony tutorial](https://www.joshmorony.com/building-a-crud-ionic-2-application-with-firebase-angularfire/)

**Warning** The original tutorial is not updated with AngularFire 5.0, so if you are running AngularFire 5.0.x use my code below which is updated according to the angularfire2 v5.0 change log.

#### Plugin installation

First, install angularfire2 plugin ```npm install angularfire2 firebase```

#### Module declaration

After you created your Firebase project, retieve your Firebase project credentials and create a constant in the *app.module.ts* to store them

*app.module.ts*
```javascript
import { BrowserModule } from '@angular/platform-browser';
import { ErrorHandler, NgModule } from '@angular/core';
import { IonicApp, IonicErrorHandler, IonicModule } from 'ionic-angular';
import { SplashScreen } from '@ionic-native/splash-screen';
import { StatusBar } from '@ionic-native/status-bar';
import { AngularFireModule } from 'angularfire2';
import { AngularFireDatabaseModule } from 'angularfire2/database';
import { MyApp } from './app.component';
import { HomePage } from '../pages/home/home';

// AngularFire2 account settings
export const firebaseConfig = {
  apiKey: "AIzaXXXXXXXXXXXXXXXXX",
  authDomain: "xxxxxxx.firebaseapp.com",
  databaseURL: "https://xxxxxxx.firebaseio.com",
  projectId: "xxxxxxxx",
  storageBucket: "xxxxxxxx.appspot.com",
  messagingSenderId: "XXXXXXXXXX"
};

@NgModule({
  declarations: [
    MyApp,
    HomePage
  ],
  imports: [
    BrowserModule,
    IonicModule.forRoot(MyApp),
    AngularFireModule.initializeApp(firebaseConfig),	// add declaration here
    AngularFireDatabaseModule	// add declaration here
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp,
    HomePage
  ],
  providers: [
    StatusBar,
    SplashScreen,
    {provide: ErrorHandler, useClass: IonicErrorHandler}
  ]
})
export class AppModule {}
```

*Home.html*

```html
<ion-header>
  <ion-navbar>
    <ion-title>
      Ionic Blank
    </ion-title>
    <ion-buttons end>
      <button ion-button icon-only (click)="addSong()">
        <ion-icon name="add"></ion-icon>
      </button>
    </ion-buttons>
  </ion-navbar>
</ion-header>

<ion-content padding>
  <ion-list>
    <ion-item *ngFor="let song of songs | async" (click)="showOptions(song.id, song.title)">
      {{song.title}}
    </ion-item>
  </ion-list>
</ion-content>
```

That click handler is calling our function *showOptions()* and passing the song’s *title* and *id* (this is going to display an action sheet).
It’s also using the *async* pipe, because AngularFire2 returns Observables, which means it’s going to be retrieving the list items and sending them to the view as soon as they become available.

*Home.ts*

```javascript
import { Component } from '@angular/core';
import { NavController, AlertController } from 'ionic-angular';
import { AngularFireDatabase, AngularFireList } from 'angularfire2/database';
import { Observable } from 'rxjs/Observable';
import { ActionSheetController } from 'ionic-angular/components/action-sheet/action-sheet-controller';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
  songsList: AngularFireList<any>;
  songs: Observable<any[]>;
  
  constructor(public navCtrl: NavController,
              public afDB: AngularFireDatabase,
              public alertCtrl: AlertController,
              public actionSheetCtrl: ActionSheetController) {
    // Initialize data                
    this.songsList = this.afDB.list('/songs');
    this.songs = this.songsList.valueChanges();
  }

  /**
   * Add new item
   */
  addSong(){
    let prompt = this.alertCtrl.create({
      title: 'Song Name',
      message: "Enter a name for this new song you're so keen on adding",
      inputs: [
        {
          name: 'title',
          placeholder: 'Title'
        },
      ],
      buttons: [
        {
          text: 'Cancel',
          handler: data => {
            console.log('Cancel clicked');
          }
        },
        {
          text: 'Save',
          handler: data => {
	    // CREATION CODE HERE
            const newSongRef = this.songsList.push({});
               
            newSongRef.set({
              id: newSongRef.key,
              title: data.title
            });
          }
        }
      ]
    });
    prompt.present();
  }

  /**
   * Remove selected song
   * @param songId 
   */
  removeSong(songId){
    this.songsList.remove(songId);
  }

  /**
   * Update selected song
   * @param songId 
   * @param songTitle 
   */
  updateSong(songId, songTitle){
    let prompt = this.alertCtrl.create({
      title: 'Song Name',
      message: "Update the name for this song",
      inputs: [
        {
          name: 'title',
          placeholder: 'Title',
          value: songTitle
        },
      ],
      buttons: [
        {
          text: 'Cancel',
          handler: data => {
            console.log('Cancel clicked');
          }
        },
        {
          text: 'Save',
          handler: data => {
	    // UPDATE CODE HERE
            this.songsList.update(songId, {
              title: data.title
            });
          }
        }
      ]
    });
    prompt.present();
  }

  /**
   * Action sheet
   * @param songId 
   * @param songTitle 
   */
  showOptions(songId, songTitle) {
    let actionSheet = this.actionSheetCtrl.create({
      title: 'What do you want to do?',
      buttons: [
        {
          text: 'Delete Song',
          role: 'destructive',
          handler: () => {
            this.removeSong(songId);
          }
        },{
          text: 'Update title',
          handler: () => {
            this.updateSong(songId, songTitle);
          }
        },{
          text: 'Cancel',
          role: 'cancel',
          handler: () => {
            console.log('Cancel clicked');
          }
        }
      ]
    });
    actionSheet.present();
  }
}

```


## Firebase
[Back to top](#ionic-2) 

### Install firebase

```
npm install --save firebase
```

Next, got to firebase and create a new app, then got to *Authentication* menu and add a new authentication method (for example Email/Password in our case). This, will generate a JSON object wich contain authentication string. Just copy the *apiKey* and *authDomain* strings and paste them in your *app.component.ts* like below :

*app.component.ts*

```javascript
...

export class MyApp {

	tabsPage = TabsPage;
	signingPage = SigninPage;
	signupPage = SignupPage;
	@ViewChild('nav') nav: NavController; //if you are using a side menu
	isAuthenticated = false;

	constructor(...) {
		firebase.initializeApp({apiKey: "<YOUR API KEY HERE>",authDomain: "<YOUR AUTH DOMAIN HERE>"});

		firebase.auth().onAuthStateChanged(user => {
			if(user) {
				this.isAuthenticated = true;
				this.rootPage = TabsPage;	// redirecting on the good page
			} else {
				this.isAuthenticated = false;
				this.rootPage = SiningPage;	// redirecting on the good page
			}
		});
	}

	// logout function
	onLogout(){
		this.authService.logOut();
		this.menuCtrl.close();
		this.nav.setRoot(SigninPage);
	}
}

```

### Authentication with firebase

Next, you need to create a new provider for firebase interaction. (Do not forget to add your new service to the *app.module.ts* in the *provider* section

*auth.ts*

```javascript
import firebase from 'firebase';
...

export class AuthService {
    signup(email: string, password: string){
        return firebase.auth().createUserWithEmailAndPassword(email, password);
    }

    signin(email: string, password: string){
        return firebase.auth().signInWithEmailAndPassword(email, password);
    }

    logout() {
	    firebase.auth().signOut();
    }

    getActiveUser(){
        return firebase.auth().currentUser;
    }
}

```
Create signin and signup forms

*signinForm.html* same as *signupForm.html*

```html
...
<ion-content>
    <form #f="ngForm" (ngSubmit)="onSignin(f)">
        <ion-list>
            <ion-item>
                <ion-label fixed>Mail</ion-label>
                <ion-input type="email" ngModel name="email" required></ion-input>
            </ion-item>
            <ion-item>
                <ion-label fixed>password</ion-label>
                <ion-input type="password" ngModel name="password" required></ion-input>
            </ion-item>
        </ion-list>
        <button ion-button block type="submit" [disabled]="!f.valid">Signin</button>
    </form>  
</ion-content>
```

*signinForm.ts*

```javascript
import { NgForm } from '@angular/forms';
import { AuthService } from '../providers/auth';
import { LoadingController, AlertController } from 'ionic-angular';

export class SigninPage {

    constructor(private authService: AuthService, 
		private loadingCtrl: LoadingController,
		private alertCtrl: AlertController){}

    // Signing method
    onSignin(form: NgForm){
        const loading = this.loadingCtrl.create({
		content: ‘signin you up…’
	});
	loading.present();
        
	this.authService.signin(form.value.email, form.value.password)
		.then(data => {
			loading.dismiss();
		})
		.catch(error => {
			loading.dismiss();
			const alert = this.alertCtrl.create({
			title: ‘signin failed’,
			message: error.message
			buttons: ['Ok']
		});

		alert.present();
    	});
    }
}

```

*signupForm.ts*

```javascript
import { NgForm } from '@angular/forms';
import { AuthService } from '../providers/auth';
import { LoadingController, AlertController } from 'ionic-angular';

export class SigninPage {

    constructor(private authService: AuthService, 
		private loadingCtrl: LoadingController,
		private alertCtrl: AlertController){}
    
    // Signup method
    onSignup(form: NgForm){
        const loading = this.loadingCtrl.create({
		content: ‘signup you up…’
	});
	loading.present();
        
	this.authService.signup(form.value.email, form.value.password)
		.then(data => {
			loading.dismiss();
		})
		.catch(error => {
			loading.dismiss();
			const alert = this.alertCtrl.create({
			title: ‘signup failed’,
			message: error.message
			buttons: ['Ok']
		});

		alert.present();
    	});
    }
}

```


### Manipulate data with firebase

#### Save data

Note : Firebase is using JWT

Consider we have a *detail* page which permit to create new items.

*detail.ts*

```javascript

import { AuthService } from '../../providers/auth';
import { DataService } from '../../providers/dataservice';
...

export class ListPage {
	constructor(private dataService: DataService...){}
	
	// save data to firebase
	saveItem(){
		// Get active user token
		this.authService.getActiveUser().getToken()
		.then(
			(token: string) => {
				this.dataService.storeData(token)
				.subscribe(
					() => console.log("success"),
					error => {
						this.handleError(error.json().error);
					}
				);
			}
		);
	}
}
```
### Fetch data

Consider that we have a *list* page which display data

*list.ts*

```javascript
import { LoadingController, AlertController } from 'ionic-angular';
import { AuthService } from '../../providers/auth';
import { DataService } from '../../providers/dataservice';
import { Game } from "../../models/game";
...

export class ListPage {
	listItems: Game[];
	
	constructor(private dataService: DataService, private authService: AuthService, private loadingCtrl: LoadingController, alertCtrl: AlertController...){}
	
	// save data to firebase
	getItems(){
		const loading = this.loadingCtrl.create({content: 'please wait...'});
		loading.present();
		// Get active user token
		this.authService.getActiveUser().getToken()
		.then(
			(token: string) => {
				this.dataService.fetchData(token)
				.subscribe(
					(list: Game[]) => {
						loading.dismiss();
						if(list){
							this.listItems = list;
						} else {
							this.listItems = [];
						}
					},
					error => 
						loading.dismiss();
						this.handleError(error.json().error);
					}
				);
			}
		);
	}
	
	// Handle errors
	handleError(errorMessage: string) {
		const alert = this.alertCtrl.create({
			title: 'An error occured',
			message: errorMessage,
			buttons: ['Ok']
		});
		alert.present();
	}
}
```

### Database provider

Create a provider wich doing all database operation

*dataService.ts*

```javascript
import { ... }
import { Http, Response } from '@angular/http';
import { AuthService } from './auth';
import 'rxjs/Rx';
import { Game } from '../models/game';

@Injectable()
export class DataService {
	private itemList: Game[] = [];

	constructor(private http: Http, private authService: AuthService){}
	addItem(name: string){ this.itemList.push(new Game(name)); }
	deleteItem(index: number){ this.itemList.splice(index, 1); }
	getItems(){ 
		return this.itemList.slice(); // Important : slice() return a copy of the object
	}
	
	// Save data to firebase
	storeData(token: string) {
		// get the user Id 		
		const userId = this.authService.getActiveUser().uid;
		
		// url is available in firebase --> Database menu --> copy link
		// here we create a game-list.json in the db
		return this.http.put('https://mygames-8c67c.firebaseio.com/' + userId + '/game-list.json?auth=' + token
			, this.itemList)
		.map((response: Response) => {
			return response.json();
		});
	}
	
	// Fetching firebase data
	fetchData(token: string){
		// get the user Id 		
		const userId = this.authService.getActiveUser().uid;
	
		return this.http.put('https://mygames-8c67c.firebaseio.com/' + userId + '/game-list.json?auth=' + token)
		.map((response: Response) => {
			const game: Game[] = response.json() ? response.json() : [];
			for(let item of game) {
				if(!item.hasOwnProperty('category')) {
					item.category = [];
				}
			}
			return  game;//response.json();
		})
		.do((data) => {
			this.itemList = data 
		});
	}
}
```

*model game.ts*

```javascript
export class Game {
	constructor(public name: string, .....) {}
}
```
