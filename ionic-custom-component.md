[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Custom Component

* [Ionic 4 Custom component](#ionic4-custom-component)    

## Ionic 4 custom component

### Generate component

```ionic g component components/my-component --export```

### Include child component to parent

In order to use your component into parent element, you must modify your **parent.module.ts** file like below :

```
import { MyComponentComponent } from './../components/my-component/my-component.component';
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { Routes, RouterModule } from '@angular/router';

import { IonicModule } from '@ionic/angular';

import { HomePage } from './home.page';

const routes: Routes = [
  {
    path: '',
    component: HomePage
  }
];

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    IonicModule,
    RouterModule.forChild(routes)
  ],
  entryComponents: [MyComponentComponent],
  declarations: [HomePage, MyComponentComponent]
})
export class HomePageModule {}
```

### Using component

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
  @ViewChild(CompoComponent) myCompo: CompoComponent;	// Get access to the custom component

  constructor(public navCtrl: NavController, public navParams: NavParams) {}

  ngAfterViewInit(){
    this.myCompo.onFetchData();	// Call the onFetchData function of the custom component
  }
}

```
