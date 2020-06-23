[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Useful Functions

* [Find element in array](#find-element-in-array)    
* [Properly display error](#properly-display-error)    
* [Force screen refresh](#force-screen-refresh)    
* [Get specific platform](#get-specific-platform)   
* [Generate UUID](#generate-uuid)  
* [Safely displaying data with elvis operator](#safely-displaying-data-with-elvis-operator)    
* [Show / hide DOM element](#show-hide-dom-element)    
* [Checking network connection](#checking-network-connection)    
* [Close modal](#close-modal)    
* [Accessing DOM element @ViewChild](#accessing-dom-element)  
* [Show - Hide DOM element by screen size](#show-hide-dom-element-by-screen-size)    
* [Switch statement with complex expression](#switch-statement-with-complex-expression)    

## Find element in array

```
getPost(id: string) {
    return{...this.posts.find(p => p.id === id)};
}
```

## Ionic 2 useful functions snippets

[Personnal Ionic 2 snippet repo](https://github.com/gsoulie/Ionic2-snippets)    

## Properly display error
[Back to top](#useful-functions) 

Sometimes, displaying error code with ```alert(JSON.stringify(error));``` gives an understandable return like :

```
error {"__zone_symbol__currentTask":
{"type":"microTask",
"state":"notScheduled",
"source":"Promise.then",
"zone":"angular",
"cancelFn":null,
"runCount":0}}
```

To get a clean error code, use this syntax :

```javascript
alert(JSON.stringify(error, Object.getOwnPropertyNames(error));
```

## Force screen refresh
[Back to top](#useful-functions)  

You can force screen refresh by using *ChangeDetectorRef*

*home.ts*
```javascript
import { ChangeDetectorRef } from '@angular/core';

export class HomePage{
	constructor(private ref: ChangeDetectorRef){}
	
	onRefresh(){
		this.ref.detectChanges();
	}
}
```

## Get specific platform
[Back to top](#useful-functions)  

```javascript
var isWebView = ionic.Platform.isWebView();
var isIPad = ionic.Platform.isIPad();
var isIOS = ionic.Platform.isIOS();
var isAndroid = ionic.Platform.isAndroid();
var isWindowsPhone = ionic.Platform.isWindowsPhone();

var currentPlatform = ionic.Platform.platform();
var currentPlatformVersion = ionic.Platform.version();

platform.isPortrait() 
platform.isLandscape()

//Get environment variable with process.env.IONIC_ENV
if (process.env.IONIC_ENV == 'prod') {
  // production mode
  
} else {
  // dev mode 'dev'
}

-->
/*
window.addEventListener("orientationchange", function() {
    alert(window.orientation);
}, false);*/

ionic.Platform.exitApp(); // stops the app
```
 
## Generate UUID
[Back to top](#useful-functions) 

To simply generate UUID like "547d8e40-354c-11e6-aa97-083e8ec535fb"

```javascript
import { UUID } from 'angular2-uuid';
let uuid = UUID.UUID();
```

## Safely displaying data with elvis operator

Elvis operator (**?.**) aim to avoid error like 'Cannot read property xxxx od undefined'

Consider that we have a situation where we grab some data using **NavParams** inside the **ionViewDidLoad** lifecycle hook, and attempt to display it before it is ready :

*View file*

```xml
<ion-content>
	<p>{{someObject.comProperty}}</p>
</ion-content>
```
*Controller file*
```javascript
...
export class LessonPage{
	someObject: any;
	constructor(public navParams: NavParams){ }
	
	ionViewDidLoad(){
		this.someObject = this.navParams.get('someObject');
	}
}
```

At this point, in time, our template which tries to access that data has already attempted to do so. When the template tries to access the data, **someObject** will still be undefined so we would get the following error
```
Runtime Error
...Cannot read property 'someProperty' of undefined
```

To avoid this, we have to use **Elvis operator** like below :

*View file*

```xml
<ion-content>
	<p>{{someObject?.comProperty}}</p>
</ion-content>
```


## Show hide DOM element
[Back to top](#useful-functions) 

#### Solution 1

Consider that we have a card with a quote wich can be add/remove to favorites. We only show one of the two button : "add to favorites" when the quote is not in the favorites list, and "remove from favorites" when the quote is already in the favorites list. To do this, we will use a *ngIf* directive.

*View file*

```xml
...
<button ion-button (click)="addToFavorites" *ngIf="!isFavorite(quote)">Add to favorite</button>
<button ion-button (click)="removeFromFavorites" *ngIf="isFavorite(quote)">Remove from favorite</button>
...
```

*Controller file*
```javascript
export class HomePage {
	...
	isFavorite(quote: Quote){
		return this.myService.isQuoteFavorite(quote);
	}
	
	// Add the quote to the favorites list
	addToFavorites(quote: Quote){ ... }

	// remove the quote from the favorites list
	removeFromFavorites(quote: Quote){ ... }

}
```

#### Solution 2

The next solution is to use the *hidden* property of an element

```xml
<button ion-button (click)="removeNote()" [hidden]="creationMode" icon-only>
	<ion-icon name="trash"></ion-icon>
</button>
```

*controller.ts*
```javascript
export class HomePage {
  creationMode: boolean = false;
  ... 
}
```

> Angular JS 2 update : don't use hidden anymore

Prefer use this syntax

*view file*
```html
<button ion-fab fab-left icon-only [style.display]="menuIsOpen ? 'inherit':'none'">
   <ion-icon name="star"></ion-icon>
</button>
```

*controller file*
```javascript
menuIsOpen: boolean = false;
```


## Checking network connection
[Back to top](#useful-functions)

```
$ cordova plugin add cordova-plugin-network-information
```

```javascript
if(navigator.onLine){
   return true;
} else {
   return false;
}
```

## Close modal
[Back to top](#useful-functions)
	
*View file*
```xml
<button ion-button (click)="close()"></button>
```

*Controller file*

Don't forget to import *ViewController*

```javascript
import { Component } from '@angular/core';
import { NavController,ViewController} from 'ionic-angular';

@Component({
  templateUrl: 'build/pages/route-list/route-list.html',
})
export class RouteListPage {

  constructor(private navCtrl: NavController, private viewCtrl: ViewController) {

  }

  close(){
    this.viewCtrl.dismiss();
  }
}

```

## Work on JSON array

Return the max value of JSON array. Consider we have an JSON array like :

```javascript
myArray = [{date: "2017-01-01", cost: 21, qte: 16,  trip: 2999, price: 1.409,conso: 0},
      {date: "2017-01-01", cost: 21, qte: 16,  trip: 2655, price: 1.409,conso: 0},
      {date: "2017-01-01", cost: 21, qte: 16,  trip: 2833, price: 1.409,conso: 0}]
```

And the function which return the max value of *trip* attribute

```javascript
getMaxTrip() {
    return Math.max.apply(Math,this.myArray.map(function(o){return o.trip;}));
}
```
## Accessing DOM element
[Back to top](#useful-functions)  

Here, we are going to see how to accessing to a DOM element with the directive ```@ViewChild```. In the following sample, we want to change the label of the top left back button.

*View File*

```xml
<ion-header>
  <ion-navbar color="primary">
    <ion-title>Gestion du carburant</ion-title>
  </ion-navbar>
</ion-header>

<ion-content>
...
</ion-content>
```

*Controller file*

```javascript
import { Component, ViewChild } from '@angular/core';
import { Navbar } from 'ionic-angular';

@Component({
  selector: 'page-user',
  templateUrl: 'user.html'
})
export class UserPage {
 @ViewChild(Navbar, {static: false}) navbar: Navbar;
 
 ionViewWillEnter(){
 	this.navbar.setBacButtonText('Home');	// change back button title
 }
}
```

see more @ViewChild - @ViewChildren


## Show Hide DOM element by screen size
[Back to top](#useful-functions)  

Sometimes you will need to show/hide some dom element switch screen size. You can achieve this with that following code :

*controller file*
````
ngOnInit() {
    if (window.screen.width <= 768) { // 768px portrait
      this.smallScreen = true;
    } else {
      this.smallScreen = false;
    }
}
````

Then just use ````*ngIf```` directive on you item to display it or not

### Detect window orientation change

You can improve the user experience by detecting screen orientation change et show / hide DOM element when switching portrait/landscape

*controller file*
````
ngOnInit() {...}

@HostListener('window:orientationchange', ['$event'])
    onOrientationChange(event) {
      console.log('orientationChanged');
      if (window.screen.width <= 768) { // 768px portrait
        this.smallScreen = true;
      } else {
        this.smallScreen = false;
      }
}
````

### Listener on window resize
You can also add a subscription on window resize event if you need to manage responsive behaviour

````
ngOnInit() {
   window.onresize = (e) => {
      this.ngZone.run(() => {
          this.generateMap(window.innerHeight);
          this.ref.detectChanges();
      });
    };
}
ngOnDestroy() {
    // WARNING ! You need to remove the window resize listener
    // or you will get a ViewDestroyError when you leave screen
    window.onresize = null;
}
````

## Switch statement with complex expression
[Back to top](#useful-functions)  

If you need to use complex expression in switch statement like below :

```
switch (value) {
	case (value >= 0 && value < 10) :
		...
		break;
	case (value >= 10 && value < 20) :
		...
		break;
}
```

This is not working. To fix it, you have to set **true** as a value

```
let value = 15;

switch (true) {
	case (value >= 0 && value < 10) :
		...
		break;
	case (value >= 10 && value < 20) :
		...
		break;
}
```
