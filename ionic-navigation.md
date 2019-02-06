[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Navigation

* [Navigation by routing](#navigation-by-routing)    
* [Using NavController](#using-navcontroller)   
* [Passing data](#passing-data)  
* [Passing data on close event](#passing-data-on-close-event)    
* [Disable Android hardware back button](#disable-android-hardware-back-button)    


## Navigation by routing

Since Ionic 4, the navigation is opered by the Angular routing process. However, navigating by the NavController (with push / pop) is still possible.

The root configuration for the router lives in the *src/app/app-routing.module.ts* file. You may need to define routes manually, but it will be done automatically with the CLI when you generate a page.

```
import { NgModule } from '@angular/core'; 
import { Routes, RouterModule } from '@angular/router'; 

const routes: Routes = [ 
// Redirect
{ path: '', redirectTo: 'home', pathMatch: 'full' }, 

// Regular route
{ path: 'product', component: ProductComponent }, 

// Regular route with parameter
{ path: 'hello/:id', component: MyComponent },

// Lazy loaded Route (Page)
{ path: 'home', loadChildren: './pages/home/home.module#HomePageModule' }, 

// Lazy loaded with parameter
{ path: 'recipe/:id', loadChildren: './pages/recipe-list/recipe-list.module#RecipeListPageModule' }, 
]; 

@NgModule({ 
imports: [RouterModule.forRoot(routes)], 
exports: [RouterModule] })

export class AppRoutingModule { }
```

*path* : define the url of the page in the browser
*redirectTo* : redirect the associed path to the *redirectTo* target
*loadChildren* : define the module.ts file (of the page to show) to load before showing the page => Lazy loading
*component* : define the ts file to load

> **loadChildren** : is important to use for PWA

### Navigate by view file

```
<ion-button href="/hello">Hello</ion-button>

// with parameter
<ion-button href="/hello/me">Hello</ion-button>	<!-- WARNING you must define hello/id route in your app-routing.module.ts file
```

### Navigate by code

```
import { Component } from '@angular/core';
import { Router } from '@angular/router';

@Component({ ... })
export class HomePage {
  constructor(private router: Router) {}

  go() {
    this.router.navigateByUrl('/animals');
  }
}
```

### Get routing parameters

```
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({ ... })
export class ProfileComponent implements OnInit {

  id: string;

  constructor(private route: ActivatedRoute) {}

  ngOnInit() {
    this.id = this.route.snapshot.paramMap.get('id');
  }
}
```

It is also possible to react to changes by using Observable

```
ngOnInit() {
  this.route.params.subscribe(...);
}
```

## Using NavController

### Passing Data
[Back to top](#navigation)

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

### Passing data on close event
[Back to top](#navigation)

Let's see how to return value from child page to parent

*Parent controller*

```javascript
returnedValue;

onOpenChild(){
	this.navCtrl.push(ChildPage, {id: this.myId, callback: (data) => {
		this.returnedValue = data}
	});
}
```


*Child controller*

```javascript

onSelectItem(e){
	this.navCtrl.pop()
	.then(() => {
		this.navParams.get("callback")(e);
	});
}
```

## Disable Android hardware back button
[Back to top](#navigation)

```javascript
public unregisterBackButtonAction: any;

constructor(public platform: Platform){...}

// When entering page
ionViewWillEnter(){
	// initialize listener on back button
	this.initializeCustomBackButtonHandler();
}

// When leaving page
ionViewWillLeave(){
	this.unregisterBackButtonAction && this.unregisterBackButtonAction();
}

// Initialize back button custom handler
initializeCustomBackButtonHandler(){
	this.unregisterBackButtonAction = this.platform.registerBackButtonAction(() => {},101);
}
```

