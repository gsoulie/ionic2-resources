[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Useful Functions

* [Find element in array](#find-element-in-array)    
* [Ionic Angular snippets](#ionic-angular-snippets)      
* [Properly display error](#properly-display-error)    
* [Get platform info](#get-specific-platform)   
* [Generate UUID](#generate-uuid)  
* [Show / hide DOM element](#show-hide-dom-element)    
* [Accessing DOM element @ViewChild](#accessing-dom-element)  
* [Show - Hide DOM element by screen size](#show-hide-dom-element-by-screen-size)    
* [Switch statement with complex expression](#switch-statement-with-complex-expression)    

## Find element in array

```typescript
getPost(id: string) {
    return {...this.posts.find(p => p.id === id)};
}
```

## Ionic Angular snippets

[Personnal Ionic Angular snippet repo](https://github.com/gsoulie/Ionic2-snippets)    

## Properly display error
[Back to top](#useful-functions) 

Sometimes, displaying error code with ```alert(JSON.stringify(error));``` gives an understandable return like :

```typescript
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

## Get platform info
[Back to top](#useful-functions)  

````
npm install @capacitor/device
npx cap sync
````

```typescript
import { Device } from '@capacitor/device';

const logDeviceInfo = async () => {
  const info = await Device.getInfo();
};

const logBatteryInfo = async () => {
  const info = await Device.getBatteryInfo();
};
```

**See documentation** [here](https://capacitorjs.com/docs/apis/device#getinfo) for information format
 
## Generate UUID
[Back to top](#useful-functions) 

To simply generate UUID like "547d8e40-354c-11e6-aa97-083e8ec535fb"

```javascript
import { UUID } from 'angular2-uuid';
let uuid = UUID.UUID();
```

## Show hide DOM element
[Back to top](#useful-functions) 

### *hidden* property

You can use the *hidden* property of an element to hide/show it :

```html
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
## Work on JSON array

Return the max value of JSON array. Consider we have an JSON array like :

```typescript
myArray = [
	{date: "2017-01-01", cost: 21, qte: 16,  trip: 2999, price: 1.409,conso: 0},
	{date: "2017-01-01", cost: 21, qte: 16,  trip: 2655, price: 1.409,conso: 0},
      	{date: "2017-01-01", cost: 21, qte: 16,  trip: 2833, price: 1.409,conso: 0}]
```

And the function which return the max value of *trip* attribute

```typescript
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
````typescript
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
````typescript
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

````typescript
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

```typescript
switch (value) {
	case (value >= 0 && value < 10) :
		...
		break;
	case (value >= 10 && value < 20) :
		...
		break;
}
```

This is **not working**. To fix it, you have to set **true** as a value

```typescript
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
