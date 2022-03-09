[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Phone and social sharing

* [Phone call](#phone-call)   
* [Social sharing](#social-sharing)    

## Phone call
[Back to top](#phone-and-social-sharing) 

### HTML version

````html
<ion-fab-button [href]="tel">
      <ion-icon name="call-outline"></ion-icon>
</ion-fab-button>
````

````typescript
ngOnInit(): void {
  this.tel = `tel:${this.contact.tel}`;
}

phoneCallByCode() {
      window.open(`tel:${this.user.tel}`);
}
````

### Capacitor

Capacitor using the native call number plugin

```
npm install call-number 
npm install @awesome-cordova-plugins/call-number 
ionic cap sync
```

Don't forget to add import into you *app.module.ts* in providers section.

```typescript
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

### Installation

````
npm install @capacitor/share
npx cap sync
````

### Usage

```typescript
import { Share } from '@capacitor/share';

@Component({
  selector: 'app-resultat-positif',
  templateUrl: './resultat-positif.page.html',
  styleUrls: ['./resultat-positif.page.scss'],
})
export class HomePage implements OnInit {
  constructor() { }

  async sharingInfo() {
    await Share.share({
      title: 'See cool stuff',
      text: 'Really awesome thing you need to see right meow',
      url: 'http://ionicframework.com/',
      dialogTitle: 'Share with buddies',
      });
  }
```
