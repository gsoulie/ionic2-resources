[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Authentication

## Ionic appauth

[Installation](https://www.npmjs.com/package/ionic-appauth)       
[Sample project](https://github.com/wi3land/ionic-appauth-capacitor-demo)      
[Source code](https://github.com/wi3land/ionic-appauth/blob/eec2889df0b526b56161a193ff54e3e1d2547776/src/auth-service.ts)      

````
auth_config: {
  client_id: 'my-client-id',
  server_host: 'https://sso.my-server.com',
  redirect_url: 'com.appauth.demo://callback',
  end_session_redirect_url: 'com.appauth.demo://endsession',
  scopes: 'openid profile email roles my.api offline_access',
  pkce: true
}
 
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

Then create the following routes on your server : *com.appauth.demo://callback* and *com.appauth.demo://ensession*

## Ionic auth
https://ionicframework.com/blog/ionic-auth-connect-single-sign-on-made-easy/

**!! see pricing**

## Login with Angular 2
[devdactic login with Angular 2](https://devdactic.com/login-ionic-2/)    

## ng2-ui-auth
[Link](https://github.com/ronzeidman/ng2-ui-auth)
[Example](ng2-ui-auth-example)

## JWT
[Link](https://jwt.io/)
