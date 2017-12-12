# Custom Component

[Back to top](#ionic-2)  


## Create custom component
First, create your custom component with ```ionic g component compo```, then add its declaration in your *app.module.ts* file

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
