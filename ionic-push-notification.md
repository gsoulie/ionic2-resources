# Push notification

## FCM push notification (Firebase Cloud Messaging)

[Documentation](#https://www.djamware.com/post/58a1378480aca7386754130a/ionic-2-fcm-push-notification)    

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
    this.fcm.subscribeToTopic('all');
    this.fcm.getToken().then(token => {
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
  }
}
```

Finally, run the application on a device

#### 7 - Send notification from Firebase

Go to your firebase console and open **Notifications** menu, then follow the instructions to send your first push notification. 

## Other way
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
[Back to top](#push-notification) 
