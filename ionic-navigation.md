# Navigation
[Back to top](#ionic-2)  

### Using NavController

[link : Understanding ionic 2 NavController](http://mcgivery.com/understanding-ionic-2-navigation-navcontroller/)

Before we can use the NavController, we will need to import it.

```javascript
import {NavController} from 'ionic-angular';
```

Next we will inject it into our ```@Component``` and assign it to a property.

```javascript
import {Component, NavController} from 'ionic-angular';

@Component({
	templateUrl: "build/pages/Main/Main.html"
})
export class MainPage(){
	constructor(nav: NavController){
		this.nav = nav;
	}
}
```

Now, we can call properties on nav, our instance of **NavController**. For example, say we want to navigate from our Main view to our About view, we would need to start by importing that ```@Component``` class.

```javascript
import {Component, NavController} from 'ionic-angular';
import {AboutPage} from 'About/About'

@Component({
	templateUrl: "build/pages/Main/Main.html"
})
export class MainPage(){
	constructor(nav: NavController){
		this.nav = nav;
	}
}
```

Next, let’s create a method on our page called **goToAbout** that we can call from our template. This method will push the AboutPage onto the stack.

```javascript
import {Component, NavController} from 'ionic-angular';
import {AboutPage} from 'About/About'

@Component({
	templateUrl: "build/pages/Main/Main.html"
})
export class MainPage(){
	constructor(nav: NavController){
		this.nav = nav;
	}
	
	goToAbout(){
		this.nav.push(AboutPage);
	}
}
```

In our template, **Main.html**, we will have a button that will call this method when pressed.

```xml
<button ion-button (click)="goToAbout()">About</button>
```

To summarize, when this button is pressed, it will call the goToAbout method which pushes an instance of the AboutPage class onto the navigation stack which is then compiled and animated into view.

#### Passing Data
In many scenarios we have data in one view that we need to pass to another. Luckily, the push method accepts a second parameter which is an object of data to pass to the ```@Component``` passed into the first parameter.

```javascript
this.nav.push(AboutPage,{
	username: "andrewmcgivery",
	blogger: true
});
```

This data is then accessible in the pushed ```@Component``` via navParams which is similar to $stateParams in 1.0.

```javascript
import {Component, NavParams} from 'ionic-angular';

@Component({
	templateUrl: 'build/pages/About/About.html',
})
export class AboutPage {
	constructor(navParams: NavParams){
		this.username = navParams.get("username"); // "andrewmcgivery"
		this.blogger = navParams.get("blogger"); // true
	}
}
```
#### Pop

Pop is super simple to use as well. As an example, if we wanted to create a function called **goBack** that goes back when pressed in our AboutPage, we could just call **nav.pop()** :

```javascript
import {Component, NavController} from 'ionic-angular';

@Component({
	templateUrl: 'build/pages/About/About.html',
})
export class AboutPage {
	constructor(nav: NavController){
		this.nav = nav;
	}
	
	goBack(){
		this.nav.pop();
	}
}
```

#### Other Methods
There is a few more methods available on the NavController such as insert, remove, etc. I would suggest reading the Official Docs.

#### Lifecycle Events
In version 1.0, we had the concept of events being fired when we were entering and leaving the view, among others. In version 2.0, we have a very similar set of events. To handle one of these events, we just need to give our ```@Component``` class a method that matches the event. For example, if we want to run an event when the ```@Component``` is loaded, we will need to give our page the **onPageLoaded** method:
```javascript
import {Component} from '@angular/core';

@Component({
	templateUrl: 'build/pages/About/About.html',
})
export class AboutPage {
	onPageLoaded(){
		console.log("Page Loaded!");
	}
}
```

We can have a set of handlers for a variety of events including then the page is about to be entered, when the page is leaving, etc. Again, I would suggest reading the Official Docs.

#### Navigate using navPush directive

*View file*

```html
...
<ion-content>
	<button ion-button [navPush]="userPage">User</button>
</ion-content>
```

*Controller file*

```javascript
import { NavController } from "ionic-angular";
import { UserPage } from "../user/user";
...
export class HomePage {
	userPage = UserPage;
	
	constructor(public navCtrl: NavController) {}
}
```