[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Themes

* [Color Generator](#color-generator)    
* [Dynamic theming](#dynamic-theming)  
* [Navbar](#navbar)    
* [StatusBar](#statusbar)    
* [Change background color of a specific page](#change-background-color-of-a-specific-page)    
* [Override Sass variable](#override-variable-sass)    
* [Remove Android input green highlight](#remove-android-input-green-highlight)    
* [Remove android navbar border](#remove-android-navbar-border)    
* [Custom fonts](#custom-fonts)    

## Color Generator
[Back to top](#themes)    

Ionic 4 provide an online color generator : https://ionicframework.com/docs/theming/color-generator

This color generator allows you to dynamically generate your */src/theme/variables.scss* file

## Dynamic theming

[devdactic dynamic theming](https://devdactic.com/dynamic-theming-ionic/)    

[Dynamically switch app theming - light/night mode](https://chrisgriffith.wordpress.com/2019/05/03/dynamic-ionic-theming/?utm_campaign=Ionic%20Dev%20Newsletter&utm_source=hs_email&utm_medium=email&utm_content=72678826&_hsenc=p2ANqtz-9QWSeTR97KwevKMUcvzKEnRYTLjx5Ugy1aS6vxYqFQFWg8sqlfJy6xbewh6Gy_7omSyC_J4HmXrz50Ufwq09hsC-E_gOU2QJaK_tXVpq5z7bQQEP8&_hsmi=72678826)

## Navbar
[Back to top](#themes)    

[Styling on header](https://yannbraga.com/2017/03/16/how-to-transparent-header-ionic2/)

## Statusbar
[Back to top](#themes)    

**cordova plugin installation**

```
$ ionic cordova plugin add cordova-plugin-statusbar
$ npm install --save @ionic-native/status-bar
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

To change ios searchbar background color, add the following code to your variable.scss file :

```$searchbar-ios-background-color: rgba(247,247,247,1);```

## Change background color of a specific page
[Back to top](#themes)    

```xml
<ion-content padding class="masters">
  <ion-list inset="true">
    <ion-item *ngFor="#master of masterPages" (click)="navMaster(master)">
      {{master.title}}
    </ion-item>
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
[Back to top](#themes)    

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

## Remove Android input green highlight
[Back to top](#themes)    

add the following to your *variable.scss*

```
$text-input-md-show-focus-highlight: false !default;
$text-input-md-show-valid-highlight: $text-input-md-show-focus-highlight !default;
$text-input-md-show-invalid-highlight: $text-input-md-show-focus-highlight !default;
```

## Remove Android Navbar border
[Back to top](#themes)    

```
<ion-header no-border>

<ion-header>
```

## Custom fonts
[Back to top](#themes)    

To use custom fonts, first download font file and put it in src/assets/fonts

Next, add a *@font-face* for each font you want to use

*variable.scss*

```
@font-face {
    font-family: "AguafinaScript-Regular";
    src: url("../assets/fonts/AguafinaScript-Regular.ttf") format("truetype");
    font-weight: 200;
    font-style: normal;
}
@font-face {
    font-family: "DINPro";
    src: url("../assets/fonts/DINPro.otf") format("opentype");
    font-weight: 200;
    font-style: normal;
}
@font-face {
    font-family: "DINPro-Italic";
    src: url("../assets/fonts/DINPro-Italic.otf") format("opentype");
    font-weight: 200;
    font-style: normal;
}
:root {
    --ion-font-family: 'DINPro';
    ...
    ...
}
```

**Important** : *format()* must use the real font format name (i.e ttf = truetype, otf = opentype)

And use it like :

*home.scss*

```
ion-title{
	font-family: "AguafinaScript-Regular" !important;
}
.label1{
	font-family: "DINPro" !important;
	font-size: 18px;
}
.label2{
	font-family: "DINPro-Italic" !important;
	font-size: 18px;
}
```

** Assign font-family to the entire app**

*global.scss*

```
* {
	font-family: 'My-custom-font' !important;
}
```
