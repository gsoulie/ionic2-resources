[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# UI Components

* [ion-button](#ion-button)    
* [ModalController](#modalcontroller)    
* [ion-select](#ion-select)    
* [ion-item](#ion-item)    
* [ion-list](#ion-list)  
* [pagination](#pagination)  
* [ion-label](#ion-label)    
* [ion-searchbar](#ion-searchbar)    
* [list filtering](#high-performance-list-filtering)    
* [ion-tab](#ion-tab)       
* [tab icon](#tab-icon)    
* [ion-fab](#ion-fab)  
* [chart](https://github.com/gsoulie/ionic2-resources/blob/master/ionic-chart.md)    
* [ion-grid](#grid)    
* [ion-toggle](#ion-toggle)    
* [dynamic style](#dynamic-style)    
* [Toggle menu](#toggle-menu)    
* [Expandable header](#expandable-header)    
* [List accordion](#list-accordion)    
* [Picker](#picker)     
* [round progress bar](#round-progress-bar)     
* [Swiper (replace ion-slide from v6)](#swiper-replace-ion-slide-from-v6)     
* [ion-slide (deprecated since v6)](#ion-slide-deprecated-since-v6)    
* [Hiding header on scroll](#hiding-header-on-scroll)     
* [Notification badge on icon](#notification-badge-on-icon)    
* [Input with autocomplete datalist](#input-with-autocomplete-datalist)     
* [Action sheet customization](#action-sheet-customization)        
* [ion-toast](#ion-toast)       
* [ion-sliding-item swipe to delete](#ion-sliding-item-swipe-to-delete)       
* [swipe to delete with gesture](#swipe-to-delete-with-gesture)       
* [iOS keyboard scroll](#ios-keyboard-scroll)     
* [ion-range](#ion-range)    
* [ion-title](#ion-title)    
* [ion-checkbox](#ion-checkbox)     
* [ion-datetime](#ion-datetime)     
* [ion-infinite-scroll](#ion-infinite-scroll)        

## ion-button
[Back to top](#ui-components)  

### options

```html
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
```html
<ion-button class="btnValueChecked allowMultipleLines" fill="clear">My very very long text button with multiple line</ion-button>
```

*style file*
```css
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

## ModalController
[Back to top](#ui-components)  

### Syntax

*Parent Controller*

````typescript
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

````typescript
constructor(private modalCtrl: ModalController) {}

onClose() {
	this.modalCtrl.dismiss(myUserIsLogged)
}
````

### Passage de paramètres via Input
[Back to top](#ui-components)  

*home.component.ts*

````typescript
async openModal() {
	const modal = this.modalCtrl.create({
		component: ModalPage,
		componentProps: { username: 'Jack', userage: 24 }
	});
	
	await modal.present();
	
	const { data } = await modal.onDidDismiss();
	console.log(data);
}
````


*modal.component.ts*

````typescript
export class ModalPage {

	@Input() username: string;
	@Input() userage: number;
	
	constructor(private modalCtrl: ModalController) {}
	
	close() {
		this.modalCtrl.dismiss({ myreturnedvalue: 42 });
	}
}
````

### Passage de paramètres avec BehaviourSubject
[Back to top](#ui-components)  

*home.component.ts*

````typescript
async openModal() {
	const mySubject = new BehaviourSubject(null);
	
	const modal = this.modalCtrl.create({
		component: ModalPage,
		componentProps: { username: 'Jack', userage: 24, subject: mySubject }
	});
	
	await modal.present();
	
	mySubject.subscribe((res) => {
		console.log('behaviour subject value : ', res);
	});
	
	const { data } = await modal.onDidDismiss();
	console.log(data);
}
````


*modal.component.ts*

````typescript
export class ModalPage {

	@Input() username: string;
	@Input() userage: number;
	@Input() subject: BehaviourSubject<string>;
	
	constructor(private modalCtrl: ModalController) {}
	
	close() {
		this.subject.next('A new value was sent !');
		this.modalCtrl.dismiss({ myreturnedvalue: 42 });
	}
}
````

### Other example
[Back to top](#ui-components)  

Consider that we have a page (UserListPage for example) with a button wich open a modal based on UserPage controller.

*UserListPage Controller file*

```typescript
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

```typescript
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
```html
 <ion-item>
    <ion-label>Profession</ion-label>
	 <ion-select (ionChange)="onFiltering($event.detail.value)"
      	interface="action-sheet" placeholder="Select your item" [(ngModel)] ="selectedItem" name="cbo">
        <ion-select-option *ngFor="let item of itemList" [value]="item.id">{{ item.id }} - {{ item.name }}</ion-select-option>
      </ion-select>
 </ion-item>
```

*Controller file*

```typescript
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

- show right arrow : add ```detail``` property on ion-item ```<ion-item detail>```
- hide right arrow : add ```detail="false"``` property on ion-item  ```<ion-item detail="false">```
- hide item line : add ```lines="none"``` property on ion-item  ```<ion-item lines="none">```

*Different type of lines*

```html
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

### ion-item router-direction

It is possible to add a ````router-direction```` when clicking on ion-item. It's value could be : "back" | "forward" | "root"

### ion-item with multiple lines

```html
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

```html
<ion-item *ngFor="let item of group.patients" detail>{{ item }}</ion-item>
```

And modify your *variable.sass* file to reflect the following 

```typescript
// App iOS Variables
// --------------------------------------------------
// iOS only Sass variables can go here
$item-ios-detail-push-show: true;
```

After that refresh your browser

### ion-list with clickable items

```html
<ion-list> 
    <button ion-item *ngFor="let item of items">{{item.fullname}}</button> 
</ion-list>
```

### Add filter on ion-list

Consider a ion-list in which we want to hide every items which property "deleted" is set to true

```html
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
[Back to top](#ui-components)  

```typescript
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

```html
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
[Back to top](#ui-components)  

### ion-item-sliding

```html
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

```html
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
[Back to top](#ui-components)  


*Controller file*

```typescript
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
[Back to top](#ui-components)  

#### Solution 1

[link : Scroll performance](http://www.joshmorony.com/boosting-scroll-performance-in-ionic-2/)
[link : virtual-scroll official doc](http://ionicframework.com/docs/v2/api/components/virtual-scroll/VirtualScroll/)

**Create new project**

```
ionic start virtual-scroll tabs --v2
```

**Create Data**

Modify Page1 and Page2 controller with :

```typescript
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
[Back to top](#ui-components)  

**Create List**

First we’re going to set up a standard list to display the data we created in the constructor.

Modify **page1.html** to reflect the following:
```html
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

```html
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
[Back to top](#ui-components)  

> **IMPORTANT** : In Framework v6, the ion-virtual-scroll component has been deprecated in favor of using framework-specific solutions.

[link : Infinite scroll](http://ionicframework.com/docs/v2/api/components/infinite-scroll/InfiniteScroll/)

*View file*

```html
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

```typescript
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
[Back to top](#ui-components)  

*View file*

```html
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

```typescript
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
    }];

    this.items = dataset;
  }

  openDetail(_idDevice, _deviceName){
    this.nav.push(FichePage,{idDevice: _idDevice, deviceName: _deviceName});
  }
}
```

*Change button backgroundColor dynamically*

```html
<ion-button[style.backgroundColor]="enable ? '#00CC00' : '#FF0000'">Test</ion-button>
```

### Pull to refresh
[Back to top](#ui-components)  

*View file*

```html
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

```typescript
doRefresh(refresher){
    this.users = getUsers();
    setTimeout(() => { refresher.complete(); console.log('Async operation has ended'); }, 2000); 
  }
```


### List with dividers

*View file*

```html
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
[Back to top](#ui-components)  

*Controller file*

```typescript
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
[Back to top](#ui-components)  

**Other example on complex list**

*JSON objects list*

```typescript
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

```html
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

````html
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

[Back to top](#ui-components)  

*Controller file*

````typescript
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
[Back to top](#ui-components)  

*Service file*

**Note :** Here, the service returns elements 50 by 50 starting from the passed page index
````typescript
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

[Back to top](#ui-components)  

*Style file*

**Important :** put the pagination style inside de page style, not in the global.scss
````css
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

```html
<ion-label position="floating">Datetime</ion-label>
```

### set long label

To show long label, add the following css to your ion-label

```css
.longLabel {
    white-space: pre-warp !important;
}
```

## ion-searchbar
[Back to top](#ui-components)  

> **Update** [see this tutorial for Observable update on searchbar](https://www.joshmorony.com/using-observables-in-ionic-3-9-x-and-angular-5/)    

*View file*

```html
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
```html
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
[Back to top](#ui-components)  

*Controller file*

```typescript
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

```html
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
[Back to top](#ui-components)  

*Controller file*

```typescript
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

```typescript
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

### Filtering on Observable array
[Back to top](#ui-components)  

````typescript
games$: Observable<GameDto[]>;

search(ev) {
 this.games$ = this.games$
    .pipe(
      map(item => item.filter(g => g.title.toLowerCase().indexOf(ev.target.value.toLowerCase()) > -1))
    );
 }
````

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

````html
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

````typescript
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

````typescript
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

```typescript
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

```typescript
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

```typescript
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

````html
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

````html
<svg xmlns="http://www.w3.org/2000/svg" width="16.426" height="15" viewBox="0 0 16.426 15"><defs><style>.a{fill:#009dbe;}.b{fill:#fff;stroke:#009dbe;stroke-width:1.5px;}.c{stroke:none;}.d{fill:none;}</style></defs><g transform="translate(0)"><path class="a" d="M21.073,58.843H17.641a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h3.432a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,21.073,58.843Z" transform="translate(-5.163 -52.41)"/><path class="a" d="M21.073,58.843H13.839a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h7.234a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,21.073,58.843Z" transform="translate(-5.163 -47.41)"/><path class="a" d="M7.977,58.843H5.68a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h2.3a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,7.977,58.843Z" transform="translate(-5.163 -57.41)"/><path class="a" d="M13.1,58.843H5.68a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5H13.1a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,13.1,58.843Z" transform="translate(-5.163 -52.41)"/><path class="a" d="M9.234,58.843H5.68a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5H9.234a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,9.234,58.843Z" transform="translate(-5.163 -47.41)"/><path class="a" d="M21.073,58.843H14.081a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h6.992a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,21.073,58.843Z" transform="translate(-5.163 -57.41)"/></g><g class="b" transform="translate(1.855)"><circle class="c" cx="2.5" cy="2.5" r="2.5"/><circle class="d" cx="2.5" cy="2.5" r="1.75"/></g><g class="b" transform="translate(9.855 5)"><circle class="c" cx="2.5" cy="2.5" r="2.5"/><circle class="d" cx="2.5" cy="2.5" r="1.75"/></g><g class="b" transform="translate(5.855 10)"><circle class="c" cx="2.5" cy="2.5" r="2.5"/><circle class="d" cx="2.5" cy="2.5" r="1.75"/></g></svg>
````

*svg file after modification*

````html
<svg xmlns="http://www.w3.org/2000/svg" width="16.426" height="15" viewBox="0 0 16.426 15"><defs><style>.a{}.b{stroke:#009dbe;stroke-width:1.5px;}.c{stroke:none;}.d{}</style></defs><g transform="translate(0)"><path class="a" d="M21.073,58.843H17.641a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h3.432a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,21.073,58.843Z" transform="translate(-5.163 -52.41)"/><path class="a" d="M21.073,58.843H13.839a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h7.234a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,21.073,58.843Z" transform="translate(-5.163 -47.41)"/><path class="a" d="M7.977,58.843H5.68a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h2.3a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,7.977,58.843Z" transform="translate(-5.163 -57.41)"/><path class="a" d="M13.1,58.843H5.68a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5H13.1a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,13.1,58.843Z" transform="translate(-5.163 -52.41)"/><path class="a" d="M9.234,58.843H5.68a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5H9.234a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,9.234,58.843Z" transform="translate(-5.163 -47.41)"/><path class="a" d="M21.073,58.843H14.081a.508.508,0,0,0-.516.5v1a.508.508,0,0,0,.516.5h6.992a.508.508,0,0,0,.516-.5v-1A.508.508,0,0,0,21.073,58.843Z" transform="translate(-5.163 -57.41)"/></g><g class="b" transform="translate(1.855)"><circle class="c" cx="2.5" cy="2.5" r="2.5"/><circle class="d" cx="2.5" cy="2.5" r="1.75"/></g><g class="b" transform="translate(9.855 5)"><circle class="c" cx="2.5" cy="2.5" r="2.5"/><circle class="d" cx="2.5" cy="2.5" r="1.75"/></g><g class="b" transform="translate(5.855 10)"><circle class="c" cx="2.5" cy="2.5" r="2.5"/><circle class="d" cx="2.5" cy="2.5" r="1.75"/></g></svg>


*Tab scss file*

````css
ion-tab-button {
    --color: black;
    --color-selected: red;
}
````

## ion-fab
[Back to top](#ui-components)  

This snippet show how to fix floating button in front of a list

```html
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
## Grid
[Back to top](#ui-components)  

[link : complex layout using grid and flexbox](http://www.joshmorony.com/an-in-depth-look-at-the-grid-system-in-ionic-2/) 

```html
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

## ion-toggle
[Back to top](#ui-components)  

*View.html*

```html
...
<ion-content padding>
  <ion-item>
    <ion-label>Tracking</ion-label>
    <ion-toggle slot="end" [(ngModel)]="notificationToggled" (ngModelChange)="refreshNotification()"></ion-toggle>
  </ion-item>
</ion-content>
```

*View.ts*

```typescript
notificationToggled = false;

refreshNotification(){
   console.log("notificationToggled " + this.notificationToggled);
}
```

## Dynamic style
[Back to top](#ui-components)  

You can dynamically change the UI component style with condition like below :

```html
<ion-button [style.backgroundColor]="enable ? '#00CC00' : '#FF0000'">Test</ion-button>
```

## Toggle menu
[Back to top](#ui-components)  

To add a toggle menu in your pages, follow the example below

*myPage.html*
```html
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

```typescript
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

```typescript
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

```typescript
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

## round progress bar
[Back to top](#ui-components)  

[Round progress github](https://github.com/crisbeto/angular-svg-round-progressbar/tree/master/demo) .    

**Angular plugin installation**
```
npm install angular-svg-round-progressbar --save
```

**Configure your home.module.ts**

```typescript
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
```html
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
```css
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
```typescript
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

## Swiper (replace ion-slide from v6)   
[Back to top](#ui-components)      

[Swiper Angular documentation](https://swiperjs.com/angular)     
[Swiper demos and code](https://swiperjs.com/demos#effect-fade)      

> IMPORTANT : By default Swiper Angular uses core version of Swiper (without any additional modules). If you want to use Navigation, Pagination and other modules, you have to install them first. Here is the list of additional modules imports:

````npm install swiper````

import module in page module file (lazy loading) or in *app.module.ts*

*swiper.page.module*

````typescript
import { SwiperModule } from 'swiper/angular';
...
import { SlidePage } from './slide.page';

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    IonicModule,
    SlidePageRoutingModule,
    SwiperModule
  ],
  declarations: [SlidePage]
})
export class SlidePageModule {}
````

### import global scss

*global.scss*
````@import 'swiper/css/bundle';	// import all swiper bundle css````

#### Initialisation

> A vérifier : non nécessaire depuis les dernières versions de Swiper

*slider.component.ts*

````typescript
export class SlidePage implements OnInit, AfterContentChecked {

  @ViewChild('swiper') swiper: SwiperComponent;
  constructor() { }

  ngOnInit() {
  }

  ngAfterContentChecked(): void {
      // if (this.swiper) {
      //   this.swiper.updateSwiper({});
      // }
  }

}
````

*slider.page.html*

````html

<ion-content>
	<swiper #swiper>
	  <ng-template swiperSlide>Slide 1</ng-template>
	  <ng-template swiperSlide>Slide 2</ng-template>
	  <ng-template swiperSlide>Slide 3</ng-template>
	</swiper>
</ion-content>
````

### Mise en forme

**TRES IMPORTANT**

Le swiper est protégé par la *ViewEncapsulation*, de fait, lorsqu'on ajoute du style à un slider via la classe *swiper-slide*, **rien ne se produit**. 
Il faut donc désactiver la *ViewEncapsulation* (encapsulation shadow dom) si l'on souhaite customiser le style du slider.

*slider.page.ts*

````typescript
@Component({
  selector: 'app-slide',
  templateUrl: './slide.page.html',
  styleUrls: ['./slide.page.scss'],
  encapsulation: ViewEncapsulation.None	// <-------------- important
})
export class SlidePage implements OnInit, AfterContentChecked {
````

*slider.page.scss*

````css
.swiper {
  width: 100%;
  height: 100%;

}
.swiper-slide {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
````

### Using specific modules (Navigation, Pagination, Scrollbar...)

> IMPORTANT : By default Swiper Angular uses core version of Swiper (without any additional modules). If you want to use Navigation, Pagination and other modules, you have to install them first. Here is the list of additional modules imports:

Using specific modules requires to import them before all :

````typescript
import { SwiperComponent } from 'swiper/angular';

// import Swiper core and required modules
import SwiperCore, { Navigation, Pagination, Scrollbar, A11y, EffectCube } from 'swiper';

// install Swiper modules
SwiperCore.use([Pagination, Navigation, Scrollbar, EffectCube]);


export class SlidePage implements OnInit, AfterContentChecked {

  @ViewChild('swiper') swiper: SwiperComponent;
  config: SwiperOptions = {
    pagination: true,	// <---- doing nothing if 'Pagination' module is not imported
	effect: 'cube'
  };
}
````

> Note : If you don't import specific module, using it will doing nothing

### Interactions

To dynamically interact with swiper properties / functions, you must use swiper's reference. You cannot directly use swiper object : 

````typescript
@ViewChild('swiper') swiper: SwiperComponent;

this.swiper.swiperRef.slideNext();
this.swiper.swiperRef.slidePrev();
this.swiper.swiperRef.allowTouchMove = this.touchAllowed;
...
````

### Cube effect

*slide.page.ts*

````typescript
// import Swiper core and required modules
import SwiperCore, { Pagination, EffectCube } from 'swiper';

// install Swiper modules
SwiperCore.use([Pagination, EffectCube]);

@Component({
  selector: 'app-slide',
  template: `
  <ion-content>
	<swiper #swiper [config]="config">
	  <ng-template swiperSlide *ngFor="let s of images">
		<img [src]="s" alt="">
	  </ng-template>
	</swiper>
	</ion-content>
  `,
  style: `
  .swiper {
	  width: 100%;
	}
`,
  encapsulation: ViewEncapsulation.None
})
export class SlidePage implements AfterContentChecked {

  @ViewChild('swiper') swiper: SwiperComponent;

  config: SwiperOptions = {
    slidesPerView: 'auto',
    pagination: true,
    effect: 'cube'
  };

  images = [
    'https://images.unsplash.com/photo-1580894742597-87bc8789db3d',
    'https://images.unsplash.com/photo-1580894742597-87bc8789db3d',
    'https://images.unsplash.com/photo-1580894742597-87bc8789db3d',
    'https://images.unsplash.com/photo-1580894742597-87bc8789db3d',
    'https://images.unsplash.com/photo-1580894742597-87bc8789db3d',
  ];
 ````
 
 ### Full basic sample
 
 *slider.page.html*
 
 ````html
 <ion-header>
  <ion-toolbar>
    <ion-title>slide</ion-title>
  </ion-toolbar>
</ion-header>

<ion-content>
<swiper #swiper [config]="config"
(slideChange)="swiperSlideChange($event)">
  <ng-template swiperSlide *ngFor="let s of [].constructor(10); let i = index">Slide {{ i }}
  </ng-template>
  <!-- <ng-template swiperSlide *ngFor="let s of images">
    <img [src]="s" alt="">
  </ng-template> -->
</swiper>
</ion-content>
<ion-footer [style.background]="'white'">
  <ion-row>
    <ion-col>
      <ion-button fill="clear" expand="block" (click)="slidePrevious()">Prev</ion-button>
    </ion-col>
    <ion-col>
      <ion-button expand="block" (click)="slideNext()">Next</ion-button>
    </ion-col>
  </ion-row>
</ion-footer>
````

*slider.page.ts*

````typescript
import { AfterContentChecked, Component, ViewChild, ViewEncapsulation } from '@angular/core';
import { SwiperOptions } from 'swiper';
import { SwiperComponent } from 'swiper/angular';

// import Swiper core and required modules
import SwiperCore, { Navigation, Pagination, Scrollbar, A11y, EffectCube } from 'swiper';

// install Swiper modules
SwiperCore.use([Pagination, EffectCube]);

@Component({
  selector: 'app-slide',
  templateUrl: './slide.page.html',
  styleUrls: ['./slide.page.scss'],
  encapsulation: ViewEncapsulation.None
})
export class SlidePage implements AfterContentChecked {

  @ViewChild('swiper') swiper: SwiperComponent;
  config: SwiperOptions = {
    //slidesPerView: 1.5,
    //spaceBetween: 50,
    pagination: true,
    initialSlide: 1,
    //allowTouchMove: false // disable swiping by touching
  };

  constructor() { }

  ngAfterContentChecked(): void {
      // if (this.swiper) {
      //   this.swiper.updateSwiper({});
      // }
  }

  swiperSlideChange(ev): void {
    console.log('slide change : ', ev);
  }
  slideNext(): void {
    this.swiper.swiperRef.slideNext();
    // this.swiper.swiperRef.slideTo(x);  // <---- slide to specific index
  }
  slidePrevious(): void {
    this.swiper.swiperRef.slidePrev();
  }

  /**
   * Enable/disable touch move dynamically
   */
  toggleTouch() {
    //this.touchAllowed = !this.touchAllowed;
    //this.swiper.swiperRef.allowTouchMove = this.touchAllowed;
  }
}
````

*slider.page.scss*

````css
.swiper {
  width: 100%;
  height: 100%;
}
.swiper-slide {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
````

## ion-slide (deprecated since v6)
[Back to top](#ui-components)  

[Video : How to build Dynamic Ionic 4 Slides with Shopping Cart](https://www.youtube.com/watch?v=6sXz2swm6Sw&ab_channel=SimonGrimm)      
[Web : How to build Dynamic Ionic 4 Slides with Shopping Cart](https://devdactic.com/dynamic-ionic-4-slides/)      

*view sample*

````html
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

````typescript
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
````css
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

*Remove automatic fixed size*

This will set the ion-slide width with the content width
````css
ion-slide {
    width: unset !important;
}
````

### Make ion-slide responsive

To make your *ion-slide* responsive when you resizing screen, you need to add the *breakpoints* property to your *ion-slide* configuration :

*controller.ts*

```typescript
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

```html
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

```typescript
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

```typescript
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

```typescript
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

```html
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

```html
<div class="notifications-badge">
   <ion-badge color="accent">399</ion-badge>
   <ion-icon slot="icon-only" class="box-shadow" name="list" mode="ios" (click)="onClick()"></ion-icon>
</div>
```

*Style file*

```css
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
````html
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
````html
<ion-item>
  <input id="criteriaInput" list="autocompleteValues"  placeholder="Criteria" [(ngModel)]="searchValue">
</ion-item>
<datalist>
  <option *ngFor="let i of advancedFilters" value="{{i.name}}"></option>
</datalist>
````

*Controller file*

````typescript
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
````typescript
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
````css
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

````typescript
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

**See good tutorial from Devdactic**

https://devdactic.com/gmail-clone-ionic-angular/
https://devdactic.com/gmail-swipe-to-delete-ionic/

#### Other way

If you don't want to use gesture with animation to achieve swipe to delete action on item list, you can use the basic *ion-item-sliding* element 

[Gesture with animation](https://github.com/gsoulie/ionic2-resources/blob/master/ionic-gesture.md)       

The inconvenient to use the basic *ion-item-sliding* with delete on swipe is that there is no animation and the item removing is not smooth.

*View file*

````html
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

````typescript
delete(idx) {
// TODO : check the swipe side to delete or doing an other action
	if (idx < this.studies.length) {
		this.studies.splice(idx, 1);
	}
}
````

## Swipe to delete with gesture

*parent.html*

````html
 <ion-list>
    <ion-list-header>Inbox</ion-list-header>
    <app-swipe-item *ngFor="let e of emails"
      [email]="e"
      (delete)="deleteEmail(e.id)">
    </app-swipe-item>
  </ion-list>
````

*parent.controller.ts*

````typescript
  emails: Email[];
  emailSubscription: Subscription;
  
  constructor(private dataService: DataService) { }

  ngOnInit() {
    this.emailSubscription = this.dataService.fetchEmail()
    .subscribe((res: Email[]) => {
      	this.emails = res
      });
    });
  }
  ngOnDestroy(): void {
    this.emailSubscription.unsubscribe();
  }
  
  deleteEmail(ev): void {
    this.emails = this.emails.filter(e => e.id !== ev);
  }
````

### Swipe item component

*item.html*

````html
<!-- Hidden wrapper (visible after swiping) containing action buttons -->
<div class="wrapper" #wrapper>
  <div class="column">
      <ion-icon name="trash-outline" color="light" class="ion-margin-start" #trash></ion-icon>
  </div>
  <div class="column" class="ion-text-right">
    <ion-icon name="archive-outline" color="light" class="ion-margin-end" #archive></ion-icon>
  </div>
</div>

<ion-item class="email" lines="none">
  <ion-row class="ion-align-items-center">
    <ion-col size="2" (click)="openDetail(email.id)">
      <div [style.background]="email.color" class="email-circle">{{ email.from | slice:0:1}}</div>
    </ion-col>
    <ion-col size="8" (click)="openDetail(email.id)">
      <ion-label
        color="dark"
        class="ion-text-capitalize ion-text-wrap"
        [style.font-weight]="!email.read ? 'bold' : ''">
          {{ email.from.split('@')[0] }}
          <p class="excerpt">
            {{ (email.content.length > 50) ? (email.content | slice:0:50)+'...' : (email.content)}}
          </p>
      </ion-label>
    </ion-col>
    <ion-col size="2">
      <div class="ion-text-right" tappable (click)="email.star = !email.star;">
        <p class="date">{{ email.date | date: 'dd. MMM' }}</p>
        <ion-icon
        [name]="email.star ? 'star' : 'star-outline'"
        [color]="email.star ? 'warning' : 'medium'"></ion-icon>
      </div>
    </ion-col>
  </ion-row>
</ion-item>

````

*item.scss*

````css
$row-height: 89px;

.email {
  //margin-bottom: 6px;
  height: $row-height;
  display: flex;
  background: white;

  .excerpt {
    padding-top: 4px;
  }
  .date { font-size: small;}
}
ion-item {
  --width: 100%;
}
.email-circle {
  border-radius: 50%;
  height: 40px;
  width: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 500;
  text-transform: capitalize;
  color: #efefef;
}
.wrapper {
  display: flex;
  flex-direction: row;
  align-items: center;
  width: 100%;
  height: $row-height;
  position: absolute;
  background: red;
}
.column {
  display: flex;
  flex: 1;
  flex-basis: 100%;
  flex-direction: column;
}
// active sur swipe
.rounded {
  border-radius: 10px;
  z-index: 2;
  box-shadow: 0px 6px 13px -5px rgb(0 0 0 / 28%);
}

````

*item.ts*

````typescript
import { AfterViewInit, Component, ElementRef, EventEmitter, Input, OnInit, Output, ViewChild } from '@angular/core';
import { Router } from '@angular/router';
import { Haptics, ImpactStyle } from '@capacitor/haptics';
import { AnimationController, GestureController, IonItem, Animation } from '@ionic/angular';

const ANIMATION_BREAKPOINT = 70;

@Component({
  selector: 'app-swipe-item',
  templateUrl: './swipe-item.component.html',
  styleUrls: ['./swipe-item.component.scss'],
})
export class SwipeItemComponent implements AfterViewInit {
  @Input() email: any;

  @ViewChild(IonItem, {read: ElementRef }) item: ElementRef;
  @ViewChild('wrapper') wrapper: ElementRef;
  @ViewChild('trash', {read: ElementRef}) trashIcon: ElementRef;
  @ViewChild('archive', {read: ElementRef}) archiveIcon: ElementRef;

  @Output() delete: EventEmitter<any> = new EventEmitter();

  bigIcon = false;
  trashAnimation: Animation;
  archiveAnimation: Animation;
  deleteAnimation: Animation;

  constructor(
    private router: Router,
    private gestureCtrl: GestureController,
    private animationCtrl: AnimationController) { }

  ngAfterViewInit() {
    this.setupAnimations();
    this.initializeGesture();
  }

  initializeGesture(): void {
    const style = this.item.nativeElement.style;
    const windowWidth = window.innerWidth;

    const moveGesture = this.gestureCtrl.create({
      el: this.item.nativeElement,
      gestureName: 'move',
      threshold: 0,
      onStart: ev => {
        style.transition = '';
      },
      onMove: ev => {
        style.transform = `translate3d(${ev.deltaX}px, 0, 0)`;
        this.item.nativeElement.classList.add('rounded');

        if (ev.deltaX > 0) {
          // swipe right
          this.wrapper.nativeElement.style['background-color'] = 'var(--ion-color-primary)';

        } else if (ev.deltaX < 0) {
          // swipe left
          this.wrapper.nativeElement.style['background-color'] = 'green';
        }

        // Animation icône de gauche
        if (ev.deltaX > ANIMATION_BREAKPOINT && !this.bigIcon) {
          this.animateTrash(true);
        } else if (ev.deltaX > 0 && ev.deltaX < ANIMATION_BREAKPOINT && this.bigIcon) {
          this.animateTrash(false);
        }

        // Animation icône de droite
        if (ev.deltaX < -ANIMATION_BREAKPOINT && !this.bigIcon) {
          this.animateArchive(true);
        } else if (ev.deltaX < 0 && ev.deltaX > -ANIMATION_BREAKPOINT && this.bigIcon) {
          this.animateArchive(false);
        }
      },
      onEnd: ev => {
        this.item.nativeElement.classList.remove('rounded');
        style.transition = '0.2s ease-out';
        if (ev.deltaX > ANIMATION_BREAKPOINT) {	// pour plus de confort (ANIMATION_BREAKPOINT + 50)
          style.transform = `translate3d(${windowWidth}px, 0, 0)`;
          this.animateDelete();

        } else if (ev.deltaX < -ANIMATION_BREAKPOINT) {	// pour plus de confort -(ANIMATION_BREAKPOINT + 50)
          style.transform = `translate3d(-${windowWidth}px, 0, 0)`;
            this.animateDelete();
          } else {
            style.transform = '';
        }
      }
    });


    moveGesture.enable();
  }

  setupAnimations() {
    this.deleteAnimation = this.animationCtrl.create('trash-animation')
    .addElement(this.item.nativeElement)
    .duration(300)
    .easing('ease-out')
    .fromTo('height', '89px', '0')
    .afterStyles({'display': 'none'});

    this.trashAnimation = this.animationCtrl.create('trash-animation')
    .addElement(this.trashIcon.nativeElement)
    .duration(300)
    .easing('ease-in')
    .fromTo('transform', 'scale(1)', 'scale(1.5)');

    this.archiveAnimation = this.animationCtrl.create('archive-animation')
    .addElement(this.archiveIcon.nativeElement)
    .duration(300)
    .easing('ease-in')
    .fromTo('transform', 'scale(1)', 'scale(1.5)');
  }

  // animation icône
  animateTrash(zoomIn) {
    this.bigIcon = zoomIn;
    if (zoomIn) {
      this.trashAnimation.direction('alternate').play();
    } else {
      this.trashAnimation.direction('reverse').play();
    }
    Haptics.impact({ style: ImpactStyle.Light }); // retour haptic pour mobile
  }
  animateArchive(zoomIn) {
    this.bigIcon = zoomIn;
    if (zoomIn) {
      this.archiveAnimation.direction('alternate').play();
    } else {
      this.archiveAnimation.direction('reverse').play();
    }
    Haptics.impact({ style: ImpactStyle.Light }); // retour haptic pour mobile
  }
  animateDelete(): void {
    this.deleteAnimation.play();
    // Envoyer l'évènement une fois l'animation terminée pour éviter la suppression de l'item
    // avant que l'animation ne soit terminée
    this.deleteAnimation.onFinish(() => {
      this.delete.emit(true);
    });
  }

  openDetail(id) {
    this.router.navigate(['tabs', 'mail', id]);
  }
}

````

## iOS keyboard scroll
[Back to top](#ui-components)  

To prevent iOS keyboard scrolling screen when entering in input field, add the following configuration in the *capacitor.config.json*

*capacitor.config.json*

````typescript
"plugins": {
    "Keyboard": {
      "resize": "ionic"
    }
  },
````

In addition you can add **<ion-content scrollY="false">** 
	
## ion-range
[Back to top](#ui-components)  

Customize **ion-range**

*view.html*
````html
  <div class="range-div">
    <ion-range
    [value]="rangeDefault"
    [(ngModel)]="rangeDefault"
    class="custom-range"
    [min]="rangeMin"
    [max]="rangeMax"
    snaps
    step="10"></ion-range>
    <div class="range-legend">
      <ion-label>{{ rangeMin }}</ion-label>
      <ion-label>{{ rangeDefault }}</ion-label>
      <ion-label>{{ rangeMax }}</ion-label>
    </div>
  </div>
````

*view.scss*
````typescript
.range-legend {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  padding-left: 10px;
  padding-right: 10px;
}
.custom-range {
  --bar-height: 6px;
  --bar-background: #56686D;
  --bar-background-active: #56686D;
  --knob-background: white;

  --knob-size: 18px;
  --knob-border-radius: 50%;
  --knob-box-shadow: 0px 0px 0px 6px #56686D;
}
````
[Back to top](#ui-components)  

## ion-title
[Back to top](#ui-components)  

To achieve centering *ion-title* on Android with left and right buttons, use **mode="ios"** option. 
	
````html
<ion-title mode="ios">My title</title>
````
	
## ion-checkbox
[Back to top](#ui-components)  

Customize checkbox :

````css
ion-checkbox {
  --background-checked: darkgray;
  --border-color-checked: darkgray;
  --border-radius: 5px;
  height : 25px;
  width: 25px;
}
````

[Back to top](#ui-components)  
## ion-datetime

*inline modal version*
````html
<ion-item class="ion-no-padding">
	<ion-label>Départ</ion-label>
	<ion-input
	  value="{{ startHour | date: 'HH:mm' }}"
	  id="startDate"
	  class="ion-text-end"></ion-input>
	<ion-modal trigger="startDate" size="cover">
	  <ng-template>
	    <ion-content>
	      <div class="section-top">Heure de départ</div>
	      <ion-datetime
		presentation="time"
		[(ngModel)]="startHour"
		locale="fr-FR"
		showDefaultButtons="true"
	      ></ion-datetime>
	    </ion-content>
	  </ng-template>
	</ion-modal>
</ion-item>
````

*popover inline version*
````html
	<ion-item class="ion-no-padding">
        <ion-label>Arrivée</ion-label>
        <ion-input
          value="{{ endHour | date: 'HH:mm' }}"
          id="endDate"
          class="ion-text-end"></ion-input>
        <ion-popover trigger="endDate" size="cover">
          <ng-template>
              <ion-datetime
                presentation="time"
                [(ngModel)]="endHour"
                locale="fr-FR"
                showDefaultButtons="true"
              ></ion-datetime>
          </ng-template>
        </ion-popover>
      </ion-item>
````
	
### Using standard html input to manage time value
[Back to top](#ui-components)  
	
*component.page.html*
	
````html
<ion-item [disabled]="!currentDatetime" id="fieldId">
  <ion-label>{{ title }}</ion-label>
  <ion-input
  value="{{ currentDatetime | date: 'dd/MM/YYYY HH:mm' }}"
  class="ion-text-end"
  ></ion-input>
</ion-item>
<ion-modal trigger="fieldId">
  <ng-template>
    <ion-content>
      <ion-row>
        <div class="datepicker-content">
          <ion-datetime
            #dateTime
            presentation="date"
            size="cover"
            [value]="currentDatetime"
            (ionChange)="dateChanged(dateTime.value)"
            (ionCancel)="showPicker = false;">
          </ion-datetime>
          <ion-item class="time-item">
            <ion-label>Heure</ion-label>
            <input class="timeField" type="time" [value]="currentDatetime | date: 'HH:mm'" (change)="timeChange($event)">
          </ion-item>
          <ion-buttons class="button-toolbar-grid" slot="buttons">
            <ion-button class="btn-standard btn-clear" (click)="cancelStartDate()">Annuler</ion-button>
            <ion-button class="btn-standard btn-plain" (click)="selectStartDate()">Valider</ion-button>
          </ion-buttons>
        </div>
      </ion-row>
    </ion-content>
  </ng-template>
</ion-modal>
````

*component.page.scss*
	
````css
.time-item {
  --width: 100% !important;
  width: 100% !important;
}
.timeField {
  background-color: white;
  color: var(--ion-color-primary);
  border: none;
  text-align: right;
}
.timeField:focus {
  outline: none;
}
````

*component.page.ts*
	
````typescript
import { IonDatetime } from '@ionic/angular';
import { Component, EventEmitter, Input, OnInit, Output, ViewChild } from '@angular/core';
import * as dayjs from 'dayjs';

@Component({
  selector: 'app-datetime-field',
  templateUrl: './datetime-field.component.html',
  styleUrls: ['./datetime-field.component.scss'],
})
export class DatetimeFieldComponent implements OnInit {

  @Input() title;
  @Input() currentDatetime;
  @Output() selectHour = new EventEmitter();
  @ViewChild(IonDatetime) dateTime: IonDatetime;

  showPicker = false;
  selectedDate;
  time;

  constructor(private tool: ToolService) {}

  ngOnInit() {
    if (this.currentDatetime !== null && this.currentDatetime !== '') {
      this.selectedDate = dayjs(this.currentDatetime);
      this.currentDatetime = dayjs(this.selectedDate).format('YYYY-MM-DDTHH:mm');
      this.time = dayjs(this.currentDatetime).format('HH:mm');
    }
  }

  dateChanged(value) {
    this.selectedDate = dayjs(value);
    if (this.time !== undefined) {
      this.currentDatetime = this.selectedDate.format('YYYY-MM-DD') + 'T' + this.time;
    } else {
      this.currentDatetime = this.selectedDate.format('YYYY-MM-DDTHH:mm');
    }

    this.showPicker = false;
  }

  timeChange(ev) {
    const { value } = ev.target;
    this.time = value;
    this.selectedDate = dayjs(this.currentDatetime);
    let d: dayjs.Dayjs = this.selectedDate;
    d = d.set('h', value.split(':')[0]);
    d = d.set('m', value.split(':')[1]);
    this.currentDatetime = d.format('YYYY-MM-DDTHH:mm');
  }

  selectStartDate() {
    this.selectHour.emit(this.currentDatetime);
    this.dateTime.confirm(true);
  }
  cancelStartDate() {
    this.dateTime.reset();
    this.dateTime.confirm(true);
  }
}
````
	
[Back to top](#ui-components)  
	

## ion-infinite-scroll

source : https://ionicacademy.com/ionic-6-app-api-calls/      

*environment.ts*

````typescript
export const environment = {
  production: false,
  apiKey: '', // <-- Enter your own key here!'
  baseUrl: 'https://api.themoviedb.org/3',
  images: 'http://image.tmdb.org/t/p',
};
````

*movie.service.ts*

````typescript
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { environment } from 'src/environments/environment';
 
export interface ApiResult {
  page: number;
  results: any[];
  total_pages: number;
  total_results: number;
}
 
@Injectable({
  providedIn: 'root',
})
export class MovieService {
  constructor(private http: HttpClient) {}
 
  getTopRatedMovies(page = 1): Observable<ApiResult> {
    return this.http.get<ApiResult>(
      `${environment.baseUrl}/movie/popular?page=${page}&api_key=${environment.apiKey}`
    );
  }
 
  getMovieDetails(id: string): Observable<any> {
    return this.http.get<ApiResult>(
      `${environment.baseUrl}/movie/${id}?api_key=${environment.apiKey}`
    );
  }
}
````

*movies.component.ts*

````typescript
import { Component, OnInit } from '@angular/core';
import { InfiniteScrollCustomEvent, LoadingController } from '@ionic/angular';
import { MovieService } from 'src/app/services/movie.service';
import { environment } from 'src/environments/environment';
 
@Component({
  selector: 'app-movies',
  templateUrl: './movies.page.html',
  styleUrls: ['./movies.page.scss'],
})
export class MoviesPage implements OnInit {
  movies = [];
  currentPage = 1;
  imageBaseUrl = environment.images;
 
  constructor(
    private movieService: MovieService,
    private loadingCtrl: LoadingController
  ) {}
 
  ngOnInit() {
    this.loadMovies();
  }
 
  async loadMovies(event?: InfiniteScrollCustomEvent) {
    const loading = await this.loadingCtrl.create({
      message: 'Loading..',
      spinner: 'bubbles',
    });
    await loading.present();
 
    this.movieService.getTopRatedMovies(this.currentPage).subscribe(
      (res) => {
        loading.dismiss();
        this.movies.push(...res.results);	// concat the next movies
 
        event?.target.complete();	// close infinite scroll loading indicator
        if (event) {
          event.target.disabled = res.total_pages === this.currentPage;
        }
      },
      (err) => {
        console.log(err);
        loading.dismiss();
      }
    );
  }
 
  loadMore(event: InfiniteScrollCustomEvent) {
    this.currentPage++;
    this.loadMovies(event);
  }
}
````

*movies.component.html*

````typescript
<ion-header>
  <ion-toolbar color="primary">
    <ion-title>Trending Movies</ion-title>
  </ion-toolbar>
</ion-header>
 
<ion-content>
  <ion-list>
    <ion-item button *ngFor="let item of movies" [routerLink]="[item.id]">
      <ion-avatar slot="start">
        <img [src]="imageBaseUrl + '/w92' + item.poster_path" />
      </ion-avatar>
 
      <ion-label class="ion-text-wrap">
        <h3>{{ item.title }}</h3>
        <p>{{ item.release_date | date:'y' }}</p>
      </ion-label>
 
      <ion-badge slot="end"> {{ item.vote_average }} </ion-badge>
    </ion-item>
  </ion-list>
 
  <ion-infinite-scroll (ionInfinite)="loadMore($event)">
    <ion-infinite-scroll-content loadingSpinner="bubbles" loadingText="Loading more data..."> </ion-infinite-scroll-content>
  </ion-infinite-scroll>
</ion-content>
````
[Back to top](#ui-components)  
