[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Sass

* [Set transparent color to element](#set-transparent-color-to-element)     
* [Set transparent NavBar](#set-transparent-navbar)    
* [Set background image to content](#set-background-image-to-content)    
* [Lock screen orientation](#lock-screen-orientation)   
* [Positioning](#positioning)    
* [Remove Android input green highlight](#remove-android-input-green-highlight)    
* [Styling Searchbar](#styling-searchbar)    
* [overlay](#overlay)    

Here are some tips to perform your scss.

## Get color variables from *variable.scss*

```css
myClass {
    background-color: map-get($colors, primary);
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

## Set transparent NavBar

Put the following code in your *variable.scss* : ```$toolbar-background: transparent;```

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
