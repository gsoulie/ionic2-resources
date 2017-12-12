
# AngularFire2


* [Backends](https://github.com/gsoulie/ionic2-resources/blob/master/ionic-firebase-angularfire2.md)    
	* [CRUD angularfire2](#crud-angularfire2)    
	* [Authentication](#authentication)  

## CRUD angularfire2
[Back to top](#angularfire2) 

[link : Josh morony tutorial](https://www.joshmorony.com/building-a-crud-ionic-2-application-with-firebase-angularfire/)

[USING angularfire OFFLINE](https://github.com/adriancarriger/angularfire2-offline)    

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
    
    // Querying data on title
    /*this.songsList = this.afDB.list('/songs', ref => ref.orderByChild('title').equalTo('<YOUR_TITLE>'));
    this.songs = this.songsList.valueChanges();
    return this.songs;  */
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


## Authentication
[Back to top](#angularfire2) 

[Github repos](https://github.com/gsoulie/ionic2-angularFire2-authentication)    
