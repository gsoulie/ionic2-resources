[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Custom Component

* [Create custom component](#create-custom-component)    
* [Alert component](#alert-component)    
* [Working example](#working-example)    
* [Popover example capacitor](#popover-example)    

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

```typescript
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

```typescript
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

```html
<div class="backdrop" (click)="onClose()"></div>
<div class="alert-box">
	<p>{{ message }}</p>
	<div class="alert-box-actions">
		<ion-button class="btn btn-primary" (click)="onClose()">Close</ion-button>
	</div>
</div>
```

*alert.component.ts*

```typescript
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

```css
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

```html
<app-alert [message]="error" *ngIf="error" (close)="onHandleError()"></app-alert>
```

*onHandleError()* is a function in the parent component

### Solution 2 : Adding component dynamically by code
[Back to top](#custom-component)

[Official Documentation](https://angular.io/guide/dynamic-component-loader)

With this solution, the custom component is not initially attached to the DOM. Instead, it's created dynamically by code. So you have to use *ComponentFactoryResolver* to let Angular create the component instance.

To atatch the component to the DOM, Angular needs a view container ref like ```<ng-template myCompoRef></ng-template>```

First remove the following declaration in the view file 

*parent.component.html*

```html
<app-alert [message]="error" *ngIf="error" (close)="onHandleError()"></app-alert>
```

In order to create a view container reference, we are going to create a new directive (create a new file in your components directory for example)

*placeholder.directive.ts*

```typescript
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

```html
<ng-template appPlaceholder></ng-template>
```

Now we are calling AlertComponent by code

*parent.component.ts*

```typescript
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

```typescript
entryComponents: [
	AlertComponent
]
```

## Working example
[Back to top](#custom-component)

### Custom component 

**components/toolbar**

*toolbar.component.html*

```html
<ion-toolbar>
  <ion-grid>
    <ion-row class="ion-align-items-center" class="ion-text-center">
      <ion-col class="ion-text-center">
        <ion-button fill="clear" (click)="onFilterByColor('blanc')">
          <div class="color-blanc color-div"></div>
        </ion-button>
      </ion-col>
      <ion-col class="ion-text-center">
        <ion-button fill="clear" (click)="onFilterByColor('bleu')">
          <div class="color-bleu color-div"></div>
        </ion-button>
      </ion-col>
      <ion-col class="ion-text-center">
        <ion-button fill="clear" (click)="onFilterByColor('multi')">
          <div class="color-multi color-div"></div>
        </ion-button>
      </ion-col>
    </ion-row>
  </ion-grid>
</ion-toolbar>
```

*toolbar.component.ts*

```typescript
import { Component, OnInit, Input, AfterContentInit, Output } from '@angular/core';
import { EventEmitter } from '@angular/core';

@Component({
  selector: 'app-toolbar',
  templateUrl: './toolbar.component.html',
  styleUrls: ['./toolbar.component.scss'],
})
export class ToolbarComponent implements OnInit, AfterContentInit {

  @Input() collectionName: string = '';	// passed parameter from parent
  @Output() colorSelected: EventEmitter<string> = new EventEmitter();	// Event emitter

  constructor() { }

  ngOnInit() {
    console.log('passed data : ' + this.collectionName); // => get Undefined !!!!
  }

  ngAfterContentInit() {
    console.log('passed data : ' + this.collectionName);
  }

  // Event emitter
  onFilterByColor(couleur: string) {
    this.colorSelected.emit(couleur);
  }

}

```

**components/components.module.ts**

*components.module.ts*

```typescript
import { IonicModule } from '@ionic/angular';
import { CommonModule } from '@angular/common';
import { NgModule } from '@angular/core';
import { ToolbarComponent } from './toolbar/toolbar.component';

@NgModule({
	imports: [
		CommonModule,
		IonicModule
	],
	declarations: [
		ToolbarComponent
	],
	exports: [
		ToolbarComponent
	],
})
export class ComponentsModule {}
```

### Add component in parent

*home.module.ts*

```typescript
import { ComponentsModule } from './../components/components.module';
import { IonicModule } from '@ionic/angular';
import { RouterModule } from '@angular/router';
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { HomePage } from './home.page';

@NgModule({
  imports: [
    IonicModule,
    CommonModule,
    FormsModule,
    ComponentsModule,	// <---- Import custom components
    RouterModule.forChild([{ path: '', component: Tab2Page }])
  ],
  declarations: [HomePage]
})
export class HomePageModule {}

```

*home.component.html*

```html
<ion-header no-border>
  <ion-toolbar color="primary">
    <ion-title>
      Wish list
    </ion-title>
  </ion-toolbar>
  <app-toolbar collectionName="wish" (colorSelected)="onFetchData($event)"></app-toolbar>
</ion-header>
<ion-content>
...
</ion-content>
```

*home.component.ts*

```typescript
import { DataService } from './../services/data.service';
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-home',
  templateUrl: 'home.page.html',
  styleUrls: ['home.page.scss']
})
export class HomePage implements OnInit {

  items;

  constructor(private dataService: DataService) {}

  /**
   * 'param' from event emitter
   **/
  onFetchData(param) {
    this.items = [];

      this.dataService.fetchCardByColor(param)
      .subscribe(data => {...});
      });
  }
}

```

## Popover example
[Back to top](#custom-component)

*components/popover/popover.component.html*

```html
<div class="popover-container">
  <ion-button fill="clear" (click)="about()">More information</ion-button>
</div>
```

In this sample, we don't need to create and use a *components.module.ts*

*home.module.ts*

```typescript
import { PopoverComponent } from './../components/popover/popover.component';
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { IonicModule } from '@ionic/angular';
import { FormsModule } from '@angular/forms';
import { RouterModule } from '@angular/router';

import { HomePage } from './home.page';

@NgModule({
  entryComponents: [PopoverComponent],	// <---
  imports: [
    CommonModule,
    FormsModule,
    IonicModule,
    RouterModule.forChild([
      {
        path: '',
        component: HomePage
      }
    ])
  ],
  declarations: [HomePage, PopoverComponent]	// <---
})
export class HomePageModule {}

```

*home.ts*

```typescript
async about(ev: any) {
    const popover = await this.popoverController.create({
      component: PopoverComponent,
      event: ev,
      backdropDismiss: true,
      showBackdrop: true,
      translucent: false
    });
    return await popover.present();
  }
```

> **IMPORTANT** : Do not forget to pass *$event* in your function call inside your view file. Else the popover will be always centered in the viewport
