[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Stylesheet

## Very userful tips

> https://blog.dorianguilmain.com/astuces/

## Sections

* [Component variables](#component-variables)    
* [Using variables](#using-variables)    
* [Add color](#add-color)    
* [Get color variables from variable.scss](#get-color-variables-from-variables.scss)    
* [Configure NavBar color](#configure-navbar-color)    
* [Set transparent NavBar](#configure-navbar-color)    
* [Set background image to content](#set-background-image-to-content)    
* [Lock screen orientation](#lock-screen-orientation)   
* [Positioning](#positioning)    
* [Remove Android input green highlight](#remove-android-input-green-highlight)    
* [Styling Searchbar](#styling-searchbar)    
* [Disable scrolling on ion-content](#disable-scrolling-on-ion-content)
* [Label with full width inside ion-item](#label-with-full-width-inside-ion-item)    
* [Change ion-item height](#change-ion-item-height)    
* [Split screen in two views](#split-screen-in-2-views)    
* [Modale with overlay](#modale-with-overlay)    
* [Remove ion-card shadow](#remove-ion-card-shadow) 
* [Dynamic class](#dynamic-class)     
* [Spacer](#spacer)    
* [Increase ion-icon size](#increase-icon-size)    
* [Remove header border](#remove-header-border)    
* [Create CSS stepper](#create-css-stepper)    
* [Transparent scroll](#transparent-scroll)   
* [Create fixed menu with scrollable content](#create-fixed-menu-with-scrollable-content)    
* [Responsive Flex grid with angular flex-layout](#flex-grid)    
* [Flex box](#flex-box)    
* [Make modale flexible](#make-modale-flexible)    
* [2 columns layout](#2-columns-layout)      
* [Images](#images)      
* [&::before and &::after](#before-and-after)       
* [:nth-of-type](#nth-of-type)      
* [Popover](#popover)      
* [Accessing shadow parts](#accessing-shadow-parts)      
* [Text overflow ellipsis](#text-overflow-ellipsis)      
* [ion-checkbox](#ion-checkbox)      

Here are some tips to perform your scss.

## Component variables

Since ionic 4, some component variables are available for styling it. You can find them in CSS Custom Properties of each component, for example these are the Custom css properties for ion-item (https://ionicframework.com/docs/api/item#css-custom-properties) :

```
--background	Background of the item
--background-activated	Background of the activated item
--border-color	Color of the item border
--border-radius	Radius of the item border
--border-style	Style of the item border
--border-width	Width of the item border
--box-shadow	Box shadow of the item
--color	Color of the item
--detail-icon-color	Color of the item detail icon
--detail-icon-font-size	Font size of the item detail icon
--detail-icon-opacity	Opacity of the item detail icon
--highlight-color-focused	The color of the highlight on the item when focused
--highlight-color-invalid	The color of the highlight on the item when invalid
--highlight-color-valid	The color of the highlight on the item when valid
--highlight-height	The height of the highlight on the item
--inner-border-width	Width of the item inner border
--inner-box-shadow	Box shadow of the item inner
--inner-padding-bottom	Bottom padding of the item inner
--inner-padding-end	End padding of the item inner
--inner-padding-start	Start padding of the item inner
--inner-padding-top	Top padding of the item inner
--min-height	Minimum height of the item
--padding-bottom	Bottom padding of the item
--padding-end	End padding of the item
--padding-start	Start padding of the item
--padding-top	Top padding of the item
--transition	Transition of the item
```

## Using variables
[Back to top](#stylesheet) 

using sass variable in component's style sheet :

First, define variable :

*variable.scss*

```
$my-color-red: red;
$my-custom-font-size: 14px;
$my-custom-font-family: "Helvetica", Arial;
...
```
Usage :

*my-component.scss*

```
@import "src/app/styles/variables.scss";

.my-custom-div {
	color: $my-color-red;
	font-size: $my-custom-font-size;
	...
}
```

## Add color
[Back to top](#stylesheet)  

Note : It's recommanded to use https://ionicframework.com/docs/theming/color-generator to generate your custom color

To add a new color, create a class that defines all of the variations using CSS variables for that color. The class should be written in the format .ion-color-{COLOR} where {COLOR} is the name of the color to add. For example, to add a color called favorite, the following class could be added:

```
.ion-color-favorite {
  --ion-color-base: #69bb7b;
  --ion-color-base-rgb: 105,187,123;
  --ion-color-contrast: #ffffff;
  --ion-color-contrast-rgb: 255,255,255;
  --ion-color-shade: #5ca56c;
  --ion-color-tint: #78c288;
}
```

## Get color variables from *variable.scss*
[Back to top](#stylesheet)  

**Ionic 4 syntax**

```
myClass {
    background-color: var(--ion-color-myCustomColor);
}
```

**Ionic 2/3 syntax**
```css
myClass {
    background-color: map-get($colors, primary);
}
```

Or other solution, put the code below in your css file

```
 ion-header {
        .toolbar {
          position: absolute;
      
          .toolbar-background {
            background: transparent;
          }
          .toolbar-title {
            color: black;
          }
        }
      }
```

## Configure NavBar color
[Back to top](#stylesheet)  

### ionic 4 translucent toolbar

Set the following css in your page.tss

```
ion-toolbar {
	--background: transparent;
	--ion-color-base: transparent !important;
}
```


### ionic 3 syntax

Put the following code in your *variable.scss* : ```$toolbar-background: transparent; // or color```


## Set background image to content
[Back to top](#stylesheet)  
```css
page-map {
    .main {
        background-image: url('../assets/img/my_background.jpg');
        -webkit-background-image: url('../assets/img/my_background.jpg');
        background-repeat: no-repeat;
        background-size:cover;
    }
}
```

## Lock screen orientation
[Back to top](#stylesheet)  

[Source intéressante](https://blog.dorianguilmain.com/centrer-du-contenu-avec-css/)       
[Documentation screen orientation](https://ionicframework.com/docs/native/screen-orientation/)    

**Warning** You can not test the screen orientation on ionic serve, this will throw an exception

## Positioning
[Back to top](#stylesheet)  

### Justify content center

*View*
```
<div class="container">
	<img src="/xxxx" />
	<img src="/xxxx" />
</div>
```

*Style*
```
.container {
	display: flex;
	justify-content: center;
	align-items: center;
}
```

### Center a div

Center (horizontally and vertically) a div containing a form :

*View file*

```html
<ion-content padding class="window">
    <form [formGroup]="loginForm" (submit)="loginUser()" novalidate>
      <div class="cardForm">
        <ion-item>
          <ion-label position="stacked">Email</ion-label>
          <ion-input #email formControlName="email" type="email" placeholder="Your email address"
            [class.invalid]="!loginForm.controls.email.valid"></ion-input>
        </ion-item>
        <ion-item class="error-message" *ngIf="!loginForm.controls.email.valid">
          <font class="info">Please enter a valid email.</font>
        </ion-item>
      
        <ion-item>
          <ion-label position="stacked">Password</ion-label>
          <ion-input #password formControlName="password" type="password" placeholder="Your password"
            [class.invalid]="!loginForm.controls.password.valid"></ion-input>
        </ion-item>
        <ion-item class="error-message" *ngIf="!loginForm.controls.password.valid">
          <font class="info">Your password needs more than 6 characters.</font>
        </ion-item>
      </div>
  
      <ion-button block class="submitButton" type="submit">
        Login
      </ion-button>
      <ion-button expand="block" class="submitButtonOutline" (click)="createAccount()">
        Create a new account
      </ion-button>
    </form>  
</ion-content>
```

*Style file*

```
.myDiv{
    width: 400px;
    position: absolute;
    top:50%;
    left:50%;
    padding:15px;
    -ms-transform: translateX(-50%) translateY(-50%);
    -webkit-transform: translate(-50%,-50%);
    transform: translate(-50%,-50%);
}
```

### Other way to center a div

*View file*

```html
<div class="overlay" padding>
  <div class="popup_modal">
    <font class="title">{{ title }}</font>
    <p class="content">{{ content }}</p>

    <ion-button [style.float]="'right'" fill="clear" text-center (click)="onDismiss()">
      {{ cancelButtonCaption }}
    </ion-button>
    <ion-button [style.float]="'right'" fill="clear" text-center (click)="onDismiss()">
      {{ okButtonCaption }}
    </ion-button>
  </div>
</div>
```

*Style file*

```
popup {
    .overlay{
        background-color: rgba(0,0,0,0.6);
        height: 100%;
        width: 100%;
    }
    .popup_modal{
        background-color: #ffffff;
        border-radius: 5px;
        padding: 5px;
        position: absolute;
        left: 20px;
        right: 20px;
        margin: auto;
        margin-top: 50vh; /* poussé de la moitié de hauteur de viewport */
        transform: translateY(-50%); /* tiré de la moitié de sa propre hauteur */
    }
    .title{
        font-size: 20px;
        font-weight: bold;
        margin-top: 20px !important;
        padding: 5px;
    }
    .content{
        padding: 5px;
    }
}
```

*View file*

```javascript
import { NavController, NavParams } from 'ionic-angular';
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'popup',
  templateUrl: 'popup.html'
})
export class PopupComponent implements OnInit{

  title: string = "Title";
  content: string = "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
  okButtonCaption: string = "OK";
  cancelButtonCaption: string = "CANCEL";

  constructor(public navCtrl: NavController, public navParam: NavParams) {
  }

  ngOnInit(){
    // Récupération des paramètres
    
    if(this.navParam.get("title") && this.navParam.get("title") !== ""){
      this.title = this.navParam.get("title")
    }
    
    if(this.navParam.get("content") && this.navParam.get("content") !== ""){
      this.content = this.navParam.get("content")
    }

    if(this.navParam.get("okButtonCaption") && this.navParam.get("okButtonCaption") !== ""){
      this.okButtonCaption = this.navParam.get("okButtonCaption")
    }

    if(this.navParam.get("cancelButonCaption") && this.navParam.get("cancelButonCaption") !== ""){
      this.cancelButtonCaption = this.navParam.get("cancelButonCaption")
    }
  }

  onDismiss(){
    this.navCtrl.pop();
  }

}

```

### Bottom positioning
```css
.cform {
    position: absolute;
    bottom: 20%;
    left: 50px;
    right: 50px;
}
```

### Center image
[Back to top](#stylesheet)  

```css
.image {
    position: absolute;
    left: 10px;
    right: 10px;
    display: block;
    margin-left: auto;
    margin-right: auto;
}
```

### Center overlayed images
[Back to top](#stylesheet)  

*View file*

```html
<div class="mainContent">
	<img class="image1" src="..."/>
	<img class="image2" src="..."/>
</div>
```

*Style sheet*

```css
.image1 {
	width: 100%;
	height: 200px;
	z-index:0;
	position: absolute;
}
.image2 {
	position: absolute;
	left: 10px;
	right: 10px;
	top: 10px;
	bottom: 10px;
	margin-left: auto;
	margin-right: auto;
	margin-top: auto;
	margin-bottom: auto;
	display: block;
	z-index: 1;
}
.mainContent {
        width: 100%;
        height: 200px;
        left: 0px;
        right: 0px;
        top: 0px;
        position: relative;
    }
```

### Center image
[Back to top](#stylesheet)  

```css
img {
	position: absolute;
	margin: auto;
	top: 0;
	bottom: 0;
	left: 0;
	right: 0;
	padding: 0px;
}
```

### Center component
[Back to top](#stylesheet)  

vertical center in row

```css
 <ion-row center>
        <ion-col text-center>
          <ion-label>{{ c.date }}</ion-label>
        </ion-col>
        <ion-col text-center>
          <ion-label>{{ c.cost }}€</ion-label>
        </ion-col>
        <ion-col text-center>
          <ion-label>{{ c.trip }}km</ion-label>
        </ion-col>
</ion-row>
```

### Center button text
[Back to top](#stylesheet)  

```css
.myButtonStyle{
	text-align: center;
}
```

### Center text inside ion-input
[Back to top](#stylesheet)  

In the view file
```xml
<ion-input text-center></ion-input>
```

### Vertical alignment
[Back to top](#stylesheet)  

#### Using text-center

```html
<div text-center>
	<ion-button fill="outline" type="submit" text-center [disabled]="!f.valid">VALIDER</ion-button>
</div>
```

#### Using display flex

````
<div>
  <label for='name'>Name:</label>
  <input type='text' id='name' />
</div>
````

````
div {
  height: 50px;
  background: grey;
  display: flex;
  align-items: center
}
````

#### ion-label vertical alignment
[Back to top](#stylesheet)  

To set vertical alignment for ```<ion-label>``` inside ```<ion-item>```, you need to set ```display: flex;``` on the parent container (here the ion-item) and set ```margin:auto;``` on the ```<ion-label>```

```html
  <ion-item lines="none" class="alertItem">
    <ion-label class="alertLabel" color="danger">Bluetooth adapter is disabled</ion-label>
    <ion-button class="alertButton" fill="clear" default slot="end" color="white" (click)="onEnableBluetooth()">ENABLE</ion-button>
  </ion-item>
```

```css
    .alertLabel{
        text-align: left;
        color: #ffffff;
        margin: auto;
    }
    .alertItem{
        display: flex;
        background-color: var(--ion-color-myCustomColor)
    }
```

#### ion-icon vertical alignment
[Back to top](#stylesheet)  

```css
    ion-icon{
        vertical-align: middle;
	display: inline-block;
    }
```

#### ion-col vertical alignment

*view.html*
```
<ion-grid>
<ion-row>
<ion-col [style.backgroundColor]="'#ffcc00'">
  <ion-item class="formItem" lines="none">
    <ion-input placeholder="Suggestion" type="text" name="suggestion" ngModel></ion-input>
  </ion-item>
</ion-col>
<ion-col [style.backgroundColor]="'#aacc00'">
  <ion-button fill="clear" class="submitButton" color="dark_red" [disabled]="!f.valid">AJOUTER</ion-button>
</ion-col>
</ion-row>
</ion-grid>
```

*style.css*
```
   ion-row{
        display: flex;
        align-items: center;
        justify-content: center;
    }
    ion-col{
        height: 100%;
    }
```


#### CSS vertical alignment
[Back to top](#stylesheet)    

```html
<div class="box">
  <div></div>
</div>
```

```
.box {
  display: flex;
  align-items: center;
  justify-content: center;
}

.box div {
  width: 100px;
  height: 100px;
}
```

### Add button at the bottom of listview
[Back to top](#stylesheet)  

```html
<ion-content>
...
</ion-content>

<ion-footer no-shadow>
	<ion-button expand="block">MY BUTTON</ion-button>
</ion-footer>
```

## Remove Android input green highlight
[Back to top](#stylesheet)  

add the following to your *variable.scss*

```javascript
$text-input-md-show-focus-highlight: false !default;
$text-input-md-show-valid-highlight: $text-input-md-show-focus-highlight !default;
$text-input-md-show-invalid-highlight: $text-input-md-show-focus-highlight !default;
```

## Styling Searchbar
[Back to top](#stylesheet)  

To change ios searchbar background color, add the following code to your *variable.scss* file :

```
$searchbar-ios-background-color: rgba(247,247,247,1);
```

### Remove border, add specific box shadow, resize

*page.html*
````
<ion-searchbar 
          mode="md"
          debounce="500" 
          animated
          class="searchbar"
          [(ngModel)]="searchTerm"
          (ionChange)="searchbarListener($event)"
          (ionClear)="onClearSearch()"
          >          
        </ion-searchbar>
````

*page.tss*
```
ion-searchbar {
	border-radius: 10px;
	box-shadow: 0 2px 8px 0 rgba(0,0,0,0.2);
	margin-left: 20px;
	margin-right: 20px;
	width: auto;
}
.searchbar-input {
  border: 0!important;
  box-shadow: none !important;
}
.searchbar-ios .searchbar-input {
    background-color: white !important;
}
```

## Disable scrolling on ion-content
[Back to top](#stylesheet)  

### Old method

*View file*

```javascript
<ion content class="no-scroll">
...
</ion-content>
```

*Style file*

```css
.no-scroll {
        .scroll-content {
            overflow: hidden !important;
        }
}
```

### New solution
[Back to top](#stylesheet)  

```
$ ionic cordova plugin add ionic-plugin-keyboard
$ npm install --save @ionic-native/keyboard
```

Next, add provider for plugin like this

*app.module.ts*

```
@NgModule({
declarations: [...],
imports: [
IonicModule.forRoot(MyApp, { scrollAssist: false, autoFocusAssist: false } )
]
providers: [
    ...
    Keyboard
    ...
  ]
})
```

## Label with full width inside ion-item
[Back to top](#stylesheet)  

Tips to avoid ion-label truncate

```html
<ion-item>
	<ion-label>my very long text label</ion-label>
</ion-item>
```

```css
ion-label{
	width: 100%;
	display: table:
}
```

## Change ion-item height
[Back to top](#stylesheet)  

To reduce the size of *ion-item* element you need to override *min-height* property in your css. Then apply your size in pixels.

```css
.my_ion_item_class{
	min-height: 3rem; // Or less
        height: 20px; 
}
```

## Split screen in 2 views
[Back to top](#stylesheet)  

*View File*
```css
<ion-header>
  <ion-navbar>
    <ion-title>test</ion-title>
  </ion-navbar>
</ion-header>

<ion-content class="no-scroll">
  <div class="top"></div>
  <div class="bottom">
      <p class="val">{{ very_long_text }}</p>
  </div>
</ion-content>
```

*Style file*
```css
    .top{
        height: 50%;
        background-color: #e3e3e3;
    }
    .bottom{
        height: 50%;
        background-color: #e3b5e5;
    }
    .val{
        overflow: scroll;
        height: 100%;
    }
    .no-scroll{
        overflow: hidden;
    }
```

## Modale with overlay
[Back to top](#stylesheet)     

Create modale with transparent black overlay

### Solution 1

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

### Solution 2

*View file*

```html
<ion-content>
   <div class="overlay" *ngIf="doExport === true">
    <div class="exportModal">
      <ion-list *ngFor="let location of locations">
        <ion-item>{{ location?.label }}</ion-item>
      </ion-list>
    </div>
  </div>
</ion-content>
```

*Style file*

```css
 .overlay{
        position: fixed; /* Sit on top of the page content */
        width: 100%; /* Full width (cover the whole page) */
        height: 100%; /* Full height (cover the whole page) */
        top: 0; 
        left: 0;
        right: 0;
        bottom: 0;
        background-color: rgba(0,0,0,0.5); /* Black background with opacity */
        z-index: 2; /* Specify a stack order in case you're using a different order for other elements */
        cursor: pointer; /* Add a pointer on hover */
    }
    .exportModal{
        background-color: white;
        height: 50%;
        margin-top:100%;
        opacity: 1;
        z-index: 0;
    }
```

## Remove ion-card shadow
[Back to top](#stylesheet)     

```
ion-card, .card-ios, .card-md {
    box-shadow: none !important;
}
```

## Dynamic class
[Back to top](#stylesheet)

```
  <div class="divCard" [ngClass]="item.show == true ? 'divCardExtand' : 'divCard'"></div>
  
  <div [ngClass]="{class1: text === 'titi', class2: text === 'toto', class3: text === 'tata'}">
  my content here
</div>
```

## Spacer
[Back to top](#stylesheet)  

*View file*
```
<mat-toolbar color="primary">
    <span><a routerLink="/">My messages</a></span>
    <span class="spacer"></span>
    <span><a routerLink="/create">New messages</a></span>
</mat-toolbar>
```

*Style file*
```
a {
    text-decoration: none;
    color: white;
}
.spacer {
    flex: 1 1 auto;
}
```

Spacer will set the first span on the left side and the third span on the right side of the screen

# Increase icon size
[Back to top](#stylesheet)  

*ion-icon* using font, so you just need to increase *font-size* to get bigger icons

# Remove header border
[Back to top](#stylesheet)  

````
<ion-header class="ion-no-border">
	<ion-toolbar></ion-toolbar>
</ion-header>
````

In case of ````<ion-header class="ion-no-border">```` is not working, here is a workaround

````
ion-header {
  &.header-md:after {
    background: none !important;
  }
}
// IOS --> 
ion-toolbar {
    border: none !important;
}
````


# Create css stepper
[Back to top](#stylesheet)  

[Documentation W3C](https://www.w3schools.com/w3css/w3css_progressbar.asp)
[W3C stylesheet](https://www.w3schools.com/w3css/4/w3.css)

*html file*
````
<div class="w3-light-grey w3-tiny">
  <div class="w3-green" style="height:8px;width:13%"></div>
</div>
````

*scss file*
````
.w3-container,.w3-panel {
    padding:0.01em 16px
}
.w3-panel {
    margin-top:16px;
    margin-bottom:16px
}
.w3-light-grey,.w3-hover-light-grey:hover,.w3-light-gray,.w3-hover-light-gray:hover {
    color:#000!important;
    background-color:#9EB8BE !important;
    border-radius:20px;
}
.w3-tiny {
    font-size:10px!important
}
.w3-small {
    font-size:12px!important;
}
.w3-green,.w3-hover-green:hover {
    color:#fff!important;
    border-radius:20px;
    background-color:#009DBE!important;
}
````

# Transparent scroll
[Back to top](#stylesheet)

To make browser scrollbar transparent use 

````
::-webkit-scrollbar
{
    width: 0px;
}
::-webkit-scrollbar-track-piece
{
    background-color: transparent;
    -webkit-border-radius: 6px;
}
````

# Create fixed menu with scrollable content
[Back to top](#stylesheet)

*View file*

````
<div class="main">
    <!-- ----------------- LEFT PANEL ---------------------->
    <div class="left-panel">
  	<ion-list>
		<ion-item *ngFor="let m of menu">{{ m.title }}</ion-item>
	</ion-list>
    </div>
    <div class="right-panel">
    	<ion-card *ngFor="let c of cards">
		<ion-card-header>
			<ion-card-title>{{ c.title }}</ion-card-title>
		</ion-card-header>
		<ion-card-content>
			<p>{{ c.content }}</p>
		</ion-card-content>
	</ion-card>
    </div>
</div>
````

*Style file*

````
.left-panel {
    position: fixed;
    overflow: auto;
    width: 21%;
    float: left;
    height: 100%;
    background-color: transparent !important;
    --background: transparent !important;
}
.right-panel {
    position: relative;
    margin-left: 21%;
    float: left;
    height: 100%;
    width: 79%;
    padding-left: 25px;
}
````


## Flex grid
[Back to top](#stylesheet)

### Angular flex-layout

https://zoaibkhan.com/blog/create-a-responsive-card-grid-in-angular-using-flex-layout-part-1/        
https://medium.com/angular-in-depth/angular-flex-layout-flexbox-and-grid-layout-for-angular-component-6e7c24457b63         

````
import { FlexLayoutModule } from '@angular/flex-layout';
````

Example of responsive grid with mat-card

*card-component*

````
<mat-toolbar color="primary">
    <span>Card view demo</span>
    <div fxHide.lt-md>
      <span class="column-label">Columns</span>
      <mat-slider [max]="6" [min]="3" [(ngModel)]="gridColumns" [thumbLabel]="true">
      </mat-slider>
    </div>
  </mat-toolbar>
<div class="content">
    <div fxLayout="row wrap" fxLayoutGap="16px grid">
        <div [fxFlex]="(100/gridColumns) + '%'" fxFlex.xs="100%" fxFlex.sm="33%" *ngFor="let num of [1,2,3,4,5,6,7]">
        
        <!-- Utiliser le redimenssionnement classique sans le slider -->
            <!-- <div fxFlex="25%" fxFlex.xs="100%" fxFlex.sm="33%" *ngFor="let num of [1,2,3,4,5,6,7]"> -->
            <mat-card class="mat-elevation-z4">
            <mat-card-header>
                <mat-card-title>Himalayan Peaks</mat-card-title>
            </mat-card-header>
            <img mat-card-image src="./../assets/images/2-chtulhu.jpg">
            <mat-card-content>
                <p>
                The Himalayas is a mountain range in Asia.
                </p>
            </mat-card-content>
            <mat-card-actions>
                <button mat-button>LIKE</button>
                <button mat-button>SHARE</button>
            </mat-card-actions>
            </mat-card>
        </div>
    </div>
  </div>

````

*card-component.scss*

````
.content {
    padding: 16px;
}

.content > mat-card {
    width: 200px;
}

mat-toolbar {
    justify-content: space-between;
}
.content > mat-card {
    margin-bottom: 16px;
}

.column-label {
    margin-right: 8px;
    font-size: 1rem;
}
````

### flex-box css example

https://www.w3schools.com/css/tryit.asp?filename=trycss3_flexbox_flex-wrap_wrap

According to the previous documentation, here is an example of a responsive grid with 3 columns containing 3 charts. When the screen size is over 1600px, each div has 33% width, then when screen size is between 1600px and 1344px, each div has 47% width. Finally when screen size is under 1024px, each div has 100% width.

*View file*

````
<div class="flex-container">
  <div class="flex-div">
    <div class="tile chart-div2">
      <div class="chart-title">
        <ion-label>Line chart</ion-label>
      </div>
      <canvas #lineCanvas></canvas>
    </div>
  </div>
  <div class="flex-div">
    <div class="tile chart-div2">
      <div class="chart-title">
        <ion-label>Bar Chart 1</ion-label>
      </div>
      <canvas #barCanvas1></canvas>
    </div>
  </div>
  <div class="flex-div">
    <div class="tile chart-div2">
      <div class="chart-title">
        <ion-label>Bar Chart 2</ion-label>
      </div>
      <canvas #barCanvas2></canvas>
    </div>
  </div>  
</div>
````

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
[Back to top](#stylesheet)


## Flex box
[Back to top](#stylesheet)

https://css-tricks.com/snippets/css/a-guide-to-flexbox/
https://makina-corpus.com/blog/metier/2017/introduction-a-flexbox
https://www.youtube.com/watch?v=KMvB08yNqlI&feature=youtu.be

## Make modale flexible
[Back to top](#stylesheet)

````
ion-modal.auto-height {
    --height: auto;
    .modal-wrapper{
      width: 90vw;
      .ion-page {
        position: relative;
        contain: content;
        max-height: 90vh;
        .modal-content {
          overflow: auto;
        }
      }
    }
  }
````

## 2 columns layout
[Back to top](#stylesheet)

CSS 2 columns layout with 100% height 

*View file*
````
<!DOCTYPE html>
<html>
  <head></head>
  <body>
    <div class="page-wrapper">
      <div class="left-column"></div>
      <div class="right-column">
        This is the content.<br>This is the content.<br>This is the content.<br>
        This is the content.<br>
        This is the content.<br>
        This is the content.<br>
      </div>
    </div>
  </body>
</html>
````
*style file*
````
html, body {
/*Important*/
  height: 100%;
  width: 100%;
  padding: 0;
  margin: 0;
}

.page-wrapper {
  height: 100%;
  position: relative;
}

.left-column {
  position:fixed;
  top:0;
  left:0;
  width:235px;
  height:100%;
  background:#090909;
}

.right-column {
  margin-left:235px;
  background:yellow;
  min-height:100%;
}
````

## Images
[Back to top](#stylesheet)

````
img {
	width: 400px;
	height: 300px;
	/** keep aspect ratio **/
	object-fit: cover;
	/** adjust alignment **/
	object-position: bottom center;
}
````

## before and after
[Back to top](#stylesheet)

https://blog.dorianguilmain.com/bien-comprendre-after-before/

## nth-of-type

````
// Apply absolute position to all childs reflexion-shape
.reflexion-shape:nth-of-type(n) {
	position: absolute;
}

// target a specifi child
.reflexion-shape:nth-of-type(1) {
	background-color: #fca054;
	width: 35%;
}
.reflexion-shape:nth-of-type(2) {
	background-color: blue;
	width: 35%;
	height: 80px;
}
````

## Popover
[Back to top](#stylesheet)

*global.scss*
````css
.custom-popover {
  --ion-backdrop-opacity: 0.6;
  .popover-arrow {
      display: none;
  }

  .popover-content {
      left: 10px !important;
      width: calc(100% - 20px);
  }
}
````

## Accessing shadow parts
[Back to top](#stylesheet)

*global.scss* 

````css
ion-modal::part(content) {
	backdrop-filter: blur(12px);
}

// or

.my-custom-class {
    &::part(content) {
    	font-weight: 600;
    }
    
    &::part(arrow) {
    	background: red;
    }
    
    &::part(backdrop) {
    	backdrop-filter: blur(12px);
    }
}
````

## Text overflow ellipsis

Text ellipsis on 2 lines field 

````css
  height: 60px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: initial;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
````
[Back to top](#stylesheet)

## ion-checkbox

Customize checkbox icon with svg

````css
ion-checkbox {
  --checkmark-color: red;
  --border-width: 0px;
  --background-checked: transparent;
}

ion-checkbox::part(mark) {
  d: path('M 12 21 C 11.699219 21 11.40625 20.910156 11.15625 20.738281 C 7.472656 18.238281 5.878906 16.523438 4.996094 15.453125 C 3.121094 13.167969 2.226562 10.820312 2.25 8.28125 C 2.28125 5.367188 4.617188 3 7.457031 3 C 9.523438 3 10.953125 4.164062 11.789062 5.132812 C 11.839844 5.195312 11.917969 5.230469 12 5.230469 C 12.082031 5.230469 12.160156 5.195312 12.210938 5.132812 C 13.046875 4.164062 14.476562 3 16.542969 3 C 19.382812 3 21.71875 5.367188 21.75 8.28125 C 21.773438 10.820312 20.875 13.167969 19.003906 15.453125 C 18.121094 16.523438 16.527344 18.238281 12.84375 20.738281 C 12.59375 20.910156 12.300781 21 12 21 Z M 12 21');
  opacity: 1;
}

ion-checkbox.checkbox-checked::part(mark) {
  d: path('M 12 21 C 11.699219 21 11.40625 20.910156 11.15625 20.738281 C 7.472656 18.238281 5.878906 16.523438 4.996094 15.453125 C 3.121094 13.167969 2.226562 10.820312 2.25 8.28125 C 2.28125 5.367188 4.617188 3 7.457031 3 C 9.523438 3 10.953125 4.164062 11.789062 5.132812 C 11.839844 5.195312 11.917969 5.230469 12 5.230469 C 12.082031 5.230469 12.160156 5.195312 12.210938 5.132812 C 13.046875 4.164062 14.476562 3 16.542969 3 C 19.382812 3 21.71875 5.367188 21.75 8.28125 C 21.773438 10.820312 20.875 13.167969 19.003906 15.453125 C 18.121094 16.523438 16.527344 18.238281 12.84375 20.738281 C 12.59375 20.910156 12.300781 21 12 21 Z M 12 21');
  fill: red;
}
````

[Back to top](#stylesheet)
