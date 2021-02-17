[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Push notification

* [Firebase Cloud Messaging](#fcm-push-notification)    
* [Phonegap plugin push](#push-notification-with-phonegap-plugin-push-need-to-be-updated)    
* [OneSignal](#onesignal)       

## FCM push notification
[Full Devdactic tutorial](https://devdactic.com/push-notifications-ionic-capacitor/)    
[Official plugin documentation](https://github.com/fechanique/cordova-plugin-fcm)    
[tutorial 1](http://simonwillshire.com/blog/Ionic-2-FCM-Notifications/)    
[tutorial 2](https://www.djamware.com/post/58a1378480aca7386754130a/ionic-2-fcm-push-notification)    

#### 1 - Create Firebase application

Go to Firebase console and create a new project

#### 2 - fcm plugin installation

```
ionic cordova plugin add cordova-plugin-fcm
npm install --save @ionic-native/fcm
```
#### 3 - get google-services.json

In your firebase dashboard, go to *Settings* --> *project parameters* and download **google-services.json** file. Next, copy this file at the root of your project 

#### 4 - updating build.gradle

Update your */platforms/android/build.gradle* to reflecting the following code

```javascript
// Allow plugins to declare Maven dependencies via build-extras.gradle.
allprojects {
    repositories {
        mavenCentral();
        jcenter();
        maven { url "https://maven.google.com" }  // new line
    }
}
```
#### 5 - Declare FCM module

*app.module.ts*

```javascript
import { FCM } from '@ionic-native/fcm';


@NgModule({
  declarations: [
    MyApp,
    HomePage
  ],
  imports: [
    BrowserModule,
    IonicModule.forRoot(MyApp)
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp,
    HomePage
  ],
  providers: [
    StatusBar,
    SplashScreen,
    FCM,
    {provide: ErrorHandler, useClass: IonicErrorHandler}
  ]
})
export class AppModule {}
```

#### 6 - implement basic push notification reception

*app.component.ts*

```javascript
import { Component } from '@angular/core';
import { Platform } from 'ionic-angular';
import { StatusBar } from '@ionic-native/status-bar';
import { SplashScreen } from '@ionic-native/splash-screen';
import { FCM } from '@ionic-native/fcm';
import { HomePage } from '../pages/home/home';
@Component({
  templateUrl: 'app.html'
})
export class MyApp {
  rootPage:any = HomePage;

  constructor(platform: Platform, 
    statusBar: StatusBar, 
    splashScreen: SplashScreen,
    public fcm: FCM) {
    platform.ready().then(() => {
      // Okay, so the platform is ready and our plugins are available.
      // Here you can do any higher level native things you might need.
      statusBar.styleDefault();
      this.initFCM();
      splashScreen.hide();
    });
  }

  initFCM(){
    // Subscribe to all topics
    this.fcm.subscribeToTopic('all');
    
    this.fcm.getToken().then(token => {
      // save token if needed
      // backend.registerToken(token);
    });
    
    this.fcm.onNotification().subscribe(data => {
      alert('message received')
      if(data.wasTapped) {
       console.info("Received in background");
      } else {
       console.info("Received in foreground");
      };
    });
    this.fcm.onTokenRefresh().subscribe(token => {
      // backend.registerToken(token);
    });
    
    // You can also subscribe to a specific topic
    // this.fcm.subscribeToTopic('topicExample');
  }
}
```

> **Important** : The topics are created by the client through the ```subscribeToTopic()``` function. After client subscription, the topic is available in Firebase in Target --> Topic

Finally, run the application on a device

#### 7 - Send notification from Firebase

Go to your firebase console and open **Notifications** menu, then follow the instructions to send your first push notification. 

<p align="center">
<img src="https://github.com/gsoulie/ionic2-resources/blob/master/fcm_form.png" align="center" width="900">
</p>     

As shown as above, you can specify a set of key / value in the advanced options

**Note** You can also test your notifications with https://cordova-plugin-fcm.appspot.com/ service. You will find your GCM API Key in *Settings --> Cloud Messaging*

#### 8 - Going further

With this method, you will see that push notification doesn't display on the system tray when the app is foreground, but the push notification appears when the app is in background.

So, if you want to display the notification in the system tray when your app is foreground, you can use *local notification*

Next, by default, push notification sound is disable in the Firebase console. To activate it, you have advanced settings section in the notification create screen.

## Push notification with phonegap-plugin-push (need to be updated)
[Back to top](#push-notification) 

[link : working both platform tutorial](https://medium.com/@ankushaggarwal/push-notifications-in-ionic-2-658461108c59#.tqfzfx5dd)

First way, using push notification module, only works when app is opened

```javascript
this.platform.ready().then(() => {
  console.log('Platform ready');
  this.initPush();
}

initPush() {
  let push = Push.init({
      android: {
          senderID: "XXXXXX"
      },
      ios: {
          alert: "true",
          badge: true,
          sound: 'false'
      },
      windows: {}
  });

  push.on('registration', (data) => {
      console.log("REGISTRATION");
          localStorage.setItem('token', data.registrationId);
  });

  push.on('notification', (data) => {
      console.log("ONLY WORK'S WHEN APP IS OPEN")
  });

  push.on('error', (e) => {
      console.log(e.message);
  });
```

Using push notification when app is closed needed to use background service because ionic is a web application, so the javascipt file will only be triggered/fired when the application is opened.

**TODO** using ionic.io

[push notification ionic 2](http://ionicframework.com/docs/v2/native/Push/)    

- plugins installation
```
$ ionic add ionic-platform-web-client
$ ionic cordova plugin add phonegap-plugin-push
$ ionic io init
$ ionic config set dev_push true
```

- in ```webpack.config.js``` Add the following line :

```
module.exports = {
  entry: [
    path.resolve('bower_components/ionic-platform-web-client/dist/ionic.io.bundle'),
    ......
```

- in ```bower_components/ionic-platform-web-client/dist/ionic.io.bundle.js``` replace:

```"IONIC_SETTINGS_STRING_START";"IONIC_SETTINGS_STRING_END";``` with :

```javascript
  "IONIC_SETTINGS_STRING_START";
    var settings = {
            "app_id": "YOUR APP_ID",
            "api_key": "YOUR APP_KEY",
            "gcm_key": "YOUR GCM_KEY",
            "dev_push": true
        };
        
    return {
        get: function(setting) {
            if (settings[setting]) {
                return settings[setting];
            }
            return null;
        }
    };
  "IONIC_SETTINGS_STRING_END";
``` 

- Lastly, in your index.html you can remove ```<script src="lib/ionic-platform-web-client/dist/ionic.io.bundle.min.js"></script>``` but leave ```<script src="cordova.js"></script>``` commented out.

- Everything should work now. To initiate the push service, use:

```javascript
    Ionic.io();
    ionicPush = new Ionic.Push().init({
        debug: true,
        onNotification: (data) => {
            console.log('New push notification received');
            console.log(data);
        }
    });

    ionicPush.register(data => {
        console.log("Device token:", data.token);
    });
```

## OneSignal
[Back to top](#push-notification) 
 
[Capacitor documentation](https://capacitorjs.com/docs/apis/push-notifications)       
[OneSignal documentation](https://documentation.onesignal.com/docs/ionic-sdk-setup)     

### Requirements

When you create your project on the OneSignal dashboard, you are invited to create a Firebase project to get a Firebase Sender ID and you must provide Apple push notification certificate (**note** : provide a new Key is better way than push notification certificate) with .p12 file.

After downloading this .p8 (apple key) file, you need to upload it to Firebase.

To do so, open the Cloud Messaging tab inside your Firebase project settings, upload the file and enter the details for the Key ID (which is already inside the name of the file) and your Team ID from iOS (you can find it usually in the top right corner below your name).

> Important : Do not forget to enable push notification capability for iOS when you create your certificates

### Installation

````
npm install onesignal-cordova-plugin
npm install @ionic-native/onesignal
npx cap sync
````

### Usage

*capacitor.config.json*
You can specify some badge's options
````

{
  "appId": "com.xxx.xxx",
  "appName": "pushApp",
  "bundledWebRuntime": false,
  "npmClient": "npm",
  "webDir": "www",
  "plugins": {
    "SplashScreen": {
      "launchShowDuration": 0
    },
    "PushNotifications": {
      "presentationOptions": ["badge", "sound", "alert"]
    }
  },
  "cordova": {}
}
````

*app.module.ts*

````
import { OneSignal } from '@ionic-native/onesignal/ngx';
@NgModule({
  declarations: [AppComponent],
  entryComponents: [],
  imports: [BrowserModule, IonicModule.forRoot(), AppRoutingModule],
  providers: [
    ...
    OneSignal,
    { provide: RouteReuseStrategy, useClass: IonicRouteStrategy }
  ],
  bootstrap: [AppComponent]
})
````

*home.component.ts*

````
import { OneSignal } from '@ionic-native/onesignal/ngx';

@Component({
  selector: 'app-home',
  templateUrl: 'home.page.html',
  styleUrls: ['home.page.scss'],
})
export class HomePage implements OnInit {
  oneSignalAppID = 'xxxxxxxx-xxxx-xxxx-xxxx-3aded1b78d09';
  firebaseSenderID = 'xxxxxxxxxxxx';
  
  constructor(private oneSignal: OneSignal) {}
  ngOnInit() {
    this.oneSignal.setLogLevel({logLevel: 6, visualLevel: 0});
    this.oneSignal.startInit(this.oneSignalAppID, this.firebaseSenderID);

    this.oneSignal.inFocusDisplaying(this.oneSignal.OSInFocusDisplayOption.InAppAlert);

    // create channel. 
    // Then, from onesignal dashboard : create a segment with => User Tag : channel is query
    this.oneSignal.sendTag('channel', 'query'); 
    
    // Handle notification received
    this.oneSignal.handleNotificationReceived().subscribe((jsonData) => {
      
      jsonData.included_segments['perso'];
      // do something when notification is received
      alert('notification received ' + JSON.stringify(jsonData));
      // jsonData.payload.data.notification_topic
      /*jsonData.payload.title;
      jsonData.payload.body;
      jsonData.payload.additionalData;*/
      this.trace += jsonData.payload.additionalData.task + '\r\n';
      
    });

    // Handle notification opened
    this.oneSignal.handleNotificationOpened().subscribe((jsonData) => {
      // do something when a notification is opened
      alert('notification opened');
      //jsonData.notification.payload.additionalData;
    });

    this.oneSignal.endInit();
  }
}

````
