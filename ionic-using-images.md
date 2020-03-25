[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Using image

* [Background image](#background-image)    
* [SVG](#svg)    
* [Using cascading SVG](#using-cascading-svg)    
* [Add blur](#add-blur-on-image)    
* [Upload image](#upload-image)    

To use image in your app, you can store them under ```src/assets/imgs/*.png```

*View file*

```xml
<img class="thb" src="{{item.image}}" slot="end"/>
```

*Controller file*

```javascript
let item = {name:"my item", image:"./assets/imgs/my_image.png"}
```

*Style file*

```css
.thb{
  width: 50px;
}
```

**Warning** when running serve, path must be *../assets/imgs/xxxx* but when running device, path must be *./assets/imgs/xxxxx*

## Background image

*View file*
```
<div class="div-img"></div>
```

*Style file*
```
background-image: url('/assets/imgs/picto_sr.png');
background-repeat: no-repeat;
background-position: center;
background-size: contain;
```

## SVG

To use SVG image you must specify **./assets/imgs/xxxxxx.svg** instead of **../assets/imgs/xxxxx.svg**

## Using cascading svg

To start put the svg file which containing all the svg icons in your *assets/imgs* folder, then you need to retrieve each icon separately from your svg file. To do this, you can use an online svg generator like [http://www.spritecow.com/] wich generate you the css code like

```
.sprite {
	background: url('imgs/mysvgpicture.sprite--single.svg') no-repeat -42px -10px;
	width: 13px;
	height: 12px;
}
```

Then add it to your code (don't forget to adjust the real path for ionic)

```
.icon-share {
     background: url('../assets/imgs/mysvgpicture.sprite--single.svg') no-repeat -8px -8px;
     width: 17px;
     height: 16px;
 }
      
.icon-close {
     background: url('../assets/imgs/mysvgpicture.sprite--single.svg') no-repeat -42px -10px;
     width: 13px;
     height: 12px;
}
```

*View file*

```
<ion-content>
  <ion-icon class="icon-share"></ion-icon>
  <ion-icon class="icon-close"></ion-icon>
</ion-content>
```

### Sprite automation

Install sprity
```
$ npm install -g sprity
```

Then generate sprites and css with :

```
$ sprity ./output-directory/ ./input-directory/*.png
```

## Other way to use SVG icon

[https://golb.hplar.ch/2018/01/Custom-SVG-icons-in-Ionic.html]    

## Add blur on image

*style file*

```
    .blurred{
        -webkit-filter: blur(5px);
        -moz-filter: blur(5px);
        -o-filter: blur(5px);
        -ms-filter: blur(5px);
        filter: blur(5px);
    }
```

## Upload image

*View file*
```
<div>
    <button mat-stroked-button type="button" (click)="filePicker.click()">Upload image</button>
    <input type="file" #filePicker>
</div>
```

The ```<input type="file" #filePicker>``` create an automatic upload button which open the file explorer. But this button is not customizable, so we want using our custom button ```<button mat-stroked-button>``` to target the ```<input type="file" #filePicker>``` event.

*style file*
```
input[type="file"] {
    visibility: hidden
}
```
