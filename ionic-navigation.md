[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Navigation

* [Navigation by routing](#navigation-by-routing)   
* [Routing params](#routing-params)    
* [Reset routing params](#reset-routing-params)    
* [Child routes](#child-routes)    
* [Tab Routing](#tab-routing)    
* [Passing static data to a Route](#passing-static-data-to-a-route)     
* [Passing dynamic data to a Route](#passing-dynamic-data-to-a-route)    
* [Routing Guards](#routing-guards)    
* [Wildcard route (404)](#wildcard-route)    
* [Using NavController](#using-navcontroller)   
* [Passing data Ionic 3](#passing-data-ionic-3)  
* [Passing data on close event](#passing-data-on-close-event)    
* [Disable Android hardware back button](#disable-android-hardware-back-button)    


## Navigation by routing

Since Ionic 4, the navigation is opered by the Angular routing process. However, navigating by the NavController (with push / pop) is still possible.

The root configuration for the router lives in the *src/app/app-routing.module.ts* file. You may need to define routes manually, but it will be done automatically with the CLI when you generate a page.

> **IMPORTANT** : route definition order is very important     

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

Mutliple solutions are available to navigate by code. You can use **router.navigate** or **router.navigateByUrl**

#### navigateByUrl

When you use ```router.navigateByUrl``` Angular completly rebuild the path from the root. So you need to specify the complete path for rooting

```
import { Component } from '@angular/core';
import { Router } from '@angular/router';

@Component({ ... })
export class HomePage {
  constructor(private router: Router) {}

  onOpenNewRecipe() {
    this.router.navigateByUrl('/recipe/new');
  }
}
```

#### navigate

When you use ```router.navigate```, you can specify the current root to Angular. If you do that, you just need to set the endpoint of the target root

```
import { Component } from '@angular/core';
import { Router } from '@angular/router';

@Component({ ... })
export class HomePage {
  constructor(private router: Router,
  		private activatedRoute: ActivatedRoute) {}

  onOpenNewRecipe() {
    this.router.navigate(['new'], {relativeTo: this.activatedRoute});	// append 'new' to the current route
  }
}
```


```this.router.navigate(['new']);``` is equal to ```this.router.navigateByUrl(['new']);``` and give the route **/new**
```this.router.navigate(['new'], {relativeTo: this.activatedRoute});``` gives the route **/<current_route>/new**



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


## Routing params
[Back to top](#navigation)

### Official Documentation solution

*app-routing.module.ts*

```
...
{ path: 'pubication/:id', loadChildren :'./publication/publication.module#PublicationPageModule'}
...
```

*Home.html*

```
<ion-button routerLink="/publication/54" routerDirection="forward">Go to Detail<ion-button>

<!-- USING CONSTANT OR VALUE -->
<ion-button routerLink="/publication/{{ myValue }}" routerDirection="forward">Go to Detail<ion-button>

```

*Detail.ts*

```
...
selectedId = null;

constructor(private activatedRoute: ActivatedRoute) { }

ngOnInit() {
	// Solution 1 : Get param snapshot value (!! this value will be not refreshed, it's just a snapshot at T instant
	this.selectedId = this.activatedRoute.snapshot.paramMap.get('id');
	
	// Solution 2 : Get current param value
	this.activatedRoute.params.subscribe((params: Params) => {
		this.selectedId = +params['id'];	// '+' is for casting param Id (which is a string at that moment) to number
	});
}
```

### Solution 1 : Service and Resolve Function (legit)

Best practices : [https://ionicacademy.com/pass-data-angular-router-ionic-4/]

*Home.html*

```html
...
<ion-content>
	<ion-button (click)="onOpenDetail()"></ion-button>
</ion-content>
```

*Home.ts*

```javascript
constructor(private router: Router, private dataService: DataService){ }
onOpenDetail() {
	let selectedId = 3;	// here set your selected id
	this.router.navigateByUrl('/detail/'+ selectedId);
}
```

*Detail.html*

```html
...
<ion-content>
	<h2>{{ title }}</h2>
</ion-content>
```

*Detail.ts*

```javascript
export class DetailPage implements OnInit {
	title = '';
	constructor(private route: ActivatedRoute, private router: Router){ }
	
	ngOnInit() {
		if (this.route.snapshot.data['special']) {
			this.title = this.route.snapshot.data['special'].title;
		}
	}
}
```


*dataService.ts*

```
export class DataService {
	dataset = [
		{id: 1, title: 'item 1'},
		{id: 2, title: 'item 2'},
		{id: 3, title: 'item 3'},
		{id: 4, title: 'item 4'},
		{id: 5, title: 'item 5'},
	];
	
	constructor() { }
	getItemById(id) {
		let res;
		this.dataset.filter(item => {
			if(item.id == id) {	// WARNING, do not use === operator
				res = item;
			}
		}
		return res;
	}
}
```

**Create Angular Resolver**

```ionic g service resolver/dataResolver```

*data-resolver.service.ts*

```
constructor(private dataService: DataService) { }
resolve(route: ActivatedRouteSnapshot) {
	const id = route.paramMap.get('id');
	return this.dataService.getItemById(id);
}
```

**Update routing file**

*app-routing.module.ts*

```
const routes: Routes = {
	...
	{	path: 'detail', loadChildren: './detail/detail.module#DetailPageModule},
	{
		path: 'detail/:id,
		resolve: { special: DataResolverService },
		loadChildren: './detail/detail.module#DetailPageModule'
	}
}
```

### Solution 2 : Using Query Params (bad)

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

### Test if param exists

It could be useful to test if a specific parameter exists. For example to determinate which opening mode to use for in a detail page.

```
this.route.paramMap.subscribe((paramMap: ParamMap) => {
	if(paramMap.has('postId')) {
		// EDIT mode
	} else {
		// CREATION mode
	}
});
```

## Child routes
[Back to top](#navigation)    

When you have multiple page sharing the same root, you can regroup them on beside the same parent path. In the following example, we have the following routes 

- /servers => displays servers list
- /servers/new => redirect to new server page
- /servers/id => redirect to the selected server detail page
- /servers/id/edit => redirect to the selected server edit page

We can regroupe them like below :

*app-routing.module.ts*

```
{ path: 'servers', component: ServersComponent, children: [
    { path: '', component: ServerStartComponent},	// default path when no specific server is selected
    { path: 'new', component: ServerEditComponent},
    { path: ':id', component: ServerComponent},
    { path: ':id/edit', component: EditServerComponent},
]}
```
## Reset Routing params
[Back to top](#navigation)

```
import { Subscription } from 'rxjs/Subscription';

export class UserComponent implements OnInit, OnDestroy {
	user: {id: number, name: string};
	paramSubscription: Subscription;

	constructor(private route: ActivatedRoute) {}
	
	ngOnInit() {
		this.paramSubscription = this.route.params
		.subscribe((params: Params) => {
			this.user.id = params[‘id’];
			this.user.name = params[‘name’];
		}
	}

	ngOnDestroy() {
		this.paramSubscription.unsubscribe();
	}
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

## Passing static data to a Route
[Back to top](#navigation)

*app-routing.module.ts*

```
...
const routes: Routes = [
  { path: '', component: HomeComponent, pathMatch: 'full'},
  { path: 'not-found', component: ErrorPageComponent, data: {message: 'page not found'} },
  { path: '**', redirectTo: '/not-found' },
];
```

*error-page.html*

```
<p>{{ errorMessage }}</p>
```

*error-page.ts*

```
import { ActivatedRoute } from '@angular/router';
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-error-page',
  templateUrl: './error-page.component.html',
  styleUrls: ['./error-page.component.scss']
})
export class ErrorPageComponent implements OnInit {
  errorMessage: string;

  constructor(private route: ActivatedRoute) { }

  ngOnInit() {
	//this.errorMessage = this.route.snapshot.data['message'];
	this.route.data.subscribe((data: Data) => {
		this.errorMessage = data['message'];
	});	
  }

}

```

## Passing dynamic data to a Route
[Back to top](#navigation)

*server-resolver.service.ts*

```
import { ServersService } from './../servers.service';
import { Injectable } from "@angular/core";
import { Resolve, RouterStateSnapshot, ActivatedRouteSnapshot } from '@angular/router';
import { Observable } from 'rxjs';

interface ServerInterface {
    id: number;
    name: string;
    status: string;
}

@Injectable()
export class ServerResolver implements Resolve<ServerInterface> {

    constructor(private serverService: ServersService) {}

    resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<ServerInterface> | 
    Promise<ServerInterface> | ServerInterface {
        return this.serverService.getServer(+route.params['id']);
    }
}
```

Adding *resolve* param into routing file

*app-routing.module.ts

```
import { ServerResolver } from './servers/server/server-resolver.service';
...

const routes: Routes = [
  ...,
  { path: 'servers', component: ServersComponent, children: [
    { path: ':id', component: ServerComponent, resolve: {serverParam: ServerResolver}},
    { path: ':id/edit', component: EditServerComponent},
  ]}
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

*server.component.ts*

```
import { ActivatedRoute, Params, Router, Data } from '@angular/router';
import { Component, OnInit } from '@angular/core';
import { ServersService } from '../servers.service';

@Component({
  selector: 'app-server',
  templateUrl: './server.component.html',
  styleUrls: ['./server.component.css']
})
export class ServerComponent implements OnInit {
  server: {id: number, name: string, status: string};

  constructor(private serversService: ServersService,
              private route: ActivatedRoute,
              private router: Router) { }

  ngOnInit() {
    this.route.data.subscribe((data: Data) => {
      this.server = data['serverParam'];	// !! Use the same name as set into the app-routin.module.ts
    });
  }
}

```

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

## Wildcard route
[Back to top](#navigation)

To manage unwanted routes with 404 status, you must define a wildcard route into your *app-routing.module.ts* like below

*app-routing.module.ts*
```
const routes: Routes = [
	{ path: 'home', component: HomeComponent },
	// ... here some other routes
	{ path: 'not-found', component: <YourSpecificPageNotFoundComponent> },
	{ path: '**', redirectTo: '/not-found' }
];
```

**How it work's ?**

First, you need to create a specific route for not existing route (here 'not-found' route) wich load a new component used to load a specific UI.

```
{ path: 'not-found', component: <YourSpecificPageNotFoundComponent> },
```

Next you must specify the wildcard route with ```**``` string wich redirect to the component used to display the 'not found' route

```
{ path: '**', redirectTo: '/not-found' }
```

> **IMPORTANT** : You **MUST** set the wildcard route at the **END** of the app-routing.module.ts file !!

## Using NavController

### Passing Data Ionic 3
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

