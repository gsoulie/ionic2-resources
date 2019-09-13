[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Custom Component

* [Ionic 4 Custom component](#ionic4-custom-component)    
* [Alert component](#alert-component)    

## Ionic 4 custom component

### Note

> **entryComponent** : An entry component is any component that Angular loads imperatively, (which means you’re not referencing it in the template), by type. You specify an entry component by bootstrapping it in an NgModule, or including it in a routing definition.

> To contrast the two types of components, there are components which are included in the template, which are declarative. Additionally, there are components which you load imperatively; that is, entry components.

### Generate component

```ionic g component components/my-component --export```

*note :* It is recommended to put all the component in a **components** directory

### Create component module file

In order to use a component in multiple page, you need to create a *components.module.ts* file at the root of the **components** directory, in which you will export all the components needed to be share.

*components.module.ts*

```
import { IonicModule } from '@ionic/angular';
import { FormsModule } from '@ionic/forms';
import { CommonModule } from '@ionic/common';
import { NgModule } from '@angular/core';
import { MyCustomComponent1 } from './my-custom-component1/my-custom-component1.component';
import { MyCustomComponent2 } from './my-custom-component2/my-custom-component2.component';

@NgModule({
	imports: [
		CommonModule,
		FormsModule,
		IonicModule
	],
	declarations: [
		MyCustomComponent1,
		MyCustomComponent2
	],
	exports: [
		MyCustomComponent1,
		MyCustomComponent2
	],
})
export class ComponentsModule {}
```

**Important :** Don't forget to import ```CommonModule```, ```FormsModule```, ```IonicModule```

### Add custom component to multiple pages

In order to use your customs components in differents pages, you must import the **ComponentsModule** created above in each ```<page>.module.ts``` file like below :

*home.module.ts*

```
@NgModule({
	imports: [
		CommonModule,
		FormsModule,
		IonicModule,
		ComponentsModule,	// <-- Declaration of components.module.ts
		RouterModule.forChild(routes)
	],
	declarations: [HomePage]
})
export class HomePageModule {}
```


*detail.module.ts*

```
@NgModule({
	imports: [
		CommonModule,
		FormsModule,
		IonicModule,
		ComponentsModule,	// <-- Declaration of components.module.ts
		RouterModule.forChild(routes)
	],
	declarations: [DetailPage]
})
export class DetailPageModule {}
```

Then finally simply add your component selector into your ```<page>.page.html```

*home.page.html*

```
<ion-content>
	<app-my-custom-component1 customValue="{{ title }}"></app-my-custom-component1>
</ion-content>
```

### Component sample

*my-component.component.html*

```
<h2>Hellow from component !</h2>
<p> 
Here's some data passed from my parent : 
{{passedData}}
</p>
```

*my-component.component.ts*

```
...
export class MyComponentComponent implements OnInit {
	@Input() passedData: string = '';
	
	constructor(){}
	ngOnInit() {
		console.log('passed data : ' + this.passedData); // => get Undefined !!!!
	}
	
	ngAfterContentInit() {
		console.log('passed data : ' + this.passedData);
	}
}
```

> **IMPORTANT** : *@Input()* only takes **string** type   
> **IMPORTANT** : passed data are available only in *ngAfterContentInit* or *ngAfterViewInit*    

*home.page.html*

```
<app-my-component passedData="{{ someDataFromBinding }}"></app-my-component>
<app-my-component passedData="hello"></app-my-component>
```


## Create custom component
First, create your custom component with ```ionic g component components/my-compo --export```, then add its declaration in your *app.module.ts* file

*View file*

```html
<h1>{{ title }}</h1>
<ion-item *ngFor="let i of items">
	<p>{{ i.name }}</p>
</ion-item>
```

*Controller file*

```javascript
import { Component, Input, OnInit } from '@angular/core';
@Component({
  selector: 'compo',
  templateUrl: 'compo.html'
})
export class CompoComponent implements OnInit{
  items: any[] = [];
  
  @Input() title: string = "";  // @Input allow to get parameter from parent page
  
  constructor(public navCtrl: NavController,public navParams: NavParams, dbService: DatabaseService) { 
  	console.log("title = " + title); // !!! Doesn't return anything, the constructor
  }
  
  ngOnInit(){
  	console.log("title = " + title); // Ok here !!
  	this.onFetchData();
  }
  // Get data from WS
  onFetchData(){
  	this.dbService.fetch()
	.then(data => this.items = data);
  }
  
```

Notice that in the code we are using **@Input**, this allow to get parameters from the parent's page. In this example, we create an input parameter which give us the title to set in the component

**IMPORTANT** : Custom component will not have the *ion-* view lifecycle events. In this case, you'll need to use the raw angular *ngOnInit*, *ngAfterInit* hooks.

## Using custom component in another page

Add your custom component in your view file by simply adding a tag with the name of the component

*home.html*

```html
<ion-header>
  <ion-navbar color="maintheme">
    <ion-title>Liste des jeux</ion-title>
  </ion-navbar>
</ion-header>

<ion-content class="window">
  <compo title="My custom title"></compo>
</ion-content>

```

Here you can see **title** parameter which provides the title for the component.

## Call custom component functions from parent

Previous, we have see that we can pass parameters to the component with the **@Input** decorator, but sometimes we need to call  component's functions from the parent Page. 
We can access to a component and its methods by using **@ViewChild** decorator. 

[@ViewChild documentation](http://learnangular2.com/viewChild/)

In our example, we want to call the *onFectchData* component function, from the parent page. 

*Home.ts*

```javascript
import { CompoComponent } from './../../components/compo/compo';
import { Component, ViewChild, OnInit } from '@angular/core';
import { NavController, NavParams } from 'ionic-angular';

@Component({
  selector: 'page-tab-ludo',
  templateUrl: 'tab-ludo.html'
})
export class TabLudoPage {
  @ViewChild(CompoComponent, {static: false}) myCompo: CompoComponent;	// Get access to the custom component

  constructor(public navCtrl: NavController, public navParams: NavParams) {}

  ngAfterViewInit(){
    this.myCompo.onFetchData();	// Call the onFetchData function of the custom component
  }
}

```

## Alert component
[Back to top](#custom-component)

### Solution 1 : Adding component by using ngIf 

With this solution, the custom component is initially attached to the DOM and displayed by using *ngIf*

Note : Do not forget to import the component in the *app.module.ts*

*alert.component.html*

```
<div class="backdrop" (click)="onClose()"></div>
<div class="alert-box">
	<p>{{ message }}</p>
	<div class="alert-box-actions">
		<ion-button class="btn btn-primary" (click)="onClose()">Close</ion-button>
	</div>
</div>
```

*alert.component.ts*

```
@Component({
	selector: 'app-alert',
	templateUrls: './alert.component.html',
	styleUrls: ['./alert.componentcss']
})
export class AlertComponent {
	@Input() message: string;
	@Output() close = new EventEmitter<void>();
	
	onClose() {
		this.close.emit();
	}
}
```

*alert.component.css*

```
.backdrop {
	position: fixed;
	top: 0;
	left: 0;
	width: 100vw;
	height: 100vw;
	background: rgba(0, 0, 0, 0.75);
	z-index: 50;
}
.alert-box {
	position: fixed;
	top: 30vh;
	left: 20vw;
	width: 60vw;
	padding: 16px;
	z-index: 100;
	background: white;
	box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
}
.alert-box-actions {
	text-align: right;
}
```

*parent.component.html*

```
<app-alert [message]="error" *ngIf="error" (close)="onHandleError()"></app-alert>
```

*onHandleError()* is a function in the parent component

### Solution 2 : Adding component dynamically by code
[Back to top](#custom-component)

With this solution, the custom component is not initially attached to the DOM. Instead, it's created dynamically by code. So you have to use *ComponentFactoryResolver* to let Angular create the component instance.

To atatch the component to the DOM, Angular needs a view container ref like ```<ng-template myCompoRef></ng-template>```

First remove the following declaration in the view file 

*parent.component.html*

```
<app-alert [message]="error" *ngIf="error" (close)="onHandleError()"></app-alert>
```

In order to create a view container reference, we are going to create a new directive (create a new file in your components directory for example)

*placeholder.directive.ts*

```
import { Directive, ViewContainerRef } from '@angular/core';

@Directive({
	selector: '[appPlaceholder]'
})
export class PlaceholderDirective {
	constructor(public viewContainerRef: ViewContainerRef) {}
}
```

**Important** : do not forget to expose ```viewContainerRef``` as a **public** property

Now add this reference in the view file

*parent.component.html*

```
<ng-template appPlaceholder></ng-template>
```

Now we are calling AlertComponent by code

*parent.component.ts*

```
import { AlertComponent } from '../components/alert.component.ts';
import { ComponentFactoryResolver, ViewChild } from '@angular/core';
import { PlaceholderDirective } from '../components/placeholder.directive.ts';

export class ParentComponent implements OnDestroy {
	@ViewChild(PlaceholderDirective, {static: false}) alertHost: PlaceholderDirective;
	private closeSub: Subscription;
	
	constructor(private componentFactoryResolver: ComponentFactoryResolver) {}
	
	ngOnDestroy() {
		if(this.closeSub) {
			this.closeSub.unsubscribe();
		}
	}

	onShowErrorAlert(message: string) {
		const alertCmpFactory = this.componentFactoryResolver
		.resolveComponentFactory(AlertComponent);

		const hostViewContainerRef = this.alertHost.viewContainersRef;	// Reference of the placeholder
		hostViewContainerRef.clear();

		const componentRef = hostViewContainerRef.createComponent(alertCmpFactory);	// create the custom component to the place specified by hostViewContainerRef

		// manage component parameters and lsitener
		componentRef.instance.message = messsage;
		this.closeSub = componentRef.instance.close.subscribe(() => {
			this.closeSub.unsubscibe();
			hostViewContainerRef.clear();	// clean composant reference
		});	
	}
}
```

> To avoid a execution error, you need to add your component declaration in the ```entryComponents``` section of your *app.module.ts*

*app.module.ts*

```
entryComponents: [
	AlertComponent
]
```
