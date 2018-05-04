[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Using image

* [SVG](#svg)    
* [Using cascading SVG](#using-cascading-svg)    
* [Add blur](#add-blur-on-image)    

To use image in your app, you can store them under ```src/assets/imgs/*.png```

*View file*

```xml
<img class="thb" src="{{item.image}}" item-left/>
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

Then add it to your code (juste adjust the real path for ionic)

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
