[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Using image

* [Background image](#background-image)    
* [SVG](#svg)    
* [Using cascading SVG](#using-cascading-svg)    
* [Add blur](#add-blur-on-image)    
* [Upload image](#upload-image)    
* [Converting base64 string to blob](#converting-base64-string-to-blob)     

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

### Customize SVG

In order to modify the fill or stroke color of your svg, you need to modify your svg file by removing all *fill* and *stroke* attributes which have a value

*example of svg file*

````
<svg xmlns="http://www.w3.org/2000/svg" width="20" height="16" viewBox="0 0 20 16">
  <g fill="#88AACC" fill-rule="evenodd" stroke-linecap="round" stroke="#555555" stroke-linejoin="round" stroke-width="2">
     ...
  </g>
</svg>
````

*svg file modified by removing fill ans stroke attributes*

````
<svg xmlns="http://www.w3.org/2000/svg" width="20" height="16" viewBox="0 0 20 16">
  <g fill-rule="evenodd" stroke-linecap="round" stroke-linejoin="round" stroke-width="2">
     ...
  </g>
</svg>
````

You are now able to control your svg stroke and fill attributes by adding css 

*css file*

````
<ion-icon class="stroked" src="./assets/imgs/my-picto.svg"></ion-icon>
<ion-icon class="filled" src="./assets/imgs/my-picto.svg"></ion-icon>

.stroked {
	stroke: red !important;
	fill: transparent !important;
}
.filled {
	stroke: red !important;
	fill: red !important;
}
````

#### Customizing ion-icon on ion-tab

````
<ion-tab-button tab="search" class="stroke-icon" (click)="handleTabSelect('search', $event)">
      <ion-icon src="./assets/imgs/pictos/picto_menu_search.svg"></ion-icon>
      <ion-label>Search</ion-label>
</ion-tab-button>
````

*style file*

````

.stroke-icon:active {
    ion-icon {
        stroke: var(--ion-color-primary) !important;
    }    
}
.stroke-icon:link {
    ion-icon {
        stroke: var(--ion-color-primary) !important;
    }
}
.stroke-icon:focus {
    ion-icon {
        stroke: var(--ion-color-primary) !important;
    }
}
.stroke-icon {
    ion-icon {
        stroke: var(--ion-color-text-light-grey) !important;
    }
}
````

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

## Converting base64 string to blob

https://ionicframework.com/blog/converting-a-base64-string-to-a-blob-in-javascript/

````
const base64Data = "aGV5IHRoZXJl";
const base64 = await fetch(base64Data);
````
Depending on the format of the base64 string, you might need to prepend content type data. For example, a JPEG image:
````
const base64Response = await fetch(`data:image/jpeg;base64,${base64Data}`);
````
Next, convert the response to a blob:
````
const blob = await base64Response.blob();
````

### Convert blob to base64 string

````
convertBlobToBase64 = (blob) => new Promise((resolve, reject) => {
    const reader = new FileReader;
    reader.onerror = reject;
    reader.onload = () => {
        resolve(reader.result);
    };
    reader.readAsDataURL(blob);
});

const base64String = await convertBlobToBase64(blob);
````
