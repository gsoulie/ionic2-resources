[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# UI Components

* [ion-button](#ion-button)    
* [modal](#modal)    
* [ion-select](#ion-select)    
* [ion-item](#ion-item)    
* [ion-list](#ion-list)  
* [ion-label](#ion-label)    
* [searchbar](#searchbar)    
* [list filtering](#high-performance-list-filtering)    
* [ion-tab](#tab)       
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

## Tab
[Back to top](#ui-components)  

According to the latest Android material guideline, you have to follow these rules :

- 3 to 5 destinations => use the bottom navigation
- Less than 3 => use tab on the top
- More than 5 => use another solution like slide menu

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

### Hide tab on sub pages

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

### Hide tab on specific page

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

It's pretty easy to do actually. When you set a tabIcon property, Ionic sets a class name on the tab, based on the name you provided. So for example, if you set ```tabIcon="customicon"```, then the resulting class names will be ```.ion-ios-hygglo-customicon``` and ```.ion-ios-hygglo-customicon-outline``` (for selected tabs) for iOS. On Android, the prefix will be ```.ion-md-``` instead of ```.ion-ios-```.

Then just create a custom css, something like this:

```css
.ion-ios-customicon,
.ion-md-customicon {
  content: url(../../assets/img/ui/customicon.svg);
  width: 24px;
  height: 32px;
  padding: 6px 4px 2px;
  opacity: 0.9;
}
```

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

### Sample code

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

  @ViewChild('lineCanvas', {static: false}) lineCanvas;
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

### Pie chart
[Back to top](#ui-components)  

*view file* 
```
<canvas #myCanvas></canvas>
```

*controller file*
```
export class MyClass {
	@ViewChild('myCanvas') myCanvas: ElementRef;
	
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

## grid
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
    <ion-toggle [(ngModel)]="val" black checked="false" (ionChange)="refreshTracking($event)"></ion-toggle>
  </ion-item>
</ion-content>
```

*View.ts*

```javascript
refreshTracking(e){
   console.log("refreshTracking " + e.checked);
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
