[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Using image

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


# Add blur on image

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
