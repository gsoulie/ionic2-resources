[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# UI Components

* [ion-button](#ion-button)    
* [modal](#modal)    
* [ion-select](#ion-select)    
* [ion-item](#ion-item)    
* [ion-list](#ion-list)  
* [pagination](#pagination)  
* [ion-label](#ion-label)    
* [searchbar](#searchbar)    
* [list filtering](#high-performance-list-filtering)    
* [ion-tab](#ion-tab)       
* [tab icon](#tab-icon)    
* [ion-fab](#floating-button)  
* [chart](#chart)    
* [ion-grid](#grid)    
* [ion-toggle](#toggle)    
* [dynamic style](#dynamic-style)    
* [Toggle menu](#toggle-menu)    
* [Expandable header](#expandable-header)    
* [List accordion](#list-accordion)    
* [Picker](#picker)     
* [alert controller](#alert-controller)    
* [round progress bar](#round-progress-bar)     
* [ion-slide](#ion-slide)    
* [Hiding header on scroll](#hiding-header-on-scroll)     
* [Notification badge on icon](#notification-badge-on-icon)    
* [Input with autocomplete datalist](#input-with-autocomplete-datalist)     
* [Action sheet customization](#action-sheet-customization)        
* [ion-toast](#ion-toast)       
* [ion-sliding-item swipe to delete](#ion-sliding-item-swipe-to-delete)       

## ion-button
[Back to top](#ui-components)  

### options

```
<!-- Expand -->
<ion-button expand="full">Full Button</ion-button>
<ion-button expand="block">Block Button</ion-button>

<!-- Round -->
<ion-button shape="round">Round Button</ion-button>

<!-- Fill -->
<ion-button expand="full" fill="outline">Outline + Full</ion-button>
<ion-button expand="block" fill="outline">Outline + Block</ion-button>
<ion-button shape="round" fill="outline">Outline + Round</ion-button>

<!-- Icons -->
<ion-button>
  <ion-icon slot="start" name="star"></ion-icon>
  Left Icon
</ion-button>

<ion-button>
  Right Icon
  <ion-icon slot="end" name="star"></ion-icon>
</ion-button>

<ion-button>
  <ion-icon slot="icon-only" name="star"></ion-icon>
</ion-button>

<!-- Sizes -->
<ion-button size="large">Large</ion-button>
<ion-button>Default</ion-button>
<ion-button size="small">Small</ion-button>
```
### disable text-transform

```<ion-button style="text-transform: none;">```

### Mutli-line button

*view file*
```
<ion-button class="btnValueChecked allowMultipleLines" fill="clear">My very very long text button with multiple line</ion-button>
```

*style file*
```
.btnValueUnchecked {
    border-radius: 50px;
    height: 60px;
    border: 2px solid $theme-color;
    background-color: white;
    color: black;
    padding-right: 8px;
    padding-left: 8px;

    text-align: center;
    display: inline-grid;
    vertical-align:middle;

    &.allowMultipleLines {
        text-overflow: visible;
        white-space: inherit;
        min-height: 60px;
        height: auto;
    }
}
.btnValueChecked {
    border-radius: 50px;
    height: 60px;
    background: linear-gradient(154deg, rgba(0,157,190,1) 52%, rgba(29,164,152,1) 100%);
    box-shadow: 0 20px 40px 0 rgba(0,157,190,0.3);
    color: white;
    padding-right: 8px;
    padding-left: 8px;

    text-align: center;
    display: inline-grid;
    vertical-align:middle;
    
    &.allowMultipleLines {
        text-overflow: visible;
        white-space: inherit;
        min-height: 60px;
        height: auto;
    }
}
```

## Modal
[Back to top](#ui-components)  

### Syntax

*Parent Controller*

````
import { LoginModalComponent } from './../login-modal/login-modal.component';

export class HomePage implements OnInit {
	constructor(private modalCtrl: ModalController) {}
	
	async onLogin() {
	 const modal = await this.modalCtrl.create({
	    component: LoginModalComponent,
	    cssClass: 'login-modal'
	 });
	 modal.onDidDismiss()
	 .then((detail: any) => {
	    if (detail.data === null) {
	    } else {
		this.userLogged = detail.data;
	    }
	 });
		 return await modal.present();
	 }
 }
````

*modal controller*

````
constructor(private modalCtrl: ModalController) {}

onClose() {
	this.modalCtrl.dismiss(myUserIsLogged)
}
````

### Other example

Consider that we have a page (UserListPage for example) with a button wich open a modal based on UserPage controller.

*UserListPage Controller file*

```javascript
import { ModalController } from 'ionic/angular';
...
users: User[];

constructor(private modalCtrl: ModalController){}

openMyModal(user: User){
	const modal = this.modalCtrl.create(UserPage, username);
	modal.present();
	modal.onDidDismiss((remove: boolean) => {
		//call the dismiss method of the UserPage controller
		if(remove) {
			// if remove = true then remove user from database
			this.myService.removeUser(user);

			// refresh user list : 2 methods
			// 1 - reload all the list
			this.users = this.myService.getAllUsers();
			
			// 2 - remove only deleted user from users array
			const position = this.users.findIndex((userEl: User) => {
				return userEl.id == user.id;
			});
			this.users.splice(position, 1);
			
		}
	});
}
...
```

*UserPage controller file*

```javascript
...
constructor(private viewCtrl: ViewController, private navParams: NavParams){}

ionViewDidLoad(){
	...
}

/**
 * @param{Boolean} remove : if true, remove the user from database
 */
onClose(remove = false){
	this.viewCtrl.dismiss(remove);	
}
...
```


## ion-select
[Back to top](#ui-components)  

*View file*
```xml
 <ion-item>
    <ion-label>Profession</ion-label>
	 <ion-select (ionChange)="onFiltering($event.detail.value)"
      	interface="action-sheet" placeholder="Select your item" [(ngModel)] ="selectedItem" name="cbo">
        <ion-select-option *ngFor="let item of itemList" [value]="item.id">{{ item.id }} - {{ item.name }}</ion-select-option>
      </ion-select>
 </ion-item>
```

*Controller file*

```javascript
  onFilteringTag(selectedItem){
    this.currentTag = selectedTag;	// contains item.id

    if(selectedItem === null){
	// do some stuff
    } else {
     	// other stuff
    }
  }
```
### Positionning issues

https://stackoverflow.com/questions/56596932/ionic-4-customize-ion-select


## ion-item
[Back to top](#ui-components)  

- show right arrow : add ```detail``` property on ion-item ```<ion item detail>```
- hide right arrow : add ```detail="false"``` property on ion-item  ```<ion item detail="false">```
- hide item line : add ```lines="none"``` property on ion-item  ```<ion item lines="none">```

*Different type of lines*

```
<!-- Item Inset Lines -->
<ion-item lines="inset">
  <ion-label>Item Lines Inset</ion-label>
</ion-item>

<!-- Item Full Lines -->
<ion-item lines="full">
  <ion-label>Item Lines Full</ion-label>
</ion-item>

<!-- Item None Lines -->
<ion-item lines="none">
  <ion-label>Item Lines None</ion-label>
</ion-item>

<!-- List Full Lines -->
<ion-list lines="full">
  <ion-item>
    <ion-label>Full Lines Item 1</ion-label>
  </ion-item>

  <ion-item>
    <ion-label>Full Lines Item 2</ion-label>
  </ion-item>
</ion-list>
```

### ion-item slots

Since Ionic 4, ion-item uses *slot* in replacement of *item-left* and *item-right*

Now use slots instead :

```
<ion-button slot="start">
<ion-button slot="end">
<ion-icon slot="icon-only">
```

### ion-item router-direction

It is possible to add a *router-direction* when clicking on ion-item. It's value could be : "back" | "forward" | "root"
### ion-item with multiple lines

```
<ion-item>
    <ion-label text-wrap>
    ...
    </ion-label>
</ion-item>
```
## ion-list
[Back to top](#ui-components)  

### Show list right arrow

To show the right arrow (like ios list item), just add ```detail``` property on ion-item :

*View File*

```xml
<ion-item *ngFor="let item of group.patients" detail>{{ item }}</ion-item>
```

And modify your *variable.sass* file to reflect the following 

```javascript
// App iOS Variables
// --------------------------------------------------
// iOS only Sass variables can go here
$item-ios-detail-push-show: true;
```

After that refresh your browser

### Standard dynamic ion-list item

```xml
<ion-list> 
    <ion-item *ngFor="let item of items">{{item.fullname}}</ion-item> 
</ion-list>
```

For clickable list you have to use **button ion-item**

```xml
<ion-list> 
    <button ion-item *ngFor="let item of items">{{item.fullname}}</button> 
</ion-list>
```

### Add filter on ion-list

Consider a ion-list in which we want to hide every items which property "deleted" is set to true

```xml
<ion-list>
  <ion-item-sliding *ngFor="let item of getActiveItems()" #slidingItem>
    <ion-item>{{item.fach}} ({{item.kuerzel}})</ion-item>
    <ion-item-options>
      <ion-button (click)="showEditModal(item, slidingItem)"><ion-icon name="create"></ion-icon>Bearbeiten</ion-button>
      <ion-button danger (click)="doConfirm(item, slidingItem)"><ion-icon name="trash"></ion-icon>Löschen</ion-button>
    </ion-item-options>
  </ion-item-sliding>
</ion-list>
```

```javascript
@Component(
    // ...
)
export class TestPage {
    // ...
    getActiveItems() {
        // NOTE: Returning a new array might mess up the change detection of Ng2.
        // TODO: Use an existing array instead of returning a new one each time.
        return this.items.filter(item => !item.deleted);
    }
}
```

Second solution, in case that you prefer to do the filtering in the template then you can do it this way:

```xml
<ion-list>
  <template ngFor #item="$implicit" [ngForOf]="items">
    <ion-item-sliding *ngIf="!item.deleted" #slidingItem>
      <ion-item>{{item.fach}} ({{item.kuerzel}})</ion-item>
      <ion-item-options>
        <ion-button (click)="showEditModal(item, slidingItem)"><ion-icon name="create"></ion-icon>Bearbeiten</ion-button>
        <ion-button danger (click)="doConfirm(item, slidingItem)"><ion-icon name="trash"></ion-icon>Löschen</ion-button>
      </ion-item-options>
    </ion-item-sliding>
  </template>
</ion-list>
```


### Remove item from list

*View file*

```xml
<ion-list>
    <ion-item-sliding *ngFor="let post of posts">
    	<ion-item>
    	...
    	</ion-item>
    	<ion-item-options>
    	    <ion-button danger (click)="removePost(post)" icon-only>
    	    	<ion-icon name="remove"></ion-icon>
    	    </ion-button>
    	</ion-item-options>
    </ion-item-sliding>
</ion-list>
```

*Controller file*

```javascript
removePost(post){
    let index = this.posts.indexOf(post);

    if(index > -1){
      this.posts.splice(index, 1);
    }
}
```

### ion-item-sliding

```xml
<ion-content class="home">
  <ion-list>
    <ion-item-sliding  *ngFor="#item of notes" >
      <button ion-item (click)="noteSelected(item)">
        <ion-item-content>
          <h2>{{item.title}}</h2>
          <p>{{item.text | truncate: 20}}</p>
        </ion-item-content>
      </button>

      <ion-item-options>
        <ion-button color="darkgrey" (click)="removeNote(item)" class="btn"><ion-icon name="trash"></ion-icon>Delete</ion-button>
      </ion-item-options>
    </ion-item-sliding>
  </ion-list>
</ion-content>
```

### Lazy loading on scroll

*View file*

```
<ion-header>
  ...
</ion-header>

<ion-content>
  <ion-list *ngFor="let item of items">
    
  </ion-list>
  <ion-infinite-scroll (ionInfinite)="scrollHandler($event)">
    <ion-infinite-scroll-content></ion-infinite-scroll-content>
  </ion-infinite-scroll>
</ion-content>
```

*Controller file*

```
import { IonInfiniteScroll } from '@ionic/angular';

@Component({
  selector: 'app-home',
  templateUrl: 'home.page.html',
  styleUrls: ['home.page.scss'],
})
export class HomePage {

    // variables pour la gestion du lazy loading
    @ViewChild(IonInfiniteScroll, {static: false}) infiniteScroll: IonInfiniteScroll;
    
    constructor(...) {
      this.getData();
    }
    
    
  /**
   * Infinite scroll listener
   * @param infiniteScroll 
   */
  scrollHandler(event) {
    setTimeout(() => {
      // Update data only when end of page is detected
      if ((window.innerHeight + window.scrollY) >= document.body.offsetHeight) {
        this.getData();
      }
      event.target.complete();
    }, 500);
  }
  
  /**
   * Fetching data from Firebase 20 by 20 items
   */
  async getData() {
    if (this.filteringMode === false) {
      this.deckDataList = this.afDB.list(cst.TBL_DECK, ref => ref.orderByChild('deckName')); 
      this.deckObservable = this.deckDataList.valueChanges();
      this.deckObservable.subscribe((res) => {
        this.decks = res as any[];
        this.decks.push({deckName: 'Tous'});
      });

      const loader = await this.loadingCtrl.create({
        message: 'Loading...'
      });
      await loader.present();

      // fetching data 20 by 20
      if (this.lastItem === null) {
        this.dataList = this.afDB.list(cst.TBL_MATCH, ref => ref.orderByChild('id').limitToLast(20));
      } else {
        this.dataList = this.afDB.list(cst.TBL_MATCH, ref => ref.orderByChild('id').endAt(this.lastItem).limitToLast(20));
      }
      this.matchs = this.dataList.valueChanges()
      .pipe(map(items => items.sort().reverse()));

      try {
        this.matchs.subscribe((res) => {
            let temp = res as any[];
            this.lastItem = temp[temp.length - 1].id;	// memorize last item fetched for the next fetching

            // remove first item if it is not the first fetching (else it is added twice in the dataset)
            if (this.matchs2.length > 0) { 
              temp.splice(0, 1);
            }
            temp.forEach(match => {
              this.matchs2.push(match);
            });
            loader.dismiss();
            console.table(this.matchs2);
        });
      } catch (e) {
        loader.dismiss();
      }
    }
  }
}
```


### Inifinite scroll in ion-list

#### Solution 1

[link : Scroll performance](http://www.joshmorony.com/boosting-scroll-performance-in-ionic-2/)
[link : virtual-scroll official doc](http://ionicframework.com/docs/v2/api/components/virtual-scroll/VirtualScroll/)

**Create new project**

```
ionic start virtual-scroll tabs --v2
```

**Create Data**

Modify Page1 and Page2 controller with :

```javascript
import {Component} from '@angular/core';

@Component({
  templateUrl: 'build/pages/page1/page1.html'
})
export class Page1 {
  constructor() {
 
    this.items = [];

    for(let i = 0; i < 2000; i++){

        let item = {
            title: 'Title',
            body: 'body',
            avatarUrl: 'https://avatars.io/facebook/random'+i
        };

        this.items.push(item);
    }

  }
}
```

**Create List**

First we’re going to set up a standard list to display the data we created in the constructor.

Modify **page1.html** to reflect the following:
```xml
<ion-header>
	<ion-navbar>
	  <ion-title>
	    Standard Scroll
	  </ion-title>
	</ion-navbar>
</ion-header> 
<ion-content class="page1">
 
  <ion-list>
    <ion-item *ngFor="let item of items">
      <ion-avatar item-left>
        <ion-img [src]="item.avatarUrl"></ion-img>
      </ion-avatar>
 
      <h2>{{item.title}}</h2>
      <p>{{item.body}}</p>
    </ion-item>
  </ion-list>
</ion-content>
```

In this case we are just using the standard ```*ngFor``` directive to loop over all of the items in our array and create list items for them. Since we are not using virtual scroll, **this means that DOM elements are going to be created for every single item !**. If you try to run the application now, you will likely even notice there is a considerable amount of lag just in loading the app.

**Create a Virtual Scrolling List**

Now we’re going to create a list that uses virtual scroll.

Modify **page2.html** to reflect the following:

```xml
<ion-header>
	<ion-navbar>
	  <ion-title>
	    Virtual Scroll
	  </ion-title>
	</ion-navbar>
</ion-header> 
<ion-content class="page2">
    <ion-list [virtualScroll]="items">
 
      <ion-item *virtualItem="let item">
        <ion-avatar item-left>
          <ion-img [src]="item.avatarUrl"></ion-img>
        </ion-avatar>
 
        <h2>{{item.title}}</h2>
        <p>{{item.body}}</p>
      </ion-item>
    </ion-list>
</ion-content>
```
The syntax is a little different, this time we are using [virtualScroll] and *virtualItem, but the concept is the same. We are setting up some data that will be looped over for the list. The only difference now is that this list will now use virtual scrolling instead of the standard scrolling.

It’s important for the virtual scroll to know approximately how big your items will be, since it needs to know how many items would be required to fill up the screen. You can help this process by specifying an approxItemWidth and approxItemHeight

#### Solution 2

[link : Infinite scroll](http://ionicframework.com/docs/v2/api/components/infinite-scroll/InfiniteScroll/)

*View file*

```xml
<ion-content>
 <ion-list>
   <ion-item *ngFor="let i of items"></ion-item>
 </ion-list>

 <ion-infinite-scroll (infinite)="doInfinite($event)">
   <ion-infinite-scroll-content
      loadingSpinner="bubbles"
      loadingText="Loading more data...">
    </ion-infinite-scroll-content>
 </ion-infinite-scroll>
</ion-content>
```

*Controller file*

```javascript
@Component({...})
export class NewsFeedPage {

  constructor() {
    this.items = [];
    for (var i = 0; i < 30; i++) {
      this.items.push( this.items.length );
    }
  }

  doInfinite(infiniteScroll) {
    console.log('Begin async operation');

    setTimeout(() => {
      for (var i = 0; i < 30; i++) {
        this.items.push( this.items.length );
      }

      console.log('Async operation has ended');
      infiniteScroll.complete();
    }, 500);
  }
}
```

### Set colour dynamically of each ion-item

*View file*

```xml
<ion-list>
	<ion-item *ngFor="let item of items" (click)="openDetail(item.idDevice, item.nom)">
		<ion-button color="clear" slot="end" (click)="pickDevice($event,item.idDevice)">
			<ion-icon [style.color]="item.disponible ? '#00CC00' : '#CD1625'" name="phone-portrait"></ion-icon>
		</ion-button>
		<h2>{{item.nom}}</h2>
	</ion-item>
</ion-list>
```

**IMPORTANT** It's important to set colour by using hexadecimal string instead of standard colour name like 'primary', 'danger' etc... because it's actually doesn't work.

*Controller file*

```javascript
export class Page1 {
  public items: any[];

  constructor(public nav: NavController) {
    this.nav = nav;

    let dataset = [{
      idDevice: "1",
      nom: "iPhone 4S",
      disponible: true
    },{
      idDevice: "2",
      nom: "iPad2",
      disponible: true
    },{
      idDevice: "3",
      nom: "Mac Mini 1",
      disponible: false
    },{
      idDevice: "4",
      nom: 'Mac Book Pro 17"',
      disponible: false
    },{
      idDevice: "5",
      nom: "Nexus 4",
      disponible: true
    }];

    this.items = dataset;
  }

  openDetail(_idDevice, _deviceName){
    this.nav.push(FichePage,{idDevice: _idDevice, deviceName: _deviceName});
  }
}
```

*Change button backgroundColor dynamically*

```xml
<ion-button[style.backgroundColor]="enable ? '#00CC00' : '#FF0000'">Test</ion-button>
```


### Pull to refresh

*View file*

```xml
<ion-content padding>
  <ion-refresher (ionRefresh)="doRefresh($event)">
    <ion-refresher-content></ion-refresher-content>
  </ion-refresher>
  <ion-list>
    <button ion-item *ngFor="let user of users" (click)="goToDetails($event, user.login)">
      <ion-avatar slot="start">
        <img [src]="user.avatar_url">
      </ion-avatar>
      <h2>{{ user.login }}</h2>
      <ion-icon md="md-arrow-forward" slot="end"></ion-icon>
    </button>
  </ion-list>
</ion-content>
```

*Controller file*

```javascript
doRefresh(refresher){
    this.users = getUsers();
    setTimeout(() => { refresher.complete(); console.log('Async operation has ended'); }, 2000); 
  }
```


### List with dividers

*View file*

```xml
<ion-header>
  <ion-navbar>
    <ion-title>Contacts</ion-title>
  </ion-navbar>
</ion-header>
 
<ion-content>
    <ion-item-group *ngFor="let group of groupedContacts">
      <ion-item-divider light>{{group.letter}}</ion-item-divider>
      <ion-item *ngFor="let contact of group.contacts">{{contact}}</ion-item>
    </ion-item-group>
</ion-content>
```

*Controller file*

```javascript
import { Component } from '@angular/core';
import { IonicPage, NavController } from 'ionic-angular';
 
@IonicPage()
@Component({
  selector: 'page-contacts',
  templateUrl: 'contacts.html'
})
export class Contacts {
 
    contacts;
    groupedContacts = [];
 
    constructor(public navCtrl: NavController) {
 
        this.contacts = [
            'Kate Beckett',
            'Richard Castle',
            'Alexis Castle',
            'Lanie Parish',
            'Javier Esposito',
            'Kevin Ryan',
            'Martha Rodgers',
            'Roy Montgomery',
            'Jim Beckett',
            'Stana Katic',
            'Nathan Fillion',
            'Molly Quinn',
            'Tamala Jones',
            'Jon Huertas',
            'Seamus Dever',
            'Susan Sullivan'
        ];
 
        this.groupContacts(this.contacts);
    }
 
    groupContacts(contacts){
 
        let sortedContacts = contacts.sort();
        let currentLetter = false;
        let currentContacts = [];
 
        sortedContacts.forEach((value, index) => {
 
            if(value.charAt(0) != currentLetter){
                currentLetter = value.charAt(0);
 
                let newGroup = {
                    letter: currentLetter,
                    contacts: []
                };
 
                currentContacts = newGroup.contacts;
                this.groupedContacts.push(newGroup);
            } 
 
            currentContacts.push(value);
        });
    }
    
    //Searchbar adaptation
    onSearchPatient(ev: any){
        this.initializeItems();
        this.groupedPatients = [];
        let val = ev.detail.value;
        var temp = [];

        if (val && val.trim() != '') {
            this.patients = this.patients.filter((item) => {
                if (item.nom.toLowerCase().indexOf(val.toLowerCase()) > -1 || item.prenom.toLowerCase().indexOf(val.toLowerCase()) > -1) {
              temp.push(item);
            }
        })
            this.groupPatients(temp);
        } else {
           this.groupPatients(this.patients);
      }    
  }
}
```

**Other example on complex list**

*JSON objects list*

```javascript
...

this.patients = [{nom:"ABBANTI", prenom:"OLIVIER", ville: "Castelnau-le-lez (34)"},{nom:"ABIDAL",  prenom:"GEORGES", ville: "Montpellier (34)"},
    {nom:"AVRIL", prenom:"GERARD", ville: "Lattes (34)"},{nom:"BARLOT",prenom:"NICOLAS", ville: "Pérols (34)"},{nom:"BOURDIN",prenom:"GAETAN", ville: "Maugio (34)"},
    {nom:"BOURET",prenom:"FREDERIC", ville: "Palavas (34)"},{nom:"CASAL",prenom:"GERARD", ville: "Montpellier (34)"},{nom:"CATHERON",prenom:"DAVID", ville: "Montpellier (34)"},
    {nom:"DAUPHIN",prenom:"EMILIE", ville: "Carnon (34)"},{nom:"DICHON",prenom:"GUILLAUME", ville: "Montpellier (34)"},{nom:"LABROU",prenom:"ANGELE", ville: "Montpellier (34)"},
    {nom:"MARIN",prenom:"PIERRE", ville: "Lattes (34)"},{nom:"MARTIN",prenom:"ALPHONSE", ville: "Montpellier (34)"},{nom:"MARTIN",prenom:"JEAN", ville: "Montpellier (34)"},
    {nom:"MERMOZ",prenom:"VERONIQUE", ville: "Montpellier (34)"},{nom:"RICHARD",prenom:"STEPHANIE", ville: "Montpellier (34)"},{nom:"RIVIERE",prenom:"AMELIE", ville: "Montpellier (34)"},
    {nom:"ROI",prenom:"STEFFY", ville: "Maugio (34)"}];
    
...
    
groupPatients(patients){
 
	let sortedPatients = patients;//.sort();
	let currentLetter = false;
	let currentPatients = [];

	sortedPatients.forEach((value, index) => {

    		if(value.nom.charAt(0) != currentLetter){

			currentLetter = value.nom.charAt(0);

			let newGroup = {
			    letter: currentLetter,
			    patients: []
			};

			currentPatients = newGroup.patients;
			this.groupedPatients.push(newGroup);

    		} 

    		currentPatients.push(value);

	});

}   
 ```

*View file*

```xml
<ion-item *ngFor="let item of group.patients" (click)="onSelectPatient(item)"> 
      {{ item.nom }} {{ item.prenom }}
      <p class="subtitle">{{ item.ville }}
</ion-item>
```   

## pagination
[Back to top](#ui-components)  

An easy way to manage pagination with dynamic data loading via webservices is to use https://www.npmjs.com/package/ngx-pagination

````
npm install ngx-pagination --save
````

*View file*

````
<pagination-controls class="my-pagination" (pageChange)="changePage($event)"></pagination-controls>    
<div *ngFor="let item of filteredStudies | paginate: { 
	itemsPerPage: pageSize,
	currentPage: p,
	totalItems: total }">
	<ion-card mode="ios" class="ion-no-padding" class="result-card">
		<ion-card-content class="ion-no-padding">
		  <ion-card-header class="ion-no-padding">
		    {{ item.name }}
		  </ion-card-header>
		</ion-card-content>
	</ion-card>
</div>
<pagination-controls class="my-pagination" (pageChange)="changePage($event)"></pagination-controls>    
````

*Controller file*

````
import { DataService } from './../services/data.service';
import { Component, OnInit } from '@angular/core';
import {NgxPaginationModule} from 'ngx-pagination';
import { LoadingController } from '@ionic/angular';
export interface IPeriodAnalytics {
  periode: string;
  data: any[];
}
export interface IServiceResult {
  totalCount: string;
  data: any[];
  filters: {
    method: any[],
    out: any[],
    pop: any[],
    hg: any[],
    cntry: any[],
    npi: any[],
    jns: any[],
    part: string,
    dis: any[]
  };
  periodAnalytics: IPeriodAnalytics;
}

@Component({
  selector: 'app-pagination',
  templateUrl: './pagination.page.html',
  styleUrls: ['./pagination.page.scss'],
})
export class PaginationPage implements OnInit {
  filteredStudies = []; // dataset
  pageSize = 50;
  p = 1;	// current page
  total = 0;	// total pages
  dataset: any = {};	// dataset returned by webservice
  
  constructor(private dataService: DataService,
              private loadingController: LoadingController) { }

  ngOnInit() {
	this.fetchData();
  }
  
  async fetchData(pageNumber: number = 0) {
    const loading = await this.loadingController.create({
      message: 'Searching data...'
    });
    await loading.present();

    await this.dataService.getFullStudies(pageNumber)
      .subscribe((result: any) => {
        this.dataset = result;
        this.filteredStudies = result.data as any[];
        this.total = this.dataset.totalCount;	// Total count is given by the webservice
        loading.dismiss();
      });
  }
  
  // Change page event
  async changePage(e) {
    this.p = e;
    this.fetchData(this.p);	// call webservice for the selected page index
  }
}

````

*Service file*

**Note :** Here, the service returns elements 50 by 50 starting from the passed page index
````
import { Injectable } from '@angular/core';
import { HttpClient, HttpHeaders, HttpErrorResponse } from '@angular/common/http';

@Injectable({
  providedIn: 'root'
})
export class DataService {

  constructor(private http: HttpClient) { }
  
  getFullStudies(page = 0) {
    
    const url = 'https://my-service/GetScientificStudies';
    const parameters = 'searchValue=test' +
    '&StartIndex=' + page +
    '&Length=50';

    const httpOptions = {
      headers: new HttpHeaders({'Access-Control-Allow-Origin': '*', 'Content-Type': 'application/json'})
    };

    return this.http.get(url + '?' + parameters, httpOptions);
  }
}

````

*Style file*

**Important :** put the pagination style inside de page style, not in the global.scss
````
// active page
.my-pagination::ng-deep .ngx-pagination .current,
  .btn-info {
    background: transparent !important;
    color: #707070;
    font-weight: bold;
    border-radius: 20px !important;
  }
// active page hover
.my-pagination::ng-deep .ngx-pagination .current:hover {
    background: transparent !important;
    color: gold;
    font-weight: bold;
    border: transparent !important;
}
// inactive page
.my-pagination::ng-deep .ngx-pagination a {
    background: transparent !important;
    color: #BEBDB8 !important;
    font-weight: normal;
    border: transparent !important;
}
// inactive page hover
.my-pagination::ng-deep .ngx-pagination a:hover, .ngx-pagination button:hover {
    background: transparent !important;
    color: #707070 !important;
    font-weight: normal;
    border: transparent !important;
}
````

## ion-label
[Back to top](#ui-components)  

```
<ion-label position="floating">Datetime</ion-label>
```

### set long label

To show long label, add the following css to your ion-label

```
.longLabel {
    white-space: pre-warp !important;
}
```

## Searchbar
[Back to top](#ui-components)  

> **Update** [see this tutorial for Observable update on searchbar](https://www.joshmorony.com/using-observables-in-ionic-3-9-x-and-angular-5/)    

*View file*

```xml
<ion-header>
  <ion-navbar color="primary">
    <ion-title>
      Annuaire
    </ion-title>
  </ion-navbar>
  <ion-toolbar color="maintheme">
      <ion-searchbar debounce="500" placeholder="Search by Tag ID, Name or Mac address" 
      class="searchbar" [(ngModel)]="searchTerm" (ionChange)="getDevice($event)"></ion-searchbar>
   </ion-toolbar>
</ion-header>

<ion-content padding class="page1">
  <ion-list>
    <ion-item *ngFor="let item of items">
      <h2>{{item.nom}}</h2>
    </ion-item>
  </ion-list>
</ion-content>
```

> It's recommanded to use *ionChange* event instead of *ionInput* to gives enough time to the user for tiping. In completion, use *debounce* option

*Some options*
```
<!-- Searchbar with danger color -->
<ion-searchbar color="danger"></ion-searchbar>

<!-- Searchbar with value -->
<ion-searchbar value="Ionic"></ion-searchbar>

<!-- Searchbar with telephone type -->
<ion-searchbar type="tel"></ion-searchbar>

<!-- Searchbar with a cancel button and custom cancel button text -->
<ion-searchbar showCancelButton cancelButtonText="Custom Cancel"></ion-searchbar>

<!-- Searchbar with a custom debounce -->
<ion-searchbar debounce="500"></ion-searchbar>

<!-- Animated Searchbar -->
<ion-searchbar animated></ion-searchbar>
```

*Controller file*

```javascript
constructor(public nav: NavController) {
    this.nav = nav;
    this.getAllDevices();
  }

  getAllDevices(){
    this.items = [{
      nom: "iPhone 4S",
      version: "iOS 7.2.1"
    },{
      nom: "iPad2",
      version: "iOS 8.3"
    },{
      nom: "Mac Mini 1",
      version: "Mac OSX El Capitan - SSD 128Go"
    },{
      nom: 'Mac Book Pro 17"',
      version: "Mac OSX Yosemite"
    },{
      nom: "Nexus 4",
      version: "Android 4.4.4"
    }];
  }
  
  getDevice(ev: any){
    this.getAllDevices();

    // set searchText to the value of the searchbar
    var searchText = ev.target.value;// var searchText = ev.detail.value;

    // Avoid research if searchtext is empty
    if (!searchText || searchText.trim() === '') {
      return;
    }

    // Filtering on the attribute 'nom'
    this.items = this.items.filter((v) => {
      if (v.nom.toLowerCase().indexOf(searchText.toLowerCase()) > -1) {
        return true;
      }
      return false;
    })
  }
```

## High performance list filtering
[Back to top](#ui-components)  

To increase list filtering, we can use Observable instead of basic filtering shows in searchbar section above.

*View file*

```xml
<ion-header>
  <ion-navbar color="primary">
    <ion-title>
      Ionic Blank
    </ion-title>
  </ion-navbar>
</ion-header>
 
<ion-content class="home-page">
 
    <ion-searchbar [(ngModel)]="searchTerm" [formControl]="searchControl" (ionInput)="onSearchInput()"></ion-searchbar>
 
    <div *ngIf="searching" class="spinner-container">
        <ion-spinner></ion-spinner>
    </div>
 
    <ion-list>
        <ion-item *ngFor="let item of items">
            {{item.title}}
        </ion-item>
    </ion-list>
</ion-content>
```

*Controller file*

```javascript
import { Component } from '@angular/core';
import { Control } from '@angular/common';
import { NavController } from 'ionic-angular';
import { Data } from '../../providers/data/data';
import 'rxjs/add/operator/debounceTime';
 
@Component({
  templateUrl: 'build/pages/home/home.html',
  providers: [Data]
})
export class HomePage {
 
    searchTerm: string = '';
    searchControl: Control;
    items: any;
    searching: any = false;
 
    constructor(public navCtrl: NavController, private dataService: Data) {
        this.searchControl = new Control();
    }
 
    ionViewLoaded() {
        this.setFilteredItems();
 
        this.searchControl.valueChanges.debounceTime(700).subscribe(search => {
            this.searching = false;
            this.setFilteredItems();
        });
    }
 
    onSearchInput(){this.searching = true;}
 
    setFilteredItems() {this.items = this.dataService.filterItems(this.searchTerm); }
}
```

*Style file*

```css
.home-page {
 
    .spinner-container {
        width: 100%;
        text-align: center;
        padding: 10px;
    }
 
}
```

*Data.ts provider*

```javascript
import { Injectable } from '@angular/core';
import { Http } from '@angular/http';
import 'rxjs/add/operator/map';
 
@Injectable()
export class Data {
    items: any;
 
    constructor(private http: Http) {
        this.items = [
            {title: 'one'},
            {title: 'two'},
            {title: 'three'},
            {title: 'four'},
            {title: 'five'},
            {title: 'six'}
        ]
    }
    filterItems(searchTerm){
        return this.items.filter((item) => {
            return item.title.toLowerCase().indexOf(searchTerm.toLowerCase()) > -1;
        });     
    }
}
```

Our list filtering is designed pretty nicely now, and it should perform very well. However, since we have added this debounceTime it causes a slight delay that will certainly be perceptible to the user. Whenever we do something “in the background” we should indicate to the user that something is happening, otherwise it will appear as if the interface is lagging or broken.

A delay is fine, and an artificial delay is sometimes even beneficial, but you definitely don’t want to leave the user wondering “is this just slow or is it frozen?”.

We’re going to make a change now that won’t have any effect on performance, but it will have an impact on the user’s perception of the responsiveness of the app. We’re simply going to add a loading spinner that will display when a search is in progress.

## ion-tab
[Back to top](#ui-components)  

According to the latest Android material guideline, you have to follow these rules :

- 3 to 5 destinations => use the bottom navigation
- Less than 3 => use tab on the top
- More than 5 => use another solution like slide menu

### Basic structure

In the sample below, we assume that we have 4 tabs. In each of them we have the possibility to open some child modals.
We added the *handleTabSelect()* function to reset the route when switching tab in order to close all child modals.

**Warning** : We don't care of potential "edit" state in child modals. So keep in mind to add a verification if you have a child modal in "edit" mode in order to prevent from losing data

*tabs.page.html*

````
<ion-tabs #tabs>
  <ion-tab-bar slot="bottom">
    <ion-tab-button tab="home" (click)="handleTabSelect('home', $event)">
      <ion-icon name="home"></ion-icon>
      <ion-label>Home</ion-label>
      <ion-badge>6</ion-badge>
    </ion-tab-button>  
    <ion-tab-button tab="queries" (click)="handleTabSelect('queries', $event)">
      <ion-icon name="options-outline"></ion-icon>
      <ion-label>My queries</ion-label>
    </ion-tab-button>
    <ion-tab-button tab="lists" (click)="handleTabSelect('lists', $event)">
      <ion-icon name="star"></ion-icon>
      <ion-label>My lists</ion-label>
    </ion-tab-button>
    <ion-tab-button tab="settings">
      <ion-icon name="person-circle"></ion-icon>
      <ion-label>Settings</ion-label>
    </ion-tab-button>
  </ion-tab-bar>
</ion-tabs>

````

*tabs.component.ts*

````
import { Router } from '@angular/router';
import { Component, OnInit, ViewChild } from '@angular/core';
import { IonTabs } from '@ionic/angular';

@Component({
  selector: 'app-home-tabs',
  templateUrl: './home-tabs.page.html',
  styleUrls: ['./home-tabs.page.scss'],
})
export class HomeTabsPage implements OnInit {

  @ViewChild('tabs') tabs: IonTabs;

  constructor(private router: Router) { }
  ngOnInit(): void { }

  /**
   * Force route resetting when navigating in case of child modals
   * are opnened in specific tab
  **/ 
  async handleTabSelect(tab: string, evt): Promise<boolean> {
    const tabSelected = this.tabs.getSelected();
    evt.stopImmediatePropagation();
    evt.preventDefault();
    return tabSelected !== tab ? await this.router.navigateByUrl(tab) : this.tabs.select(tab);      
  }
}
````

*tabs-routing.module.ts*

````
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { QueriesPage } from '../pages/queries/queries.page';
import { HomeTabsPage } from './home-tabs.page';

const routes: Routes = [
  {
    path: '',
    component: HomeTabsPage,
    children: [
      {
        path: 'home',
        loadChildren: () => import('./../pages/home/home.module').then(m => m.HomePageModule)
      },
      {
        path: 'queries',
        children: [
          {
            path: '',
            loadChildren: () => import('./../pages/queries/queries.module').then(m => m.QueriesPageModule)
          },
          {
            path: 'results/:queryId',
            loadChildren: () => import('./../pages/results/results.module').then( m => m.ResultsPageModule)
          },
          {
            path: 'results/:queryId/study/:idStudy',
            loadChildren: () => import('./../pages/study-detail/study-detail.module').then( m => m.StudyDetailPageModule)
          }
        ]
      },
      {
        path: 'lists',
        children: [
          {
            path: '',
            loadChildren: () => import('./../pages/lists/lists.module').then(m => m.ListsPageModule)
          },
          {
            path: 'results/:listId',
            loadChildren: () => import('./../pages/results/results.module').then( m => m.ResultsPageModule)
          }
        ]
        
      },
      {
        path: 'settings',
        loadChildren: () => import('./../pages/settings/settings.module').then(m => m.SettingsPageModule)
      },
     /* {
        path: '',
        redirectTo: '/home',
        pathMatch: 'full'
      }*/
    ]
  },
  {
    path: '',
    redirectTo: '/home',
    pathMatch: 'full'
  },
 
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule],
})
export class HomeTabsPageRoutingModule {}

````

### tabs options

So, by default, ionic 2 tabs project sample is using bottom navigation. To set top navigation, just change your bootstrap (in your **app.module.ts**) code by :

```
import { TabsPage } from './../pages/tabs/tabs';
import { NgModule, ErrorHandler } from '@angular/core';
import { IonicApp, IonicModule, IonicErrorHandler } from 'ionic-angular';
import { MyApp } from './app.component';

@NgModule({
  declarations: [
    MyApp,
    TabsPage
  ],
  imports: [
    IonicModule.forRoot(MyApp,{tabsPlacement: "top"})	// tab placement
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp,
    TabsPage
  ],
  providers: [{provide: ErrorHandler, useClass: IonicErrorHandler}]
})
export class AppModule {}

```

#### Hide tab on sub pages

To hide tabs on sub pages, modify your *app.module-ts* as following

*app.module.ts*

```javascript
@NgModule({
  declarations: [...],
  imports: [
    IonicModule.forRoot(MyApp,{tabsHideOnSubPages:"true"})	// Hide tab
  ],
  bootstrap: [IonicApp],
  entryComponents: [...],
  providers: [{provide: ErrorHandler, useClass: IonicErrorHandler}]
})
```

#### Hide tab on specific page

You can need to hide tabgroup just on a specific page with this :

```javascript
export class MyClass implements OnInit{

  tabBarElement: any;

  constructor(public navCtrl: NavController, public navParams: NavParams) {
    this.tabBarElement = document.querySelector('.tabbar.show-tabbar');
  }
  
  ionViewWillEnter(){
    this.tabBarElement.style.display = 'none';
  }
  ionViewWillLeave(){
    this.tabBarElement.style.display = 'flex';
  }
}
```

## Tab icon
[Back to top](#ui-components)  

Customize tab icon

You can set your own svg as tab-icon like below :

*View file*

````
<ion-tabs #tabs>
  <ion-tab-bar slot="bottom">
    <ion-tab-button tab="home" (click)="handleTabSelect('home', $event)">
      <ion-icon src="./assets/imgs/pictos/picto_menu_home.svg"></ion-icon>
      <ion-label>Home</ion-label>
    </ion-tab-button>  
    <ion-tab-button tab="queries" (click)="handleTabSelect('queries', $event)">
      <ion-icon src="./assets/imgs/pictos/picto_menu_query.svg"></ion-icon>
      <ion-label>My queries</ion-label>
    </ion-tab-button>
    <ion-tab-button tab="lists" (click)="handleTabSelect('lists', $event)">
      <ion-icon src="./assets/imgs/pictos/picto_menu_list.svg"></ion-icon>
      <ion-label>My lists</ion-label>
    </ion-tab-button>
    <ion-tab-button tab="settings">
      <ion-icon name="person-circle"></ion-icon>
      <ion-label>Settings</ion-label>
    </ion-tab-button>
  </ion-tab-bar>
</ion-tabs>
````

**Important** in order to make icon automatically change color when selecting tab, you must remove **all** *fill* attribute inside your svg file 

*svg file before modification*

````
<svg xmlns="http://www.w3.org/2000/svg" width="16.426" height="15" viewBox="0 0 16.426 15"><defs><style>.a{fill:#009dbe;}.b{fill:#fff;stroke:#009dbe;stroke-width:1.5px;}.c{stroke:none;}.d{fill:none;}</style></defs><g transform="translate(0)"><path class="a" d="M21.073,58.843H17.641a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h3.432a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,21.073,58.843Z" transform="translate(-5.163 -52.41)"/><path class="a" d="M21.073,58.843H13.839a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h7.234a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,21.073,58.843Z" transform="translate(-5.163 -47.41)"/><path class="a" d="M7.977,58.843H5.68a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h2.3a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,7.977,58.843Z" transform="translate(-5.163 -57.41)"/><path class="a" d="M13.1,58.843H5.68a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5H13.1a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,13.1,58.843Z" transform="translate(-5.163 -52.41)"/><path class="a" d="M9.234,58.843H5.68a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5H9.234a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,9.234,58.843Z" transform="translate(-5.163 -47.41)"/><path class="a" d="M21.073,58.843H14.081a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h6.992a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,21.073,58.843Z" transform="translate(-5.163 -57.41)"/></g><g class="b" transform="translate(1.855)"><circle class="c" cx="2.5" cy="2.5" r="2.5"/><circle class="d" cx="2.5" cy="2.5" r="1.75"/></g><g class="b" transform="translate(9.855 5)"><circle class="c" cx="2.5" cy="2.5" r="2.5"/><circle class="d" cx="2.5" cy="2.5" r="1.75"/></g><g class="b" transform="translate(5.855 10)"><circle class="c" cx="2.5" cy="2.5" r="2.5"/><circle class="d" cx="2.5" cy="2.5" r="1.75"/></g></svg>
````

*svg file after modification*

````
<svg xmlns="http://www.w3.org/2000/svg" width="16.426" height="15" viewBox="0 0 16.426 15"><defs><style>.a{}.b{stroke:#009dbe;stroke-width:1.5px;}.c{stroke:none;}.d{}</style></defs><g transform="translate(0)"><path class="a" d="M21.073,58.843H17.641a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h3.432a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,21.073,58.843Z" transform="translate(-5.163 -52.41)"/><path class="a" d="M21.073,58.843H13.839a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h7.234a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,21.073,58.843Z" transform="translate(-5.163 -47.41)"/><path class="a" d="M7.977,58.843H5.68a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h2.3a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,7.977,58.843Z" transform="translate(-5.163 -57.41)"/><path class="a" d="M13.1,58.843H5.68a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5H13.1a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,13.1,58.843Z" transform="translate(-5.163 -52.41)"/><path class="a" d="M9.234,58.843H5.68a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5H9.234a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,9.234,58.843Z" transform="translate(-5.163 -47.41)"/><path class="a" d="M21.073,58.843H14.081a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h6.992a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,21.073,58.843Z" transform="translate(-5.163 -57.41)"/></g><g class="b" transform="translate(1.855)"><circle class="c" cx="2.5" cy="2.5" r="2.5"/><circle class="d" cx="2.5" cy="2.5" r="1.75"/></g><g class="b" transform="translate(9.855 5)"><circle class="c" cx="2.5" cy="2.5" r="2.5"/><circle class="d" cx="2.5" cy="2.5" r="1.75"/></g><g class="b" transform="translate(5.855 10)"><circle class="c" cx="2.5" cy="2.5" r="2.5"/><circle class="d" cx="2.5" cy="2.5" r="1.75"/></g></svg>


*Tab scss file*

````
ion-tab-button {
    --color: black;
    --color-selected: red;
}
````

## Floating button
[Back to top](#ui-components)  

This snippet show how to fix floating button in front of a list

```xml
 <!-- Fixed Floating Action Button that does not scroll with the content -->
  <ion-fab vertical="bottom" horizontal="end" slot="fixed">
    <ion-fab-button>
      <ion-icon name="arrow-dropleft"></ion-icon>
    </ion-fab-button>
  </ion-fab>

  <!-- Default Floating Action Button that scrolls with the content.-->
  <ion-fab-button>Default</ion-fab-button>

  <!-- Mini -->
  <ion-fab-button size="small">Mini</ion-fab-button>

  <!-- Colors -->
  <ion-fab-button color="primary">Primary</ion-fab-button>
```

## Chart
[Back to top](#ui-components)  

[Chart.js](http://www.chartjs.org/)    
[Chart.js API](http://www.chartjs.org/docs/latest/charts/)    
[D3js](https://www.d3-graph-gallery.com/)    
[ChartistJS](http://gionkunz.github.io/chartist-js/)

[Josh morony chartJS](https://www.joshmorony.com/adding-responsive-charts-graphs-to-ionic-2-applications/)    
[link : ng2-chart](http://valor-software.com/ng2-charts/)    
[forum : related post](https://forum.ionicframework.com/t/solved-ionic-2-ng2-charts/42926)    

### ChartJS Sample code

#### Installation

````
npm install chart.js --save
````



*View.html*

```
<ion-header>
  <ion-navbar color="dark">
    <ion-title>
      ChartJS
    </ion-title>
  </ion-navbar>
</ion-header>


<ion-content padding>
  <ion-item [style.backgroundColor]="'transparent'" lines="none">
    <ion-icon slot="start" name="md-tablet-landscape" color="dark"></ion-icon>
    <b slot="end">Basculez en mode paysage pour voir le graphe</b>
  </ion-item>
  <div *ngIf="orientationPortrait">  
    <ion-item *ngFor="let i of dataList">{{ i }}</ion-item>
  </div>
  <ion-card no-padding *ngIf="!orientationPortrait">
    <ion-card-header>
      Weight chart
    </ion-card-header>
    <ion-card-content>
      <canvas #lineCanvas></canvas>
    </ion-card-content>
  </ion-card>
</ion-content>

```

*Controller.ts*

```javascript
import { Component, ViewChild, OnInit } from '@angular/core';
import { NavController, NavParams } from 'ionic-angular';
import { Chart } from 'chart.js';
import { ScreenOrientation } from '@ionic-native/screen-orientation';
import { ChangeDetectorRef } from '@angular/core';

@Component({
  selector: 'page-chart',
  templateUrl: 'chart.html',
})
export class ChartPage implements OnInit{

  @ViewChild('lineCanvas', {static: true}) lineCanvas;
  lineChart: any;
  orientation;
  orientationPortrait: boolean = true;
  dataList = [13.2, 13.5, 14.2, 13.8, 13.5, 13.4, 13.7];

  constructor(public navCtrl: NavController, 
    public navParams: NavParams, 
    private screenOrientation: ScreenOrientation,
    private ref: ChangeDetectorRef) {
  }

  ngOnInit(){
    
    this.screenOrientation.onChange().subscribe(
      () => {
          this.orientation = this.screenOrientation.type
          switch(this.screenOrientation.type){
            case this.screenOrientation.ORIENTATIONS.LANDSCAPE :
            case this.screenOrientation.ORIENTATIONS.LANDSCAPE_PRIMARY :
            case this.screenOrientation.ORIENTATIONS.LANDSCAPE_SECONDARY :
              this.orientationPortrait = false;
              this.ref.detectChanges();// force refresh
              this.onGenerateChart();
              this.ref.detectChanges();// force refresh
              break;
            case this.screenOrientation.ORIENTATIONS.PORTRAIT :
            case this.screenOrientation.ORIENTATIONS.PORTRAIT_PRIMARY :
            case this.screenOrientation.ORIENTATIONS.PORTRAIT_SECONDARY :
              this.orientationPortrait = true;
              this.ref.detectChanges();// force refresh
              break;
            default:
              this.orientationPortrait = true;
              this.ref.detectChanges(); // force refresh
              break;
          }
      }
   );
  }

  ionViewDidLoad() {
    this.onGenerateChart();
  }

  onGenerateChart(){
    // fill the chart with gradient
    //https://developer.mozilla.org/fr/docs/Web/API/CanvasRenderingContext2D/createLinearGradient
    var ctx = this.lineCanvas.nativeElement.getContext("2d");
    var grd=ctx.createLinearGradient(0,0,0,200);
    grd.addColorStop(0,"#cc1e1e");
    grd.addColorStop(1,"white");

    this.lineChart = new Chart(this.lineCanvas.nativeElement, {
 
      type: 'line',
      data: {
          labels: ["January", "February", "March", "April", "May", "June", "July"],
          datasets: [
              {
                  label: "weight",
                  fill: true, // remplissage de la zone sous le graphe
                  lineTension: 0.5, // lissage de la courbe
                  backgroundColor: grd,//"rgba(0,0,0,0.4)",
                  borderColor: "rgba(0,0,0,1)",
                  borderCapStyle: 'butt',
                  borderDash: [],
                  borderDashOffset: 0.0,
                  borderJoinStyle: 'miter',
                  pointBorderColor: "rgba(0,0,0,1)",
                  pointBackgroundColor: "#fff",
                  pointBorderWidth: 1,
                  pointHoverRadius: 5,
                  pointHoverBackgroundColor: "rgba(0,0,0,1)",
                  pointHoverBorderColor: "rgba(220,220,220,1)",
                  pointHoverBorderWidth: 2,
                  pointRadius: 1,
                  pointHitRadius: 10,
                  data: this.dataList,
                  spanGaps: false
              }
          ]
      }
   });
  }
}
```
*Style.scss*

```
page-chart {
    .scroll-content {
        background-color: map-get($colors, light);
    }

    ion-card-header {
        font-weight: bold;
    }
}
```

#### Pie chart
[Back to top](#ui-components)  

*view file* 
```
<canvas #myCanvas></canvas>
```

*controller file*
```
export class MyClass {
	@ViewChild('myCanvas', {static: true}) myCanvas: ElementRef;
	
	initChart() {
		const dataset = { 
			datasets: [{
				data: [5, 6, 18],
				backgroundColor: ['#ffcc55', '#a51231', '#44ff22']
			}],
			labels: ['Red', 'Blue', 'Green']
		};
		
		const ctx = this.myCanvas.nativeElement;
		
		const myChart = new Chart(ctx,{
			type: 'pie',
			data: dataset,
			options: options
		});
	}
}
```

**Define chart height**

```
const ctx = this.myCanvas.nativeElement;
ctx.height = 150;

const myChart = new Chart(ctx,{
	type: 'pie',
	data: dataset,
	options: {
		maintainAspectRatio: false,
	}
});
```

#### Customization
[Back to top](#ui-components)  

https://blog.vanila.io/chart-js-tutorial-how-to-make-gradient-line-chart-af145e5c92f9
https://codepen.io/nzuki/pen/oVmOOd
https://medium.com/@ValeriaCortezVD/tutorial-this-is-why-you-should-use-gradient-charts-with-chart-js-f5be20e39c92

**Gradient**

````
gradientStroke.addColorStop(0, firstColour);
gradientStroke.addColorStop(0.3, secondColour);
gradientStroke.addColorStop(0.6, thirdColour);
gradientStroke.addColorStop(1, fourthColour);
````

The *addColorStop(stop, colour)* method specifies the colours and their position **(0–1)** in the gradient object.


### Full example
[Back to top](#ui-components)  

In this example we create 1 line chart and two bar chart.

*Controller file*

````
import { Component, OnInit, Input, ViewChild } from '@angular/core';
import { Chart } from 'chart.js';

@Component({
  selector: 'app-view-analytics',
  templateUrl: './view-analytics.component.html',
  styleUrls: ['./view-analytics.component.scss'],
})
export class ViewAnalyticsComponent implements OnInit {

  @Input() dataset: any[] = [];
  @ViewChild('lineCanvas', {static: true}) lineCanvas;
  @ViewChild('barCanvas1', {static: true}) barCanvas1;
  @ViewChild('barCanvas2', {static: true}) barCanvas2;
  lineChart: any;
  barChart1: any;
  barChart2: any;
  lineChartTitle = 'Results by year';
  barChart1Title = 'Top NPI';
  barChart2Title = 'Top Outcomes';
  dataList = [5400, 5000, 4790, 4100, 3750, 3200, 2530, 2240, 2050];

  constructor() { }

  ngOnInit() {
    this.refreshChart();
  }

  ngAfterContentInit() {
    console.table(this.dataset);
  }
  updateData(data) {
    this.dataset = data;
    console.table(this.dataset);
  }

  refreshChart() {
    this.generateLineChart();
    this.generateBarChart1();
    this.generateBarChart2();
  }

  generateLineChart() {
    const ctx = this.lineCanvas.nativeElement.getContext('2d');
    
    this.lineChart = new Chart(this.lineCanvas.nativeElement, {
      type: 'line',
      data: {
          labels: ['2012', '2013',
        '2014', '2015', '2016',
      '2017', '2018', '2019', '2020'],
          datasets: [
              {
                  data: [1200, 1600, 2020, 1990, 2390, 1850, 2100, 2000, 2600],
                  spanGaps: false,
                  borderCapStyle: 'round',
                  lineTension: 0.5,
                  fill: false,
                  backgroundColor: '#72B83B',
                  borderColor: '#72B83B',
                  pointRadius: 0,
                  borderJoinStyle: 'miter'
              }
          ]
      },
      options: {
        /*title: {
          display: true,
          text: this.lineChartTitle,
        },*/
        maintainAspectRatio: false,
        legend: {
          display: false
        },
        layout: {
          padding: {
              left: 20,
              right: 20,
              top: 20,
              bottom: 50
          }
        },
        scales: {
            xAxes: [{
              gridLines: {
                display: false
              },
              scaleLabel: {
                display: false // hide egend title on X
              },
              ticks: {
                autoSkip: false,
                maxRotation: 90,  // legend rotation
                minRotation: 90  // legend rotation
              }
            }]
        },
      },
    });
  }

  generateBarChart1() {
    var ctx = this.barCanvas1.nativeElement.getContext('2d');
    var grd = ctx.createLinearGradient(0, 0, 0, 400);
    grd.addColorStop(0, '#72B83B');
    grd.addColorStop(0.5, '#ADDB88');
    grd.addColorStop(1, '#E2FACE');
    this.barChart1 = new Chart(this.barCanvas1.nativeElement, {
      type: 'horizontalBar',
      data: {
          labels: ['Chinese herbal drugs', 'Cognitive behaviour therapy',
        'Health education', 'Physical activity', 'Screening',
      'Massage', 'Beta carotene', 'Psychotherapy', 'Behavior therapy'],
          datasets: [
              {
                  label: '',
                  fillColor: grd,
                  fill: true, 
                  backgroundColor: grd,
                  borderWidth: 0,
                  borderColor: 'rgba(255,99,132,1)',  // tooltip square border
                  borderCapStyle: 'round', //'butt',
                  borderDash: [],
                  barThickness: 'flex',
                  data: this.dataList,
                  spanGaps: false,
                  showTooltips: false
              }
          ]
      },
      options: {
        /*tooltips: {enabled: false},
        hover: {mode: null},*/
        legend: {
          display: false
        },
        layout: {
          padding: {
              left: 20,
              right: 50,
              top: 0,
              bottom: 10
          }
        },
        scales: {
            yAxes: [{
              gridLines: {
                display: false
              },
              scaleLabel: {
                display: false // hide legend on Y
              }
            }],
            xAxes: [{
              gridLines: {
                display: false
              },
              scaleLabel: {
                display: false // hide legend on X
              },
              ticks: {
                display: false // hide labels on X
              }
            }]
        },
        animation: {
          duration: 1,
          onComplete: () => {
            // Display values at the end of the bars
            const chartInstance = this.barChart1,
            ctx = chartInstance.ctx;
            ctx.font = Chart.helpers.fontString(Chart.defaults.global.defaultFontSize, Chart.defaults.global.defaultFontStyle, Chart.defaults.global.defaultFontFamily);
            ctx.textAlign = 'center';
            ctx.textBaseline = 'bottom';
            ctx.fillStyle = '#000';
            this.barChart1.data.datasets.forEach((dataset, i) => {
              const meta = chartInstance.controller.getDatasetMeta(i);
              meta.data.forEach((bar, index) => {
                const data = dataset.data[index];
                ctx.fillText(data, bar._model.x + 20, bar._model.y + 6);
              });
            });
          }
        },
      },
    });
  }

  generateBarChart2() {
    var ctx = this.barCanvas2.nativeElement.getContext('2d');
    var grd = ctx.createLinearGradient(0, 0, 0, 400);
    grd.addColorStop(0, '#EB5E62');
    grd.addColorStop(0.5, '#EE8E91');
    grd.addColorStop(1, '#F2CACB');
    this.barChart2 = new Chart(this.barCanvas2.nativeElement, {
      type: 'horizontalBar',
      data: {
          labels: ['Adaptation, psychological', 'Return to work',
                  'Quality of life', 'Self concept', 'Patient compliance',
                  'Nausea', 'Body composition',
                  'Anxiety', 'Gastrointestinal motility'],
          datasets: [
              {
                  label: '',
                  fillColor: grd,
                  fill: true, // fill under chart area
                  lineTension: 0.5, // curve smooth
                  backgroundColor: grd,
                  borderColor: 'rgba(255,99,132,1)',  // tooltip square border
                  borderCapStyle: 'round', //'butt',
                  borderDash: [],
                  borderWidth: 0,
                  barThickness: 'flex',
                  data: this.dataList,
                  spanGaps: false,
              }
          ]
      },
      options: {
        legend: {
          display: false
        },
        layout: {
          padding: {
              left: 20,
              right: 50,
              top: 0,
              bottom: 10
          }
        },
        scales: {
            yAxes: [{
              gridLines: {
                display: false
              },
              scaleLabel: {
                display: false // hide legend on Y
              }
            }],
            xAxes: [{
              gridLines: {
                display: false
              },
              scaleLabel: {
                display: false // hide legend on X
              },
              ticks: {
                display: false // hide labels on X
              }
            }]
        },
        animation: {
          duration: 1,
          onComplete: () => {
            // Display values at the end of the bars
            const chartInstance = this.barChart2,
            ctx = chartInstance.ctx;
            ctx.font = Chart.helpers.fontString(Chart.defaults.global.defaultFontSize, Chart.defaults.global.defaultFontStyle, Chart.defaults.global.defaultFontFamily);
            ctx.textAlign = 'center';
            ctx.textBaseline = 'bottom';
            ctx.fillStyle = '#000';
            this.barChart2.data.datasets.forEach((dataset, i) => {
              const meta = chartInstance.controller.getDatasetMeta(i);
              meta.data.forEach((bar, index) => {
                const data = dataset.data[index];
                ctx.fillText(data, bar._model.x + 20, bar._model.y + 6);
              });
            });
          }
        },
      },
    });
  }
}

````
[Back to top](#ui-components)  

*View file*

````
<div class="flex-container">
  <div class="flex-div">
    <div class="tile chart-div2">
      <div class="chart-title">
        <ion-label>{{ lineChartTitle }}</ion-label>
      </div>
      <canvas #lineCanvas></canvas>
    </div>
  </div>
  <div class="flex-div">
    <div class="tile chart-div2">
      <div class="chart-title">
        <ion-label>{{ barChart1Title }}</ion-label>
      </div>
      <canvas #barCanvas1></canvas>
    </div>
  </div>
  <div class="flex-div">
    <div class="tile chart-div2">
      <div class="chart-title">
        <ion-label>{{ barChart2Title }}</ion-label>
      </div>
      <canvas #barCanvas2></canvas>
    </div>
  </div>  
</div>
````
[Back to top](#ui-components)  


*Style file*

````
.tile {
    -webkit-box-shadow: 0px 0px 12px -4px rgba(0,0,0,0.75);
    -moz-box-shadow: 0px 0px 12px -4px rgba(0,0,0,0.75);
    box-shadow: 0px 0px 12px -4px rgba(0,0,0,0.75);
    border-radius: 10px;
}
.chart-title {
    padding-left: 24px;
    padding-top: 20px;
    font-size: 11pt;
    font-weight: bold;
    color: #555;

    float: left;
}
.chart-div2 {
    position: relative;
    margin: auto;
    height: 260px;
    width: 100%;
}
.flex-container {
    display: flex;
    flex-wrap: wrap;
    padding: none;
}
.flex-div {
    text-align: center;
    //line-height: 75px;
    //font-size: 30px;
    margin-right: 20px;
}
@media screen and (min-width: 1600px) {
    .flex-div {
        width: 31%;
    }
    .chart-div2 {
        height: 260px;
        margin-top: 0px;
    }
}
/* If the screen size is 600px wide or less, set the font-size of <div> to 30px */
@media screen and (max-width: 1600px) {
    .flex-div {
        width: 48%;
    }
    .chart-div2 {
        height: 320px;
        margin-top: 20px;
    }
}
/* If the screen size is 600px wide or less, set the font-size of <div> to 30px */
@media screen and (max-width: 1344px) {
    .flex-div {
        width: 47%;
    }
    .chart-div2 {
        height: 320px;
        margin-top: 20px;
    }
}
/* If the screen size is 600px wide or less, set the font-size of <div> to 30px */
@media screen and (max-width: 1024px) {
    .flex-div {
        width: 100%;
    }
    .chart-div2 {
        height: 400px;
        margin-top: 20px;
    }
}

````

### Chart grid 50%
[Back to top](#ui-components)  

Here is a sample code aimed to put 2 charts (line + bar) on the same grid line with 50% width each.

*View file*

````
<div id="block_container">
    <div class="left-chart-div">
      <canvas #lineCanvas></canvas>
    </div>
    <div class="right-chart-div">
      <canvas #barCanvas></canvas>
    </div>
</div>
````
[Back to top](#ui-components)  

*Style file*

````
.block_container {
    display: flex;
    justify-content: center;
}
.left-chart-div {
    width: 50%;
    padding: 10px;
    overflow: hidden;
    float: left;
}
.right-chart-div {
    float: right;
    width: 50%;
    padding: 10px;
    overflow: hidden;
}
````
[Back to top](#ui-components)  

*Controller file*

````
import { Component, OnInit, ViewChild, ElementRef } from '@angular/core';
import { Chart } from 'chart.js';
import 'chartjs-top-round-bar';

@Component({
  selector: 'app-chart',
  templateUrl: './chart.page.html',
  styleUrls: ['./chart.page.scss'],
})
export class ChartPage implements OnInit {
  @ViewChild('barCanvas', {static: true}) barCanvas;
  barChart: any;
  
  @ViewChild('lineCanvas', {static: true}) lineCanvas;
  lineChart: any;
  
  dataList = [5400, 5000, 4790, 4100, 3750, 3200, 2530, 2240, 2050];


  horizontalBarChartData = {
    labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
    datasets: [{
      label: 'Dataset 1',
      backgroundColor: "#ffcc00",
      borderColor: 'red',
      borderWidth: 1,
      data: [
        1,2,3,4,5,6,7
      ]
    }, {
      label: 'Dataset 2',
      backgroundColor: "orange",
      borderColor: "orange",
      data: [
        8,9,10,11,12,13,14
      ]
    }]
  };
    
  constructor() { }

  ngOnInit() {
    this.generateLineChart();
    this.generateBarChart();
  }

  generateBarChart() {
    var ctx = this.barCanvas.nativeElement.getContext('2d');
    var grd = ctx.createLinearGradient(0, 0, 0, 400);
    grd.addColorStop(0, '#72B83B');
    grd.addColorStop(1, '#E2FACE');
    this.barChart = new Chart(this.barCanvas.nativeElement, {
      type: 'horizontalBar',
      data: {
          labels: ['Chinese herbal drugs', 'Cognitive behaviour therapy',
        'Health education', 'Physical activity', 'Screening',
      'Massage', 'Beta carotene', 'Psychotherapy', 'Behavior therapy'],
          datasets: [
              {
                  label: '',
                  fillColor: grd,
                  fill: true, // remplissage de la zone sous le graphe
                  lineTension: 0.5, // lissage de la courbe
                  backgroundColor: grd,
                  borderColor: 'rgba(255,99,132,1)',  // border du carré dans la tooltip
                  borderCapStyle: 'round', //'butt',
                  borderDash: [],
                  barThickness: 'flex', // largeur des barres. 'flex' = définition automatique de la largeur la plus appropriée
                  //barPercentage: 0.5,
                  borderDashOffset: 0.0,
                  borderJoinStyle: 'miter', // jointure de courbe (arrondie, plate ou pointue)
                  pointBorderColor: 'rgba(0,0,0,1)',
                  pointBackgroundColor: '#fff',
                  pointBorderWidth: 0,
                  pointHoverRadius: 5,
                  pointHoverBackgroundColor: 'rgba(0,0,0,1)',
                  pointHoverBorderColor: 'rgba(220,220,220,1)',
                  pointHoverBorderWidth: 2,
                  pointRadius: 1,
                  pointHitRadius: 10,
                  data: this.dataList,
                  spanGaps: false,
              }
          ]
      },
      options: {
        legend: {
          display: false
        },
        scales: {
            yAxes: [{
              gridLines: {
                color: 'white', // couleur des lignes de l'axe
                display: false
              },
              scaleLabel: {
                display: false // affichage de la légende des y
              }
            }],
            xAxes: [{
              gridLines: {
                color: 'white', // couleur des lignes de l'axe
              },
              scaleLabel: {
                display: false // affichage de la légende des x
              },
              ticks: {
                display: false // affichage des labels de l'axe des x
              }
            }]
        },
        animation: {
          duration: 1,
          onComplete: () => {
            const chartInstance = this.barChart,
            ctx = chartInstance.ctx;
            ctx.font = Chart.helpers.fontString(Chart.defaults.global.defaultFontSize, Chart.defaults.global.defaultFontStyle, Chart.defaults.global.defaultFontFamily);
            ctx.textAlign = 'center';
            ctx.textBaseline = 'bottom';
            ctx.fillStyle = '#000';
            this.barChart.data.datasets.forEach((dataset, i) => {
              const meta = chartInstance.controller.getDatasetMeta(i);
              meta.data.forEach((bar, index) => {
                const data = dataset.data[index];
                ctx.fillText(data, bar._model.x + 20, bar._model.y + 6);
              });
            });
          }
        },
      },
   });
  }


  generateLineChart() {
    const ctx = this.lineCanvas.nativeElement.getContext('2d');
    this.lineChart = new Chart(this.lineCanvas.nativeElement, {
      type: 'line',
      data: {
          labels: ['2012', '2013',
        '2014', '2015', '2016',
      '2017', '2018', '2019', '2020'],
          datasets: [
              {
                  data: [1200, 1600, 2020, 1990, 2390, 1850, 2100, 2000, 2600],
                  spanGaps: false,
                  borderCapStyle: 'round',
                  lineTension: 0.5,
                  fill: false,
                  backgroundColor: '#72B83B',
                  borderColor: '#72B83B',
                  pointRadius: 0,
                  borderJoinStyle: 'miter'
              }
          ]
      },
      options: {
        legend: {
          display: false
        },
        layout: {
          padding: {
              left: 20,
              right: 20,
              top: 20,
              bottom: 20
          }
        },
        scales: {
            xAxes: [{
              gridLines: {
                color: 'white', // couleur des lignes de l'axe
              },
              scaleLabel: {
                display: false // affichage de la légende des x
              },
              ticks: {
                autoSkip: false,
                maxRotation: 90,  // rotation de la légende
                minRotation: 90  // rotation de la légende
              }
            }]
        },
      },
   });
  }

}

````
[Back to top](#ui-components)  



### ng2-google-chart
[Back to top](#ui-components)  

[ng2-google-chart demo](https://www.devrandom.it/software/ng2-google-charts/demo/)    
[ng2-google-chart](https://www.devrandom.it/software/ng2-google-charts/)    
[Google GeoChart doc](https://developers.google.com/chart/interactive/docs/gallery/geochart#text-geocharts)    


*installation*

```
npm install ng2-google-charts --save
```

*import library in index.html*

````
  <script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
  <script type="text/javascript">
    google.charts.load('current', {
      'packages':['geochart'],
      // Note: you will need to get a mapsApiKey for your project.
      // See: https://developers.google.com/chart/interactive/docs/basic_load_libs#load-settings
      'mapsApiKey': <YOUR_API_KEY>
    });
  
  </script>
````


The way this library works, you’ll have to import Google Charts in your *page.module.ts* using

```import { Ng2GoogleChartsModule } from 'ng2-google-charts';```

and also include *Ng2GoogleChartsModule* in the *@NgModule* imports in *app.module.ts*.

#### colored world map sample
[Back to top](#ui-components)  

*controller file*

````
import { Component, OnInit } from '@angular/core';
import { Ng2GoogleChartsModule } from 'ng2-google-charts';
import { GoogleChartInterface } from 'ng2-google-charts/google-charts-interfaces';

@Component({
  selector: 'app-worldmap',
  templateUrl: './worldmap.page.html',
  styleUrls: ['./worldmap.page.scss'],
})
export class WorldmapPage implements OnInit {

  public gChart: GoogleChartInterface;


  public geoChart: GoogleChartInterface = {
    chartType: 'GeoChart',
    dataTable: [
      ['Country', 'Population (2019)'],
      ['Austria',	8858775],
      ['Belgium',	11467923],
      ['Bulgaria', 7000039],
      ['Croatia',	4076246],
      ['Cyprus',	875898],
      ['Czech Republic', 10649800],
      ['Denmark',	5806081],
      ['Estonia',	1324820],
      ['Finland',	5517919],
      ['France',	67028048],
      ['Germany',	83019214],
      ['Greece',	10722287],
      ['Hungary',	9797561],
      ['Ireland',	4904226],
      ['Italy',	60359546],
      ['Latvia', 1919968],
      ['Lithuania',	2794184],
      ['Luxembourg', 613894],
      ['Malta',	493559],
      ['Netherlands',	17282163],
      ['Poland', 37972812],
      ['Portugal', 10276617],
      ['Romania',	19401658],
      ['Slovakia', 5450421],
      ['Slovenia', 2080908],
      ['Spain',	46934632],
      ['Sweden', 10230185],
    ],
    options: {
      //region: '150', // Europe
      colorAxis: {colors: ['#ffc107', '#fd7e14', '#dc3545']},
      /*backgroundColor: '#9cf',
      datalessRegionColor: '#f8f9fa',*/
      defaultColor: '#6c757d',
      //height: 600, // => if you want to set a specific height (width is not necessary)
    }
  };
  constructor() { }
}
````

*View file*

````
<ion-content>
  <google-chart [data]="geoChart"></google-chart>
</ion-content>
````

#### Make ng2-google-chart responsive
[Back to top](#ui-components)  

> **Warning** : By default, ng2-google-chart are not responsive !

To avoid this, here is a self-working example based on the previous colorised worldmap. To make google chart responsive, you need to listen for *window.onresize* event

````
export class WorldmapPage implements OnInit {
  public geoChart: GoogleChartInterface;

  constructor(private ngZone: NgZone) { 
    window.onresize = (e) => {
        this.ngZone.run(() => {
            console.log("Width: " + window.innerWidth);
            console.log("Height: " + window.innerHeight);
            this.refreshMap(window.innerHeight);
        });
    };
  }

  ngOnInit() {
    this.refreshMap(0, 600);
  }

  refreshMap(height) {
    this.geoChart = {
      chartType: 'GeoChart',
      dataTable: [
        ['Country', 'Population (2019)'],
        ['Austria',	8858775],
        ['Belgium',	11467923],
        ['Bulgaria', 7000039],
        ['Croatia',	4076246],
        ['Cyprus',	875898],
        ['Czech Republic', 10649800],
        ['Denmark',	5806081],
        ['Estonia',	1324820],
        ['Finland',	5517919],
        ['France',	67028048],
        ['Germany',	83019214],
        ['Greece',	10722287],
        ['Hungary',	9797561],
        ['Ireland',	4904226],
        ['Italy',	60359546],
        ['Latvia', 1919968],
        ['Lithuania',	2794184],
        ['Luxembourg', 613894],
        ['Malta',	493559],
        ['Netherlands',	17282163],
        ['Poland', 37972812],
        ['Portugal', 10276617],
        ['Romania',	19401658],
        ['Slovakia', 5450421],
        ['Slovenia', 2080908],
        ['Spain',	46934632],
        ['Sweden', 10230185],
      ],
      options: {
        //region: '150', // Europe
        colorAxis: {colors: ['#ffc107', '#fd7e14', '#dc3545']},
        /*backgroundColor: '#9cf',
        datalessRegionColor: '#f8f9fa',*/
        defaultColor: '#6c757d',
        height: height,	// => remove if you want 100% page height chart
      }
    };
  }
}
````

> **To note** : If you remove the height property of geochart options, the chart will automatically fit the page

## Grid
[Back to top](#ui-components)  

[link : complex layout using grid and flexbox](http://www.joshmorony.com/an-in-depth-look-at-the-grid-system-in-ionic-2/) 

```
<ion-grid>
	<ion-row>
		<ion-col size="6" sizeMd="4" sizeXl="3" *ngFor="let i of dataset">
			<div class="image-container" [style.background-image]="'url(../assets/imgs/' + i.image + ')'">
			</div>
		</ion-col>
	</ion-row>
</ion-grid>
```


**Explanation** : 
```size="6"``` set the coloumn size for small screen (= 2 columns), ```sizeMd="4"``` set the coloumn size for medium screen (= 3 columns), ```sizeXl="3"``` set the coloumn size for very large screen screen (= 4 columns)

**Note** : You can also use ```sizeLg,  sizeSm sizeXs``

## toggle
[Back to top](#ui-components)  

*View.html*

```xml
...
<ion-content padding>
  <ion-item>
    <ion-label>Tracking</ion-label>
    <ion-toggle slot="end" [(ngModel)]="notificationToggled" (ngModelChange)="refreshNotification()"></ion-toggle>
  </ion-item>
</ion-content>
```

*View.ts*

```javascript
notificationToggled = false;

refreshNotification(){
   console.log("notificationToggled " + this.notificationToggled);
}
```

## Dynamic style
[Back to top](#ui-components)  

You can dynamically change the UI component style with condition like below :

```xml
<ion-button [style.backgroundColor]="enable ? '#00CC00' : '#FF0000'">Test</ion-button>
```

## Toggle menu
[Back to top](#ui-components)  

To add a toggle menu in your pages, follow the example below

*myPage.html*
```xml
<!-- here is the menu -->
<ion-menu [content]="content">
  <ion-content>
    <ion-list>
      <ion-item>A</ion-item>
      <ion-item>B</ion-item>
    </ion-list>
  </ion-content>
</ion-menu>

<!-- the page header -->
<ion-header>
  <ion-navbar color="githubviolet">
    <ion-button menuToggle>
      <ion-icon name="menu"></ion-icon>
    </ion-button>
    <ion-title>{{username}}</ion-title>  
     <ion-buttons slot="end">
        <ion-button (click)="openSearch()">
          <ion-icon slot="icon-only" name="search"></ion-icon>
        </ion-button>
      </ion-buttons>
  </ion-navbar>
</ion-header>

<!-- page content -->
<ion-content padding class="main" #content>
	<ion-list>
		...
	</ion-list>
</ion-content>
```


## Expandable header
[Back to top](#ui-components)  

See the complete Josh Morony's tutorial at : 
[link : Josh Morony tutorial](https://www.joshmorony.com/creating-a-custom-expandable-header-component-for-ionic-2/)     

Here is a little sample :

First, create a new component in your ionic project ```ionic g component expandableHeader```

*Home.html*

```html
<ion-header>
    <expandable [scrollArea]="mycontent">
        <ion-item no-lines>
            <ion-label>Username</ion-label>
            <ion-input type="text"></ion-input>
        </ion-item>
        <ion-item no-lines>
            <ion-label>Password</ion-label>
            <ion-input type="text"></ion-input>
        </ion-item>
    </expandable>
</ion-header>

<ion-content #mycontent fullscreen padding>

</ion-content>
```

**Warning** Do not forget *fullscreen* property in the ```<ion-content>``` tag

*Home.scss*
```css
.ios, .md {
    page-home {
        ion-item {
            border-radius: 50px !important;
            padding-left: 10px !important;
            margin-bottom: 10px;
            background-color: #fff;
            opacity: 0.7;
            transition: 0.3s linear;
        }
    }
}
```

*expandableHeader.html*
```html
<ng-content></ng-content>
```

*expandableHeader.ts*

```javascript
import { Component, Input, ElementRef, Renderer} from '@angular/core';

classe du composant
export class expandableHeader {

    @Input('scrollArea'): any;

    headerHeight: any;
    newHeaderHeight: any;

    constructor(public element: ElementRef, public renderer: Renderer) {
        this.headerHeight = 150;
        this.renderer.setElementStyle(this.element.nativeElement, 'height', this.headerHeight + 'px');
    }

    ngOnInit() {
	// Listen to the scroll event
        this.scrollArea.ionScroll.subscribe((ev) => {
            this.resizeHeader(ev);
        });
    }

    // Resize header content when scrolling
    resizeHeader(ev){
        // update dom
        ev.domWrite(() => {

            this.newHeaderHeight = this.headerHeight - ev.scrollTop;

            if(this.newHeaderHeight < 0){
                this.newHeaderHeight = 0;
            }
            this.renderer.setElementStyle(this.element.nativeElement, 'height', this.newHeaderHeight + 'px');

            // Trigger when the fileds must be masked
            for(let headerElement of this.element.nativeElement.children){
                let totalHeight = headerElement.offsetTop + headerElement.clientHeight;

                if(totalHeight > this.newHeaderHeight){
                    this.renderer.setElementStyle(headerElement, 'opacity', '0');
                } else {
                    this.renderer.setElementStyle(headerElement, 'opacity', '1');
                }
            }
        });
    }
}
```

*expandableHeader.scss*

```css
expadableHeader {
    display: block;
    background-color: map-get($color, light);
    overflow: hidden;
}
```

## List accordion
[Back to top](#ui-components)  

[link : accordion-list](https://ionicacademy.com/accordion-list-ionic/)

## Picker
[Back to top](#ui-components)  

[Custom picker component]https://github.com/raychenfj/ion-multi-picker

First install the component with the following comand line ```npm install ion-multi-picker --save```

*app.module.ts*

```javascript
import { MultiPickerModule } from 'ion-multi-picker';

@NgModule({
  declarations: [
    MyApp,
    AboutPage,
    ContactPage,
    HomePage,
    TabsPage,
  ],
  imports: [
    IonicModule.forRoot(MyApp),
    MultiPickerModule //Import MultiPickerModule
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp,
    AboutPage,
    ContactPage,
    HomePage,
    TabsPage,
  ],
  providers: []
})
export class AppModule {}
```

*Controller file*

```javascript
import { Component } from '@angular/core';
import { NavController } from 'ionic-angular';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
  temperature: any[] = [];
  simpleColumns: any[] = [];

  getDataset(min: number, max: number, step: number, unit: string = ""){
    for(let i = min; i < max; i+= step){
      this.temperature.push({
        text: i.toFixed(1).toString()+unit,
        value: i.toFixed(1).toString()
      });
    }
  }
  constructor(public navCtrl: NavController) {
    this.getDataset(35,43,0.10,"°C");
   
    this.simpleColumns = [
      {
        name: 'col1',
        options: this.temperature
      }
    ];
  }
}
```

*View file*

```html
<ion-content padding>
  <ion-item>
    <ion-label>Simple Picker</ion-label>
    <ion-multi-picker cancelText="Fermer" doneText="Valider" item-content [multiPickerColumns]="simpleColumns"></ion-multi-picker>
  </ion-item>
</ion-content>
```
## Alert Controller
[Back to top](#ui-components)  

Ionic 4 Alert Controller syntax
```
import { AlertController } from '@ionic/angular';

const alert = await this.alertCtrl.create({
      header: titre || '',
      message: message,
      buttons: ['OK']
    });
await alert.present();
```

## round progress bar
[Back to top](#ui-components)  

[Round progress github](https://github.com/crisbeto/angular-svg-round-progressbar/tree/master/demo) .    

**Angular plugin installation**
```
npm install angular-svg-round-progressbar --save
```

**Configure your home.module.ts**

```
import { RoundProgressModule } from 'angular-svg-round-progressbar';
import { HomePage } from './home.page';

@NgModule({
  imports: [
    CommonModule,
    RoundProgressModule,
    FormsModule,
    IonicModule,
    RouterModule.forChild([
      {
        path: '',
        component: HomePage
      }
    ])
  ],
  declarations: [HomePage]
})
export class HomePageModule {}
```
**View file**
```
 <div class="progress-wrapper">
      <div class="current" [ngStyle]="getOverlayStyle()">{{ current }}/{{ max }}</div>
  
      <round-progress
      [current]="current"
      [max]="max"
      [stroke]="stroke"
      [radius]="radius"
      [semicircle]="semicircle"
      [rounded]="rounded"
      [responsive]="responsive"
      [clockwise]="clockwise"
      [color]="gradient ? 'url(#gradient)' : color"
      [background]="background"
      [duration]="duration"
      [animation]="animation"
      [animationDelay]="animationDelay"></round-progress>
    </div>
</div>
```

**Style file**
```
.current {
    position: absolute;
    color: #bbb;
    font-weight: 100;
    line-height: 1;
}
.progress-wrapper {
    position: relative;
    margin: 20px auto;
    font-size: 40px;
}
round-progress {
    margin: auto;
}
```

**Controller file**
```
  max = 100;
  current = 35;
  color = '#45ccce';
  background = '#eaeaea';
  radius = 50;
  semicircle = false;

  stroke = 12;
  rounded = false;
  responsive = false;
  clockwise = true;
  duration = 800;
  animation = 'easeOutCubic';
  animationDelay = 0;
  animations: string[] = [];
  gradient = false;
  realCurrent = 0;
  
  getOverlayStyle() {
    let isSemi = this.semicircle;
    let transform = (isSemi ? '' : 'translateY(-50%) ') + 'translateX(-50%)';

    return {
      'top': isSemi ? 'auto' : '50%',
      'bottom': isSemi ? '5%' : 'auto',
      'left': '50%',
      'transform': transform,
      '-moz-transform': transform,
      '-webkit-transform': transform,
      'font-size': this.radius / 3.5 + 'px'
    };
  }
```

## ion-slide
[Back to top](#ui-components)  

[Video : How to build Dynamic Ionic 4 Slides with Shopping Cart](https://www.youtube.com/watch?v=6sXz2swm6Sw&ab_channel=SimonGrimm)      
[Web : How to build Dynamic Ionic 4 Slides with Shopping Cart](https://devdactic.com/dynamic-ionic-4-slides/)      

*view sample*

````
<ion-content>
  <div *ngFor="let cat of items" class="category-block">
 
    <ion-row no-padding class="category-banner">
      <ion-col text-left button tappable (click)="cat.expanded = !cat.expanded" align-self-center>
        {{ cat.category }}
      </ion-col>
    </ion-row>
 
    <ion-slides [options]="sliderConfig">
      <ion-slide *ngFor="let product of cat.products">
        <div *ngIf="cat.expanded">
          <ion-card>
            <ion-card-header>
              <ion-card-title>
                {{ product.name }} - ${{ product.price }}
              </ion-card-title>
              <ion-card-content>
                <img src="https://via.placeholder.com/300x300">
                <ion-button expand="full" (click)="addToCart(product)">Add to Cart</ion-button>
              </ion-card-content>
            </ion-card-header>
          </ion-card>
        </div>
      </ion-slide>
    </ion-slides>
 
  </div>
</ion-content>
````

*controller sample*

````
export class HomePage implements OnInit {
 
  cart = [];
  items = [];
 
  sliderConfig = {
    slidesPerView: 1.6,
    spaceBetween: 10,
    centeredSlides: true
  };
 
  constructor(private router: Router, private cartService: CartService) { }
 
  ngOnInit() {
    this.items = this.cartService.getProducts();
    this.cart = this.cartService.getCart();
  }
 
  addToCart(product) {
    this.cartService.addProduct(product);
  }
 
  openCart() {
    this.router.navigate(['cart']);
  }
}
````

> Note : you can set slidesPerView: 1.6 to see the start of the next comming card

*sample styling*
````
ion-badge {
    color: #fff;
    position: absolute;
    top: 0px;
    right: 0px;
    border-radius: 100%;
} 
.category-block {
    margin-bottom: 4px;
} 
.category-banner {
    border-left: 8px solid var(--ion-color-secondary);
    background: var(--ion-color-light);
    height: 40px;
    padding: 10px;
    font-weight: 500;
}
````



### Make ion-slide responsive

To make your *ion-slide* responsive when you resizing screen, you need to add the *breakpoints* property to your *ion-slide* configuration :

*controller.ts*

```
export class HomePage {

	sliderConfig = {
		slidesPerView : 4, 	// default number of slide 
		breakpoints: {
			// when window width is >= 320px
			320: {
				slidesPerView : 1
			},
			// when window width is >= 768px
			768: {
				slidesPerView : 2
				//spaceBetween: 40
			},
			// when window width is >= 1024px
			1024: {
				slidesPerView : 3
				//spaceBetween: 20
			},
			// when window width is >= 1200px
			1200: {
				slidesPerView : 4
				//spaceBetween: 10
			}
		},
		initialSlide: 0,
		spaceBetween: 8,
		centeredSlides: false
	};
	
	constructor() {}
}
```

*view file*

```
<ion-slides [options]="sliderConfig">
	<ion-slide *ngFor="let i of slides"></ion-slide>
</ion-slides>
```

## Hiding header on scroll
[Back to top](#ui-components)  

[Hide header video](https://www.youtube.com/watch?v=rS5EYgFD2Kw)   

### Create the directive

```
ionic g directive directives/hideHeader
```

This directive will trigger on the scroll event and apply a *margin-top* on the header element.

*hide-header.directive.ts*

```
import { element } from 'protractor';
import { Directive, Input, OnInit, Renderer2 } from '@angular/core';
import { DomController } from '@ionic/angular';

@Directive({
  selector: '[appHideHeader]',
  host: {
    '(ionScroll)': 'onContentScroll($event)'
  }
})
export class HideHeaderDirective implements OnInit{

  @Input('header') header: any;
  private lastY: number = 0;

  constructor(private renderer: Renderer2,
              private domCtrl: DomController) {}

  ngOnInit() {
    this.header = this.header.el;

    this.domCtrl.write(() => {
      this.renderer.setStyle(this.header, 'transition', 'margin-top 400ms');
    });
  }

  onContentScroll(event: any) {
    if (event.detail.scrollTop > this.lastY) {
      console.log('down');
      this.renderer.setStyle(this.header, 'margin-top', `-${this.header.clientHeight}px`);
      
    } else {
      this.renderer.setStyle(this.header, 'margin-top', '0');
    }
    this.lastY = event.detail.scrollTop;
  }
}

```

### Create a global component
[Back to top](#ui-components)  

It is recommended to create a global component containing the hideHeader directive to avoid multiple declaration in multiple pages. This component will be added in each other components. 

=> Create a new directory 'components' containing the following file

*components.module.ts*

```
import { HideHeaderDirective } from '../directives/hide-header.directive';
import { IonicModule } from '@ionic/angular';
import { CommonModule } from '@angular/common';
import { NgModule } from '@angular/core';

@NgModule({
	imports: [
		CommonModule,
		IonicModule
	],
	declarations: [
		HideHeaderDirective
	],
	exports: [
		HideHeaderDirective
	],
})
export class ComponentsModule {}
```

### Add the ComponentsModule in other components

In each page, add the componentsModule import into the *my-page.module.ts* file

*home.module.ts*

```
import { ComponentsModule } from './../components/components.module';
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
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
    IonicModule,
    ComponentsModule,
    RouterModule.forChild(routes)
  ],
  declarations: [HomePage]
})
export class HomePageModule {}
```

### Link the directive to your header
[Back to top](#ui-components)  

Finally, you need to add an id **#header** on your ```<ion-header>``` tag and link on the scroll event **appHideHeader scrollEvents="true" [header]="header"** on the ```<ion-content>``` tag.

*home.html*

```
<ion-header #header>
  <ion-toolbar color="primary">
    <ion-title class="navTitle">Matchs</ion-title>
  </ion-toolbar>
</ion-header>

<ion-content padding appHideHeader scrollEvents="true" [header]="header">
```

## Notification badge on icon
[Back to top](#ui-components)  

<img src="https://github.com/gsoulie/ionic2-resources/blob/master/notification-badge.png" align="center" width="250">

*View file*

```
<div class="notifications-badge">
   <ion-badge color="accent">399</ion-badge>
   <ion-icon slot="icon-only" class="box-shadow" name="list" mode="ios" (click)="onClick()"></ion-icon>
</div>
```

*Style file*

```
.notifications-badge {
    width: 50px;
    height: 50px;
    position: relative;
    
    ion-badge {
        background-color: #EB5C60;
        top: -0.1rem;
        right: -0.2rem;
	opacity: 0.9;
        text-align: center;
        position: absolute;
        z-index: 100;

	& ~ ion-icon {
            //margin-right: 1.2rem;
            z-index: 90;
            position: absolute;
            margin: auto;
            top: 0;
            bottom: 0;
            left: 0;
            right: 0;
            border-radius: 200px;
            background-color: white;
            color: #179eb8;
            font-size: 16pt !important;
            padding: 10px;
            cursor: pointer;
        }
    }
}
.box-shadow {
    box-shadow: 1px 1px 4px #0000001A;
}
```

## Input with autocomplete datalist
[Back to top](#ui-components)  

Make an input field with autocomplete dropdown using html datalist

### Standard autocomplete

*View file*
````
<ion-item>
  <input id="criteriaInput" list="autocompleteValues"  placeholder="Criteria" [(ngModel)]="searchValue">
</ion-item>
<datalist id="autocompletesValues">
  <option *ngFor="let i of advancedFilters" value="{{i.name}}"></option>
</datalist>
````

### Add some conditions when typing

Now we want to show the dropdown only when user enters 1 character or more. 

To achieve this, first **remove the *id*** attribute from your *datalist* tag

*View file*
````
<ion-item>
  <input id="criteriaInput" list="autocompleteValues"  placeholder="Criteria" [(ngModel)]="searchValue">
</ion-item>
<datalist>
  <option *ngFor="let i of advancedFilters" value="{{i.name}}"></option>
</datalist>
````

*Controller file*

````
ngAfterViewInit() {
    // Adds a keyup listener on the input.
    this.elementRef.nativeElement.querySelector('#criteriaInput').addEventListener('keyup', (e) => {
        // If input value is longer or equal than 1 chars, adding "autocompleteValues" on ID attribute.
        if (e.target.value.length >= 1) {
            this.elementRef.nativeElement.querySelector('datalist').setAttribute('id', 'autocompleteValues');
        } else {
            this.elementRef.nativeElement.querySelector('datalist').setAttribute('id', '');
        }
      });
  }
````

## Action sheet customization
[Back to top](#ui-components)  

Related to this issue (https://github.com/ionic-team/ionic-framework/issues/17300#issuecomment-627566107), in order to customize action sheet style, you must provide a css class at the end of your *global.scss* file.

*controller file*
````
async openMenu(): Promise<void> {
    const actionSheet = await this.actionSheetCtrl.create({
      cssClass: 'match-item-action-sheet',
      buttons: {
        text: 'Check all as seen',
        handler: () => {
          this.checkAll();
        }
      }]
    });
    await actionSheet.present();
  }
````
	
*global.scss*
````
// style for container
.match-item-action-sheet .action-sheet-group:first-of-type {
    border-top-left-radius: 20px !important;
    border-top-right-radius: 20px !important;
    padding-top: 20px;
    padding-bottom: 20px;
}
// style for item icon
.action-sheet-icon {
    color: #70B62C !important;
}
// style for item button text
.action-sheet-button {
    color: #70B62C !important;
}
````

## ion-toast
[Back to top](#ui-components)  

Avoid mutli-toast stacking

In order to avoid toast stacking, you could store your Toast object in a variable outside your function, and call the dismiss() method before showing the next toast

*Controller file*

````
 toast: HTMLIonToastElement; 
 
 async presentToast() {
 try {
	this.toast.dismiss();
      } catch(e) {}

      this.toast = this.toastController.create({
        message: message,
        position: 'top',
        duration: 5000,
        color: 'danger',
        showCloseButton: true
      });
      await this.toast.present();
 }
````

## ion-sliding-item swipe to delete
[Back to top](#ui-components)  

If you don't want to use gesture with animation to achieve swipe to delete action on item list, you can use the basic *ion-item-sliding* element 

[Gesture with animation](https://github.com/gsoulie/ionic2-resources/blob/master/ionic-gesture.md)       

The inconvenient to use the basic *ion-item-sliding* with delete on swipe is that there is no animation and the item removing is not smooth.

*View file*

````
  <ion-list>
    <ion-item-sliding *ngFor="let study of studies; let idx = index" (ionSwipe)="delete(idx)">  
      <ion-item>
      	<!-- you can put a ion-card here !! -->
        {{ study.name }}
      </ion-item>           
      <ion-item-options side="start">
        <ion-item-option color="danger" expandable>Delete</ion-item-option>
      </ion-item-options>   
      <ion-item-options side="end">
        <ion-item-option expandable>Bookmark</ion-item-option>
      </ion-item-options>   
    </ion-item-sliding>
  </ion-list>
````

*Controller file*

````
delete(idx) {
// TODO : check the swipe side to delete or doing an other action
	if (idx < this.studies.length) {
		this.studies.splice(idx, 1);
	}
}
````
