[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Sass

* [Set transparent color to element](#set-transparent-color-to-element)     
* [Set transparent NavBar](#configure-navbar-color)    
* [Set background image to content](#set-background-image-to-content)    
* [Lock screen orientation](#lock-screen-orientation)   
* [Positioning](#positioning)    
* [Remove Android input green highlight](#remove-android-input-green-highlight)    
* [Styling Searchbar](#styling-searchbar)    
* [Overlay](#overlay)    
* [Remove ion-card shadow](#remove-ion-card-shadow)    
* [Remove header shadow](#remove-header-shadow)     

Here are some tips to perform your scss.

## Get color variables from *variable.scss*

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

Add new transparent color in your *variable.scss* and use it as a standard color
```css
$colors: (
  primary:    #ed2f2f,
  secondary:  #be2625,
  hide: transparent,
);
```

## Configure NavBar color

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

### Center a div

Center (horizontally and vertically) a div containing a form :

*View file*

```html
<ion-content padding class="window">
    <form [formGroup]="loginForm" (submit)="loginUser()" novalidate>
      <div class="cardForm">
        <ion-item>
          <ion-label stacked>Email</ion-label>
          <ion-input #email formControlName="email" type="email" placeholder="Your email address"
            [class.invalid]="!loginForm.controls.email.valid"></ion-input>
        </ion-item>
        <ion-item class="error-message" *ngIf="!loginForm.controls.email.valid">
          <font class="info">Please enter a valid email.</font>
        </ion-item>
      
        <ion-item>
          <ion-label stacked>Password</ion-label>
          <ion-input #password formControlName="password" type="password" placeholder="Your password"
            [class.invalid]="!loginForm.controls.password.valid"></ion-input>
        </ion-item>
        <ion-item class="error-message" *ngIf="!loginForm.controls.password.valid">
          <font class="info">Your password needs more than 6 characters.</font>
        </ion-item>
      </div>
  
      <button ion-button block class="submitButton" type="submit">
        Login
      </button>
      <button ion-button block class="submitButtonOutline" (click)="createAccount()">
        Create a new account
      </button>
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

    <button [style.float]="'right'" clear text-center ion-button (click)="onDismiss()">
      {{ cancelButtonCaption }}
    </button>
    <button [style.float]="'right'" clear text-center ion-button (click)="onDismiss()">
      {{ okButtonCaption }}
    </button>
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

```html
<div text-center>
	<button ion-button outline type="submit" text-center [disabled]="!f.valid">VALIDER</button>
</div>
```

### ion-label vertical alignment
[Back to top](#sass)  

To set vertical alignment for ```<ion-label>``` inside ```<ion-item>```, you need to set ```display: flex;``` on the parent container (here the ion-item) and set ```margin:auto;``` on the ```<ion-label>```

```html
  <ion-item no-lines class="alertItem">
    <ion-label class="alertLabel" color="danger">Bluetooth adapter is disabled</ion-label>
    <button class="alertButton" ion-button clear default item-end color="white" (click)="onEnableBluetooth()">ENABLE</button>
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
        background-color: map-get($colors , danger)
    }
```

### ion-icon vertical alignment
[Back to top](#sass)  

```css
    .ionIcon{
        vertical-align: middle;
	display: inline-block;
    }
```

### ion-col vertical alignment

*view.html*
```
<ion-grid>
      <ion-row>
        <ion-col [style.backgroundColor]="'#ffcc00'">
          <ion-item class="formItem" no-lines>
            <ion-input placeholder="Suggestion" type="text" name="suggestion" ngModel></ion-input>
          </ion-item>
        </ion-col>
        <ion-col [style.backgroundColor]="'#aacc00'">
          <button ion-button clear class="submitButton" color="dark_red" [disabled]="!f.valid">AJOUTER</button>
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


### CSS vertical alignment
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

### Vertical align text in div

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
	<button ion-button block>MY BUTTON</button>
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
<button ion-item>
	<ion-label>my very long text labe</ion-label>
</button>
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

## Overlay
[Back to top](#sass)     

Create an overlay

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

## Remove ion-card shadow
[Back to top](#sass)     

```
<ion-header no-border>
	<ion-navbar>...</ion-navbar>
</ion-header>
```
