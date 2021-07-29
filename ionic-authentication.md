[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Authentication

## Ionic appauth with capacitor redirection after authentication

[Installation](https://www.npmjs.com/package/ionic-appauth)       
[Sample project](https://github.com/wi3land/ionic-appauth-capacitor-demo)      
[Source code](https://github.com/wi3land/ionic-appauth/blob/eec2889df0b526b56161a193ff54e3e1d2547776/src/auth-service.ts)     

Using ionic appauth with Identity server

````typescript
auth_config: {
  client_id: 'my-client-id',
  server_host: 'https://sso.my-server.com',
  redirect_url: 'com.appauth.demo://callback',
  end_session_redirect_url: 'com.appauth.demo://endsession',
  scopes: 'openid profile email roles my.api offline_access',
  pkce: true
}
````

*capacitor.conf.json*
````typescript
// capacitor.conf.json => empty
{
  "appId": "com.appauth.demo",
  "appName": "appauth demo",
  "bundledWebRuntime": false,
  "npmClient": "npm",
  "webDir": "www",
  "plugins": {
    "SplashScreen": {
      "launchShowDuration": 3000
    },
    "Keyboard": {
      "resize": "ionic"
    }
  }
}
````

Then create the following routes on your server (in redirect Uris and Cors sections) : **com.appauth.demo://callback** and **com.appauth.demo://ensession**

*app.component.ts*

````typescript
import { Router } from '@angular/router';
import { AuthService, IAuthAction, AuthObserver, AuthActions } from 'ionic-appauth';
import { Component } from '@angular/core';
import { Platform, LoadingController } from '@ionic/angular';
import { SplashScreen } from '@ionic-native/splash-screen/ngx';
import { StatusBar } from '@ionic-native/status-bar/ngx';

@Component({
  selector: 'app-root',
  templateUrl: 'app.component.html',
  styleUrls: ['app.component.scss']
})
export class AppComponent {
  action: IAuthAction;
  observer: AuthObserver;
  
  constructor(
    private platform: Platform,
    private splashScreen: SplashScreen,
    private statusBar: StatusBar,
    private auth: AuthService,
    private router: Router,
    private loadingCtrl: LoadingController
  ) {
    this.initializeApp();
  }

  initializeApp(): void {
    this.platform.ready().then(() => {
      this.statusBar.styleDefault();
      this.splashScreen.hide();
      this.initializeAuth();
    });
  }

  async initializeAuth(): Promise<void> {
    const loading = await this.loadingCtrl.create({ message: 'Authenticate...' });
    await loading.present();
    this.auth.loadTokenFromStorage();
    this.observer = this.auth.addActionListener((action) => {
      this.action = action;
      if (action.action === AuthActions.SignInSuccess ||
        action.action === AuthActions.LoadTokenFromStorageSuccess) {        
        loading.dismiss();
        this.router.navigate(['/home']);
      } else {
        loading.dismiss();
        this.signIn();
      }
    });
  }
  
  ngOnDestroy(): void {
    this.auth.removeActionObserver(this.observer);
  }

  public signIn(): void {
    this.auth.signIn();
  }
}
````


## Ionic auth
https://ionicframework.com/blog/ionic-auth-connect-single-sign-on-made-easy/

**!! see pricing**

## angular-oauth2-oidc

### WARNING !! angular-oauth2-oidc seems doesn’t work with capacitor for mobile app. There is an issue to get the token back after authenticate.

## Login with Angular 2
[devdactic login with Angular 2](https://devdactic.com/login-ionic-2/)    

## ng2-ui-auth
[Link](https://github.com/ronzeidman/ng2-ui-auth)
[Example](ng2-ui-auth-example)

## JWT
[Link](https://jwt.io/)
