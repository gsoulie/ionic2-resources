[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    


# Responsive grid gallery

*view file*

```
<ion-grid>
	<ion-row>
		<ion-col size="6" sizeMd="4" sizeXl="3" *ngFor="let i of dataset">
			<div class="image-container" [style.background-image]="'url(../assets/imgs/' + i.image + ')'">
			</div>
		</ion-col>
	</ion-row>
</ion-grid>
```

*style file*

```
.image-container {
	min-height: 200px;
	background-size: cover;
}
```

**Explanation** : 
```size="6"``` set the coloumn size for small screen (= 2 columns), ```sizeMd="4"``` set the coloumn size for medium screen (= 3 columns), ```sizeXl="3"``` set the coloumn size for very large screen screen (= 4 columns)

**Note** : You can also use ```sizeLg,  sizeSm sizeXs```

`
# Gallery

**cordova plugin installation**

```
$ ionic cordova plugin add cordova-plugin-camera
```

*import plugin in ts file*
```javascript
import { Camera } from ‘ionic-native’;
```

*declare image source component*

```javascript
private imageSrc: string;

constructor(private navCtrl: NavController) { }
```

*function to access gallery*

```javascript
private openGallery (): void {
  let cameraOptions = {
    sourceType: Camera.PictureSourceType.PHOTOLIBRARY,
    destinationType: Camera.DestinationType.FILE_URI,      
    quality: 100,
    targetWidth: 1000,
    targetHeight: 1000,
    encodingType: Camera.EncodingType.JPEG,      
    correctOrientation: true
  }

  Camera.getPicture(cameraOptions)
    .then(file_uri => this.imageSrc = file_uri, 
    err => console.log(err));   
}
```

*view file*

```xml
<div class="gallery-button" text-center>
    <img [src]="imageSrc" />    
  <ion-icon name="images" on-tap="openGallery()"></ion-icon>
  <p>Choose a Photo</p>  
</div>
```

*style file*

```css
ion-content, .toolbar-background {
	background-color: #4E8EF7;
	color: #FFFFFF;
}

ion-navbar {
	border-bottom: #FFFFFF 1px solid;
}

.gallery-button {
    bottom: 30px;
    position: absolute;
    width: 100%;    
}

ion-icon[name=images] {	
    border: #FFFFFF 2px solid;    
    border-radius: 50%;
    background-color: #2155AA;    
    font-size: 35px;
		padding: 15px;	
}
 
p {
	font-size: 20px;
}
```
