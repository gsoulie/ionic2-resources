[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Local Storage

* [Cordova](#cordova)  
* [Capacitor](#capacitor)    

## Cordova
[Back to top](#local-storage)  

Cordova use *Storage* plugin

*Exemple first launch detection*

```
import { Storage } from '@ionic/storage';

export class AppComponent {
  constructor(
    private platform: Platform,
    private splashScreen: SplashScreen,
    private statusBar: StatusBar,
    private storage: Storage,
    private router: Router
  ) {
    this.initializeApp();
  }

  initializeApp() {
    this.platform.ready().then(() => {
      this.storage.get('first_launch').then((val) => {
        if (val !== null) {
          // not first launch
          this.router.navigateByUrl('/home');
        } else {
          // first launch
          this.storage.set('first_launch', 'done');
          this.router.navigateByUrl('/tutorial');
        }
     });

      this.statusBar.styleDefault();
      this.splashScreen.hide();
    });
  }
}
```


## Capacitor
[Back to top](#local-storage) 

*Same example as above*

```
import { Plugins } from '@capacitor/core';
import { Router } from '@angular/router';
const { Storage } = Plugins;

export class AppComponent {
  constructor(
    private platform: Platform,
    private splashScreen: SplashScreen,
    private statusBar: StatusBar,
    private router: Router
  ) {
    this.initializeApp();
  }

  initializeApp() {
    this.platform.ready().then(() => {
      Storage.get({key: 'first_launch'}).then((val) => {
        if (val.value !== null) {
          // not first launch
          this.router.navigateByUrl('/home');
        } else {
          // first launch
          Storage.set({key: 'first_launch',
          value: 'done'});
          this.router.navigateByUrl('/tutorial');
        }
     });

      this.statusBar.styleDefault();
      this.splashScreen.hide();
    });
  }
}
```

