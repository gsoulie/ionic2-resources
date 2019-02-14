[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Navigation

* [Navigation by routing](#navigation-by-routing)   
* [Tab Routing](#tab-routing)    
* [Routing Guards](#routing-guards)    
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

> **loadChildren** : Recommended for PWA

### Navigate by view file

```
<ion-button href="/hello">Hello</ion-button>

// with parameter
<ion-button href="/hello/me">Hello</ion-button>	<!-- WARNING you must define hello/id route in your app-routing.module.ts file
```

### Router Direction

You can specify router direction to set a specific animation

```
<ion-button expand="block" routerLink="/" routerDirection="root">
   Logout
</ion-button>
```

routerDirection values

- *root*
- *back*
- *forward*

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

### Navigate to the previous page
[Back to top](#navigation)

#### Classic way

The classic way to navigate back is to manually manage a back button using *defaultHref* property
```
<ion-header>
 <ion-toolbar color="primary">
   <ion-buttons slot="start">
     <ion-back-button defaultHref="/dashboard"></ion-back-button>
   </ion-buttons>
   <ion-title>Details</ion-title>
 </ion-toolbar>
</ion-header>

<ion-content padding>
 My ID is: {{ myId }}
</ion-content>
```

**Note :** This method does not allow to navigate back on a specific tab.

#### Navigarte from page to specific tab

The trick consists in create a new service like below : 

*previous-routing-service*
```
import { Router, NavigationEnd } from '@angular/router';
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class PreviousRouteServiceService {

  private previousUrl: string;
  private currentUrl: string;

  constructor(private router: Router) {
    this.currentUrl = this.router.url;
    router.events.subscribe(event => {
      if (event instanceof NavigationEnd) {        
        this.previousUrl = this.currentUrl;
        this.currentUrl = event.url;
      };
    });
  }

  public getPreviousUrl() {
    return this.previousUrl;
  }    
}
```

Then, just instanciate this service in each page which call a subpage

```
constructor(private previousRouteService: PreviousRouteServiceService) { }
```

And call the ```getPreviousUrl()``` function in sub page to get the last route and redirect to it

```
backFwd(){
    this.router.navigateByUrl(this.previousRouteService.getPreviousUrl());
}
```

## Tab Routing
[Back to top](#navigation)

An advanced technique is to use named outlets and secondary routes for rendering nested content inside the view - this is how the Ionic Tabs starter template works. The reason behind this routing setup is to achieve a push/pop style of navigation commonly used in native apps. Remember, the NavController from Ionic 3 is deprecated, so named outlets in Angular provide a comparable replacement.

```
{
  path: 'contact',
  outlet: 'modal',
  component: ContactModal
}
```

In the HTML, this route will be looking for an outlet with the matching name.

```<ion-router-outlet name="modal"></ion-router-outlet>```

## Routing Guards
[Back to top](#navigation)

Guards are an extremely powerful and under-utilized tool in the Angular router. At first, they might seem overwhelming, but they are really just the implementation of a single method, typically canActivate, which must return a boolean or something that resolves to a boolean, like a Promise or Observable.

```
ionic g guard guards/auth
```

*Example of guard*
```
@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  constructor(private router: Router) {}

  canActivate(
    next: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): boolean {

    const loggedIn = false; // replace with actual user auth checking logic

    if (!loggedIn) {
      this.router.navigate(['/']);
    }

    return loggedIn;
  }
}
```

Now you can apply this rule to as many routes as you need in the router config.

```
const routes: Routes = [
  { path: 'special', component: SpecialPage, canActivate: [AuthGuard] },
];
```

**Note :** You can pass an array of multiple guards to check differents conditions

### Example of guard with AngularFireAuth

```
import { AngularFireAuth } from 'angularfire2/auth';
import { Injectable } from '@angular/core';
import { CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot, Router } from '@angular/router';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class AuthGuardGuard implements CanActivate {
  constructor(public afAuth: AngularFireAuth, 
    private router: Router){}
  canActivate(
    next: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean> | Promise<boolean> | boolean {
    
    this.afAuth.authState.subscribe(res => {
      if (res && res.uid) {
        return true;
      } else {
        this.router.navigate(['/login']);
        return false;
      }
    });
    return true;
  }
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

