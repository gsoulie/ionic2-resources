[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Themes

## Dynamic theming

[devdactic dynamic theming](https://devdactic.com/dynamic-theming-ionic/)    

## Statusbar

**cordova plugin installation**

```
$ ionic cordova plugin add cordova-plugin-statusbar
```

**Customize**

```javascript
import {Platform} from 'ionic-angular';
import {Component} from '@angular/core';
import {TabsPage} from './pages/tabs/tabs';


@Component({
  template: '<ion-nav [root]="rootPage"></ion-nav>',
  config: {} // http://ionicframework.com/docs/v2/api/config/Config/
})
export class MyApp {
  static get parameters() {
    return [[Platform]];
  }

  constructor(platform) {
    this.rootPage = TabsPage;

    platform.ready().then(() => {
            StatusBar.backgroundColorByHexString("#25312C")
    });
  }
}
```

## Change background color of a specific page

```xml
<ion-content padding class="masters">
  <ion-list inset>
    <button ion-item *ngFor="#master of masterPages" (click)="navMaster(master)">
      {{master.title}}
    </button>
  </ion-list>
</ion-content>
```

Then in your scss you target the class in the ion-content tag:

```javascript
.masters {
  background-color: green;
}
```

Lastly you make sure this scss is being compiled with your **app.core.scss** by importing it in that same file like:

```javascript
@import "../pages/masters/masters";
```

## Override variable.scss

In the *variable.scss* file, you can define a lot of variables to create your custom style (see more on [ionic 2 overriding variables](#https://ionicframework.com/docs/v2/theming/overriding-ionic-variables/). Here is an example of variable.scss overriding

*variable.scss*

```javascript
...
// Shared variables
$myOwnVariable: <value>;
$content-padding: 5px;

// Colors variables
$color: {
	primary: ...
	secondary: ...
	...
}

```

*note* : New colors variables must be called with ```<Button color="myNewColor">```
