[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Phone and social sharing

* [Phone call](#phone-call)   
* [Social sharing](#social-sharing)    

## Phone call
[Back to top](#phone-and-social-sharing) 

According to the official documentation, you can use the *call-number* native plugin. But you may encounter a problem with it.

So you can use this workaround 

```
let phoneNumber = "xxxxxxxx";
setTimeout(() => {
      window.open(`tel:${phoneNumber}`, '_system');
    }, 100);
```

## Social sharing

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
