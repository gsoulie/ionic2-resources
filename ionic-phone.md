[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Phone and social sharing

* [Phone call](#phone-call)   
* [Social sharing](#social-sharing)    

## Phone call
[Back to top](#phone-and-social-sharing) 

### Cordova
According to the official documentation, you can use the *call-number* native plugin. But you may encounter a problem with it.

So you can use this workaround 

```
call(phoneNumber: string = 'XXXXXXX') {
      setTimeout(() => {
          window.open(`tel:${phoneNumber}`, '_system');
      }, 100);
}
```
### Capacitor

Capacitor using the native call number plugin

```
npm install call-number
npm install @ionic-native/call-number
ionic cap sync
```

Don't forget to add import into you *app.module.ts* in providers section.

```
import { CallNumber } from '@ionic-native/call-number/ngx';

@Component({
  selector: 'app-resultat-positif',
  templateUrl: './resultat-positif.page.html',
  styleUrls: ['./resultat-positif.page.scss'],
})
export class HomePage implements OnInit {
  constructor(private callNumber: CallNumber) { }
  
  call(phoneNumber: string) {
    this.callNumber.callNumber('XXXXXXXXXX', true)
    .then(res => console.log('Launched dialer!', res))
    .catch(err => console.log('Error launching dialer', err));
  }
```

## Social sharing
[Back to top](#phone-and-social-sharing) 

### Cordova

Cordova uses *SocialSharing* plugin (https://ionicframework.com/docs/v3/native/social-sharing/)

*controller file*

```
import { SocialSharing } from '@ionic-native/social-sharing/ngx';

export class HomePage {
  constructor(private socialSharing: SocialSharing){ }
  
  sharingInfo() {
    const options = {
      message: 'share this', // not supported on some apps (Facebook, Instagram)
      subject: 'the subject', // fi. for email
      url: 'https://www.website.com/foo/#bar?a=b',
      chooserTitle: 'Pick an app' // Android only, you can override the default share sheet title
      //appPackageName: 'com.apple.social.facebook', // Android only, you can provide id of the App you want to share with
      //iPadCoordinates: '0,0,0,0' //IOS only iPadCoordinates for where the popover should be point.  Format with x,y,width,height
    };
    
    // Method for sharing with all installed app
    this.socialSharing.share('this is message content',
    'message title', 'files', 'http://wwww.xxx.xxx')
    .then(() => {
      // ok
    })
    .catch((e) => {
      // error
    });
  }
}
```

### Capacitor
[Back to top](#phone-and-social-sharing) 

```
import { Plugins } from '@capacitor/core';
const { Share } = Plugins;

@Component({
  selector: 'app-resultat-positif',
  templateUrl: './resultat-positif.page.html',
  styleUrls: ['./resultat-positif.page.scss'],
})
export class HomePage implements OnInit {
  constructor() { }

  async sharingInfo() {
    await Share.share({
      title: 'My title',
      text: 'Really awesome thing you need to see right meow',
      url: 'http://ionicframework.com/',
      dialogTitle: 'Share with buddies'
    });
  }
```
