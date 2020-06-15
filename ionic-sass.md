[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Sass

* [Component variables](#component-variables)    
* [Using variables](#using-variables)    
* [Add color](#add-color)    
* [Get color variables from variable.scss](#get-color-variables-from-variables.scss)    
* [Set transparent color to element](#set-transparent-color-to-element)     
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
* [Remove header shadow](#remove-header-shadow)     
* [Dynamic class](#dynamic-class)     
* [Spacer](#spacer)    
* [Increase ion-icon size](#increase-icon-size)    
* [Remove header border](#remove-header-border)    
* [Create CSS stepper](#create-css-stepper)    
* [Transparent scroll](#transparent-scroll)   
* [Create fixed menu with scrollable content](#create-fixed-menu-with-scrollable-content)    
* [Flex grid](#flex-grid)    
* [Flex box](#flex-box)    

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
[Back to top](#sass) 

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
[Back to top](#sass)  

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
[Back to top](#sass)  

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

## Set transparent color to element
[Back to top](#sass)  

Add new transparent color in your *variable.scss* and use it as a standard color
```css
$colors: (
  primary:    #ed2f2f,
  secondary:  #be2625,
  hide: transparent,
);
```

## Configure NavBar color
[Back to top](#sass)  

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
[Back to top](#sass)  

[Documentation screen orientation](https://ionicframework.com/docs/native/screen-orientation/)    

**Warning** You can not test the screen orientation on ionic serve, this will throw an exception

## Positioning
[Back to top](#sass)  

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
[Back to top](#sass)  

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
[Back to top](#sass)  

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
[Back to top](#sass)  

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
[Back to top](#sass)  

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
[Back to top](#sass)  

```css
.myButtonStyle{
	text-align: center;
}
```

### Center text inside ion-input
[Back to top](#sass)  

In the view file
```xml
<ion-input text-center></ion-input>
```

### Vertical alignment
[Back to top](#sass)  

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
[Back to top](#sass)  

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
[Back to top](#sass)  

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
[Back to top](#sass)    

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

#### Using display table-cell

```
<div class="container">
	<div class="text">My text</div>
</div>
```

```
.container{
	display: table;
}
.text{
	display: table-cell;
    	vertical-align: middle;
}
```

### Add button at the bottom of listview
[Back to top](#sass)  

```html
<ion-content>
...
</ion-content>

<ion-footer no-shadow>
	<ion-button expand="block">MY BUTTON</ion-button>
</ion-footer>
```

## Remove Android input green highlight
[Back to top](#sass)  

add the following to your *variable.scss*

```javascript
$text-input-md-show-focus-highlight: false !default;
$text-input-md-show-valid-highlight: $text-input-md-show-focus-highlight !default;
$text-input-md-show-invalid-highlight: $text-input-md-show-focus-highlight !default;
```

## Styling Searchbar
[Back to top](#sass)  

To change ios searchbar background color, add the following code to your *variable.scss* file :

```
$searchbar-ios-background-color: rgba(247,247,247,1);
```

### Remove border, add specific box shadow, resize

*page.tss*
```
ion-searchbar {
	border-radius: 10px;
	box-shadow: 0 2px 8px 0 rgba(0,0,0,0.2);
	margin-left: 20px;
	margin-right: 20px;
	width: auto;
}
```

## Disable scrolling on ion-content
[Back to top](#sass)  

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
[Back to top](#sass)  

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
[Back to top](#sass)  

Tips to avoid ion-label truncate

```html
<ion-item>
	<ion-label>my very long text labe</ion-label>
</ion-item>
```

```css
ion-label{
	width: 100%;
	display: table:
}
```

## Change ion-item height
[Back to top](#sass)  

To reduce the size of *ion-item* element you need to override *min-height* property in your css. Then apply your size in pixels.

```css
.my_ion_item_class{
	min-height: 3rem; // Or less
        height: 20px; 
}
```

## Split screen in 2 views
[Back to top](#sass)  

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
[Back to top](#sass)     

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
[Back to top](#sass)     

```
ion-card, .card-ios, .card-md {
    box-shadow: none !important;
}
```

## Remove header shadow
[Back to top](#sass)     

```
<ion-header no-border>
	<ion-navbar>...</ion-navbar>
</ion-header>
```

## Dynamic class
[Back to top](#sass)

```
  <div class="divCard" [ngClass]="item.show == true ? 'divCardExtand' : 'divCard'"></div>
```

## Spacer
[Back to top](#sass)  

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
[Back to top](#sass)  

*ion-icon* using font, so you just need to increase *font-size* to get bigger icons

# Remove header border
[Back to top](#sass)  

In case of ```<ion-header no-border>``` is not working, here is a workaround

```
ion-header {
  &.header-md:after {
    background: none;
  }
}
```


# Create css stepper
[Back to top](#sass)  

[Documentation W3C](https://www.w3schools.com/w3css/w3css_progressbar.asp)
[W3C stylesheet](https://www.w3schools.com/w3css/4/w3.css)

*html file*
```
<div class="w3-light-grey w3-tiny">
  <div class="w3-green" style="height:8px;width:13%"></div>
</div>
```

*scss file*
```
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
```

# Transparent scroll
[Back to top](#sass)

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
[Back to top](#sass)

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
[Back to top](#sass)

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
[Back to top](#sass)


## Flex box
[Back to top](#sass)

https://www.youtube.com/watch?v=KMvB08yNqlI&feature=youtu.be
