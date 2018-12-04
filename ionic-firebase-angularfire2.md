[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# AngularFire2

* [CRUD angularfire2](#crud-angularfire2)    
* [Demo example](#demo-example)    
* [Authentication](#authentication)     
* [Firebase rules](#firebase-rules)     
* [Add data with custom key](#add-data-with-custom-key)    
* [angularfire issue](#angularfire-issue)     
* [Querying on Firebase](#querying-on-firebase)    
* [Firebase cloud function](#firebase-cloud-function)    
* [Firebase hosting](#firebase-hosting)    

## CRUD angularfire2
[Back to top](#angularfire2) 

[link : Josh morony tutorial](https://www.joshmorony.com/building-a-crud-ionic-2-application-with-firebase-angularfire/)    
[link : AngularFire2 change log](https://github.com/angular/angularfire2/blob/master/docs/rtdb/querying-lists.md)    
[link : Firebase query tutorial](https://www.youtube.com/watch?v=sKFLI5FOOHs)    

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

#### Usage
[Back to top](#angularfire2) 

*Home.html*

```html
<ion-header>
  <ion-navbar>
    <ion-title>
        Nb item ({{(songs | async)?.length}})
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


#### Convert Observable<any[]> to array of any

It could be useful to work with ```any[]``` instead of ```Observable<any[]>```

Simply use the following code

```javascript
songs: Observable<any[]>;
songsArray: any[] = [];

onFetchData(){
    this.songs = this.dataService.fetchData();	// Simply get data from Firebase
    this.songs.subscribe(res => {
        this.songsArray = res as any[];
    },
    error => {
    });
}
```

Then you can use ```songsArray``` in your view file as usual.

#### Firebase rules configuration
[Back to top](#angularfire2) 

In order to improve the querying performance, it is recommanded to add *.indexOn* rule in your firebase rules like below :

```javascript
{
    "rules": {
        ".read": "auth != null",
        ".write": "auth != null",
        "songs": {
            ".indexOn": "title"
        }
     }
}
```

#### Filtering data
[Back to top](#angularfire2) 

Go further with filtering data on attribute :

```javascript
onFetchData(){
    // Querying data on title
    this.songsList = this.afDB.list('/songs', ref => ref.orderByChild('title').equalTo('<YOUR_TITLE>'));
    this.songs = this.songsList.valueChanges();
    return this.songs;
}
```

#### Ordering data
[Back to top](#angularfire2) 

After retieving your dataset, you can sort it with *.map* function like below 

```javascript
onFetchData(){
    // Querying data on title and sort by artist
    this.songsList = this.afDB.list('/songs', ref => ref.orderByChild('title').equalTo('<YOUR_TITLE>'));
    this.songs = this.songsList.valueChanges().map(items => items.sort(this.predicateBy("artist")));
    return this.songs;
}

 predicateBy(prop){
    return function(a,b){
      if( a[prop] > b[prop]){
          return 1;
      }else if( a[prop] < b[prop] ){
          return -1;
      }
      return 0;
    }
  }
```

#### Targeting sub-nodes
[Back to top](#angularfire2) 

The following example shows how to add data for the current logged user.

*Firebase Data structure*
```
+ songs
-+ guid (logged user)
---- song 1
---- song 2
-+ guid (other logged user)
---- song 1
```

```javascript
...
constructor(...){
    // Get the logged user	
    this.user = this.afAuth.auth.currentUser.uid;
}

addItem(){
    const newRef = this.afDB.database.ref('/songs/'+this.user).push({});
        
    newRef.set({
      id: newRef.key,
      title: title.toUpperCase()
    });    
}

fetchData(type){
    this.songsList = this.afDB.list('/songs/'+this.user, ref => ref.orderByChild('songType').equalTo(type));
    this.songs = this.songsList.valueChanges().map(items => items.sort(this.tool.predicateBy("title")));
    return this.songs;  
}
```

## Demo example
[Back to top](#angularfire2) 

*app.module.ts*
```javascript
import { AngularFireModule } from 'angularfire2';
import { AngularFireDatabaseModule } from 'angularfire2/database';
...

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
  </ion-navbar>
</ion-header>

<ion-content padding>
  <div *ngIf="bAuth === false">
    <ion-item>
      <ion-label>login</ion-label>
      <ion-input [(ngModel)]="login"></ion-input>
    </ion-item>
    <ion-item>
      <ion-label>password</ion-label>
      <ion-input [(ngModel)]="password" type="password"></ion-input>
    </ion-item>
    <br><br>
    <button ion-button (click)="onAuth()">CONNEXION</button>
  </div>
  <div *ngIf="bAuth === true">
    <ion-item>
      <ion-label>item guid</ion-label>
      <ion-input [(ngModel)]="guid"></ion-input>
    </ion-item>
    <br><br>
    <button ion-button (click)="onAddNode()" >Add new node</button>
    <button ion-button (click)="onDisconnect()" color="danger">Disconnect</button>
    <button ion-button (click)="onReadData()">Read node</button>
    <button ion-button (click)="onInsertNode()">Insert data in current node</button>
    <p>{{ res }}</p>
  </div>
</ion-content>

```

*home.ts*

```javascript
import { FirebaseServiceProvider } from './../../providers/firebase-service/firebase-service';
import { AuthProvider } from './../../providers/auth/auth';
import { Component } from '@angular/core';
import { NavController } from 'ionic-angular';
import { Observable } from 'rxjs/Observable';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {

  login: string = "";
  password: string = "";
  bAuth: boolean = false;
  guid: string = "";
  res;

  constructor(public navCtrl: NavController,
    public auth: AuthProvider,
    public fb: FirebaseServiceProvider) {
  }

  /**
   * Firebase Authentication
   */
  onAuth(){
    this.auth.loginUser(this.login, this.password)
    .then( authData => {
      // Auth success
      this.bAuth = true;
      this.res = "Success : " + authData.user.uid;
    }, error => {
      // Auth error

    });
  }

  onDisconnect(){
    this.auth.logoutUser()
      .then( authData => {
        // Auth success
        this.bAuth = false;
        this.res = "";
      }, error => {
        // Auth error

      });
  }

  /**
   * Add new node
   */
  onAddNode(){
    // JSON object to create in firebase
    let nodeData = {  
        guid : this.guid,
        customer_info : "TOTO",
        profiles : [ {
          profile_id : "3000",
          validity_date : ""
        }, {
          profile_id : "3001",
          validity_date : ""
        }, {
          profile_id : "3EE2",
          validity_date : ""
        }, {
          profile_id : "3003",
          validity_date : ""
        } ]
    };

    this.fb.addConfig(this.guid,nodeData);  
  }

  /**
   * Read selected node (guid) data
   */
  async onReadData(){
    
    this.fb.onGetDataWithPromise(this.guid)
    .then(data => {
      console.log("--- final obj --- " + JSON.stringify(data));
      this.res = "guid : " + data[0].guid + "\r\n";
      this.res += "customer_info : " + data[0].customer_info + "\r\n";
    })
    .catch(error => {
      this.res = "Error " + error.error;
    })
  }


  /**
   * Insert daat in the current node (guid)
   */
  onInsertNode(){
    // JSON object to insert into firebase
    let nodeData = {  
      deviceList : {
        devices : [ {
          productType : "iPhone",
          id : 0,
          name : "iPhone 6"
        }, {
          productType : "iPhone",
          id : 1,
          name : "iPhone 7"
        }, {
          productType : "iPhone",
          id : 2,
          name : "iPhone 8"
        }]
      }
    };

    this.fb.insertNode(this.guid,nodeData);  
  }
}
```

*firebase-service.ts*

```javascript
import { AuthProvider } from './../auth/auth';
import { Injectable } from '@angular/core';
import { AngularFireDatabase, AngularFireList } from 'angularfire2/database';
import { Observable } from 'rxjs/Observable';
import 'rxjs/Rx';

@Injectable()
export class FirebaseServiceProvider {

  dataList: AngularFireList<any>;
  data: Observable<any[]>;  
  resData: any[] = [];

  constructor(public afDB: AngularFireDatabase,
              public auth: AuthProvider) {
  }

  /**
   * Add new node into firebase
   * @param content : contenu json
   */
  addConfig(guid,content){
    // get authenticated user uid
    let uid = this.auth.onGetCurrentUser();

    // Get ref of the main firebase node where we want to add data
    var ref = this.afDB.database.ref('/rights/');

    // Add the sub-node in "uid/<custom guid>"
    ref.child(uid + "/" + guid).set(content)
    .then(() => {
      console.log("--- add data --- Ok");
      return "Add node success";
    })
    .catch((e) => {
      console.log("--- add data --- Error");
      return "Import Error ! " + JSON.stringify(e, Object.getOwnPropertyNames(e));
    });
  }

  /**
   * Fetch data from /rights/<user_uid>/<custom guid>
   * @param guid 
   */
  onGetDataWithPromise(guid){
    let uid = this.auth.onGetCurrentUser(); // get authenticated user uid
    
    this.dataList = this.afDB.list('/rights/'+ uid);// +"/"+guid);  // fetch all sub-nodes of the current user

    return new Promise((resolve, reject) => {
      this.dataList.valueChanges().subscribe((res) => {      
        this.resData = res as any[];
        console.log("--- onGetData res ---" + JSON.stringify(this.resData));
        resolve(res);
      });
    });
  }

  /**
   * Insert data in specified node
   * @param content : contenu json
   */
  insertNode(guid,content){
    // get authenticated user uid
    let uid = this.auth.onGetCurrentUser();

    // Get ref of the main firebase node where we want to add data
    var ref = this.afDB.database.ref('/rights/');

    // Update sub-node data "uid/<custom guid>"
    ref.child(uid + "/" + guid).update(content)
    .then(() => {
      console.log("--- add data --- Ok");
      return "Add node success";
    })
    .catch((e) => {
      console.log("--- add data --- Error");
      return "Import Error ! " + JSON.stringify(e, Object.getOwnPropertyNames(e));
    });
  }
}


```

## Authentication
[Back to top](#angularfire2) 

[Personnal sample Github repos](https://github.com/gsoulie/ionic2-angularFire2-authentication)    


## Firebase rules
[Back to top](#angularfire2) 

[Reference](https://howtofirebase.com/firebase-security-rules-88d94606ce4a)    

Something important on firebase is to set optimized rules for your database. To speed up queries, firebase provide a set of rules that allows to set powerful *index* on specified columns.

Imagine the following data structure [tweets/user/tweet]:
```
{
    tweets: {
        user1_ID :{
	    tweet1_ID:{
	    	date:...
	    },
	    tweet2_ID:{
	    	date:...
	    }
	},
	user2_ID:{
	   tweet1_ID:{
	    	date:...
	    },
	    tweet2_ID:{
	    	date:...
	    },
	    tweet3_ID:{
	    	date:...
	    },
	    tweet4_ID:{
	    	date:...
	    }
	}
    }
```

To optimize the tweet querying, you can set your database rules as the following :

```javascript
{
"rules": {
   "tweets": {
      "$uid": {
        ".read": "$uid === auth.uid",
        ".write": "$uid === auth.uid",
        ".indexOn": "date"
       }       
  	}
  }
}

// Where $uid is the user generated ID
```

An other example 

```
{
    games: {
        game1_ID :{
	   title: "blablabla"
	},
	game2_ID :{
	   title: "blablabla"
	},
	game3_ID :{
	   title: "blablabla"
	},
	game4_ID :{
	   title: "blablabla"
	}
    }
}

// Rules
{
  "rules": {
    ".read": "auth != null",
    ".write": "auth != null",
    "games": {
      ".indexOn": "title"
    }
  }
}


```


## Add data with custom key
[Back to top](#angularfire2) 

When you use **push** function, firebase generate automatic unique uid like -Lz32d1q23d64d. In some case, you may need to use custom unique uid like guid, email, product number or other.

To doing this you need to use **child** function associated to **set** method instead of **add**/**push** methods that generate unique uid.

```javascript
var content = {
	"user":"me",
	"email":"email",
	...
};

addData(content){

    let uid = this.auth.onGetCurrentUser(); // ahits.auth refers to AngularFireAuth.auth.currentUser.uid
    
    // Get the main node reference
    let ref = this.afDB.database.ref('/parent-node/');
    
    // Insert data in /parent-node/<user-uid>/<my-custom-id>
    ref.child(uid + "/" + "my-custom-id").set(content)
    .then(() => {
      console.log("--- add data --- Ok");
    })
    .catch((e) => {
      console.log("--- add data --- Error");
    });
}
```

## AngularFire issue
[Back to top](#angularfire2) 

Since recent releases of AngularFire require rxjs 6. You can get the following error when you try to *list* elements of your database :

```
zone.js:192 Uncaught TypeError: Object(...) is not a function
    at SwitchMapSubscriber.eval [as project] (changes.js:7)
    at SwitchMapSubscriber._next (switchMap.js:91)
    at SwitchMapSubscriber.Subscriber.next (Subscriber.js:95)
    at RefCountSubscriber.Subscriber._next (Subscriber.js:131)
    at RefCountSubscriber.Subscriber.next (Subscriber.js:95)
    at Subject.next (Subject.js:56)
    at ConnectableSubscriber.Subscriber._next (Subscriber.js:131)
    at ConnectableSubscriber.Subscriber.next (Subscriber.js:95)
    at Notification.observe (Notification.js:32)
    at AsyncAction.DelaySubscriber.dispatch (delay.js:91)
```

To fix it, you must upgrade RxJs to v6 [RxJS v6 migration](https://github.com/ReactiveX/RxJS/blob/HEAD/MIGRATION.md#backwards-compatibility)    

If the error persists, run ```npm install rxjs@^6.0.0 --save``` command.

## Querying on Firebase
[Back to top](#angularfire2) 

Here's some help to querying more easily on firebase

[Firebase blog - part 1](https://firebase.googleblog.com/2013/10/queries-part-1-common-sql-queries.html)    
[Firebase blog - part 2](https://firebase.googleblog.com/2014/11/firebase-now-with-more-querying.html)     

#### Select a user by ID (WHERE id = x)

We'll start off with the basics and build from here. In Firebase queries, records are stored in a "path", which is simply a URL in the data hierarchy. In our sample data, we've stored our users at /user. So to retrieve record by it's id, we just append it to the URL:

```javascript
new Firebase('https://example-data-sql.firebaseio.com/user/1').once('value', function(snap) {
   console.log('I fetched a user!', snap.val());
});
```

#### Find a user by email address (WHERE email = x)

Selecting an ID is all good and fine. But what if I want to look up an account by something that's not already part of the URL path?

Well this is where ordered data becomes our friend. Since we know that email addresses will be a common lookup method, we can call *setPriority()* whenever we add a new record. Then we can use that priority to look them up later.

```javascript
new Firebase("https://examples-sql-queries.firebaseio.com/user")
    .startAt('kato@firebase.com')
    .endAt('kato@firebase.com')
    .once('value', function(snap) {
       console.log('accounts matching email address', snap.val())
});
```

#### Paginate through widgets (LIMIT 10 OFFSET 10)

Pagination for small, static data sets (less than 1MB) can be done entirely client side. For larger static data sets, things get a bit more challenging. Assuming we're writing append-only data, we can use our ordered data examples above and assign each message a page number or a unique incremental counter and then use *startAt()/endAt()*.

```javascript
// fetch page 2 of messages
new Firebase("https://examples-sql-queries.firebaseio.com/messages")
    .startAt(2) // assumes the priority is the page number
    .endAt(2)
    .once('value', function(snap) {
       console.log('messages in range', snap.val());
});
```

#### Join records using an id (FROM table1 JOIN table2 USING id)

Firebase is a real-time sync platform. It's built for speed and efficiency. You don't need to worry about creating extra references, and can listen to as many paths as you'd like to retrieve your data:

```javascript
var fb = new Firebase("https://examples-sql-queries.firebaseio.com/");
fb.child('user/123').once('value', function(userSnap) {
   fb.child('media/123').once('value', function(mediaSnap) {
       // extend function: https://gist.github.com/katowulf/6598238
       console.log( extend({}, userSnap.val(), mediaSnap.val()) );
   });
});
```

#### Join on realtime dataset

Join data on realtime database is not possible with Firebase. So you can doing your join like below :

*Database structure*
```
data
 |
 |-<auto UID>
     |- data: {...}
     |- datetime: "2018-11-01"
     |- tagID: "1"
     |- 
     
 |-<auto UID>
     |- data: {...}
     |- datetime: "2018-09-10"
     |- tagID: "2"
 ...
tags
 |
 |-<auto UID>
     |- name: "logical name 1"
     |- tagID:"1"
 |-<auto UID>
     |-name: "logical name 2"
     |- tagID: "2"
```

```
  dataList: AngularFireList<any>; // realtime datasource
  dataset: Observable<any[]>; // data to display
  searchTerm: string = '';  // text from searchbar
  nbData: number = 0; // data counter
  selectedTag: any;
  currentTag: any = {};
  tagAngularFireList: AngularFireList<any>; // tag list realtime datasource
  tagObservable: Observable<any[]>; // tag list
  tagList = []; // tag list to display
  
  constructor(public navCtrl: NavController, 
    public http: HttpClient,
    public navParams: NavParams,
    private dataService: DataProvider,
    public afDB: AngularFireDatabase, 
    private auth: AuthProvider,
    private alertCtrl: AlertController,
    private ref: ChangeDetectorRef,
    private toastCtrl: ToastController,
    private tools: ToolsProvider) {
      // Get tag list
      this.onFetchTagList().subscribe((res) => {
        this.tagList = res as any[];
        this.tagList.splice(0,0,{id:"0",mac:"",name:"ALL TAGS",tagID:null});
      });
      // Loading data
      this.onFetchData();      
  }

  /**
   * Get tag list with custom tag name
   */
  onFetchTagList(){
    this.tagAngularFireList = this.afDB.list("tags");
    this.tagObservable = this.tagAngularFireList.valueChanges();
    return this.tagObservable;
  }

  /**
   * Get data from firebase
   */
  onFetchData(tagID: string = ""){
    var dateOffset = (24*60*60*1000) * 1; //offset 1 days
    let currentDate = new Date();
    currentDate.setTime(currentDate.getTime() - dateOffset);
    let dateToString = currentDate.getFullYear()+"-"+("0"+(currentDate.getMonth()+1)).slice(-2)+"-"+("0"+(currentDate.getDate())).slice(-2);	// retrieve data from the last 24h 
    
    // Fetch all data until yesterday
    this.dataList = this.afDB.list("data",ref => ref.orderByChild('datetime').startAt(dateToString));      
    this.dataset = this.dataList.valueChanges().map(items => items.sort(this.tools.predicateBy("datetime")).reverse().filter(item => {
          item['name'] = this.getTagLogicalName(item.tagID);
          item['tagRef'] = this.getTagRef(item.tagID);
          return item;
    }));
 }
 
  
 /**
  * Retrieve tag's logical name by tagID
  * @param tagID 
  */
 getTagLogicalName(tagID){
    let logicalName = "";
    let tagJoin = this.tagList.find(tag => tag.tagID == tagID);

    if(tagJoin){
      logicalName = tagJoin.name;      
    } else {
      logicalName = tagID;
    }
    return logicalName;
  }
 /**
  * Retrieve tag's firebase ID auto (table TAGS)
  * @param tagID 
  */
 getTagRef(tagID){
    let tagRef = "";
    let tagJoin = this.tagList.find(tag => tag.tagID == tagID);

    if(tagJoin){
      tagRef = tagJoin.id      
    } else {
      tagRef = null
    }
    return tagRef;
  }
```

## Firebase cloud function
[Back to top](#angularfire2) 

[link : firebase official tutorial](https://firebase.google.com/docs/functions/get-started)    
[link : firebase cloud functions](https://stackoverflow.com/questions/32004582/delete-firebase-data-older-than-2-hours)    
[link : firebase deploy $RESSOURCE_DIR issue](https://github.com/firebase/firebase-tools/issues/610)     
[link : cloud function tutorial](https://medium.com/@ales.musto/firebase-cloud-functions-that-run-on-a-set-schedule-b5d62cbbc476)    

The first step to create a firebase cloud function is to initiate a nodejs project by running 
```
npm install -g firebase-tools
```

Then run
```
firebase login
```

And finally go to your firebase project directory and run

```
firebase init functions
```

Then following the steps in the official firebase tutorial.

### Example of function

In this example, we need a function which clean all data in our firebase database where *datetime* attribute is older than the last 24h. This function will be triggerred when create data into our *data* table

*Sample of our database*
```
data
 |
 |-<auto UID>
     |- data: {...}
     |- datetime: "2018-11-01"
     |- name: "today's data"
     
 |-<auto UID>
     |- data: {...}
     |- datetime: "2018-09-10"
     |- name:"old data"
 ...
```

Considering we are in november 1st, the goal is to remove all data before october 31st.

The function below, do that job !

```
const functions = require('firebase-functions');

// The Firebase Admin SDK to access the Firebase Realtime Database.
const admin = require('firebase-admin');
admin.initializeApp();

exports.deleteOldItems = functions.database.ref('/data/{pushId}')
  .onCreate((change, context) => {
    var ref = change.after.ref.parent;//('/data/'); // reference to the items
    
    var dateOffset = (24*60*60*1000) * 1; //offset 1 days
    let currentDate = new Date();	// current date
    currentDate.setTime(currentDate.getTime() - dateOffset);	// change current date by adding offset
    let dateToString = currentDate.getFullYear()+"-"+("0"+(currentDate.getMonth()+1)).slice(-2)+"-"+("0"+(currentDate.getDate())).slice(-2);	// need to convert date to string. If not, the following .endAt function will not work
  
  
    var oldItemsQuery = ref.orderByChild('datetime').endAt(dateToString);
    return oldItemsQuery.once('value', function(snapshot) {
      // create a map with all children that need to be removed
      var updates = {};
      snapshot.forEach(function(child) {
        updates[child.key] = null
      });
      // execute all updates in one go and return the result to end the function
      return ref.update(updates);
    });
});
```

To finish, just run firebase deploy command ```firebase deploy --only functions```

### Using the same function with Https trigger

```
exports.deleteOldItemsHttpTEST = functions.https.onRequest((req, res) =>{
  var dbRef = admin.database().ref('/data/');

  // Manage CORS ISSUE
  res.set('Access-Control-Allow-Origin', '*');
  if (req.method === 'OPTIONS') {
    // Send response to OPTIONS requests
    res.set('Access-Control-Allow-Methods', 'GET');
    res.set('Access-Control-Allow-Headers', 'Content-Type');
    res.set('Access-Control-Max-Age', '3600');
    res.status(204).send('');
  } else {
    // Set CORS headers for the main request
    res.set('Access-Control-Allow-Origin', '*');
    res.send('Hello World!');
 
    var dateOffset = (24*60*60*1000) * 1; //offset 1 days
    let currentDate = new Date();
    currentDate.setTime(currentDate.getTime() - dateOffset);
    let dateToString = currentDate.getFullYear()+"-"+("0"+(currentDate.getMonth()+1)).slice(-2)+"-"+("0"+(currentDate.getDate())).slice(-2);
  
    var oldItemsQuery = dbRef.orderByChild('datetime').endAt(dateToString);
    oldItemsQuery.once('value', function(snapshot) {
      // create a map with all children that need to be removed
      var updates = {};
      snapshot.forEach(function(child) {
        updates[child.key] = null
      });
      // execute all updates in one go and return the result to end the function
      dbRef.update(updates);
    });
    res.redirect(200);
  }

});
```

### CORS issue

[link: official cloud function documentation](https://cloud.google.com/functions/docs/writing/http)

## Firebase hosting

Firebase hosting is a service provided by Firebase, which permit to host your PWA.

The first thing you need to do is to **create a new project in the Firebase console**, then navigate in the Hosting option and click on **Get started**. You will be promped to install *firebase-tool* 

Run the commands below into your PWA folder
```
npm install -g firebase-tools
```

And follow the steps below :

```
firebase login

firebase init
--> select "hosting" option and make sure to hit SPACE then ENTER !!

What do you want to use as your public directory ?
--> platforms/browser/www

Configure as a single-page app ?
--> Yes

File www/index.html already exists. Overwrite ?
--> No

// Build your application
ionic cordova build browser --prod

firebase deploy
```

