# Using image
[Back to top](#ionic-2) 

To use image in your app, you can store them into ```src/app/assets/*.png```

*View file*

```xml
<img class="thb" src="{{item.image}}" item-left/>
```

*Controller file*

```javascript
let item = {name:"my item", image:"./assets/my_image.png"}
```

*Style file*

```css
.thb{
  width: 50px;
}
```