[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# File storage
[Back to top](#ionic-2)  

[File Documentation here](https://ionicframework.com/docs/v2/native/file/)    

First install the plugin :

```
$ ionic cordova plugin add cordova-plugin-file
$ npm install --save @ionic-native/file
```

Then declare your module in the *app.module.ts*

```javascript
import {File} from '@ionic-native/file';

@NgModule({
  declarations: [
    MyApp,
    HomePage
  ],
  imports: [
    BrowserModule,
    IonicModule.forRoot(MyApp),
    IonicStorageModule.forRoot()
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp,
    HomePage
  ],
  providers: [
    File,
    StatusBar,
    SplashScreen,
    {provide: ErrorHandler, useClass: IonicErrorHandler},
    DataServiceProvider
  ]
})
export class AppModule {}
```

Next, lets see an example of easy read/write

*home.html*

```html
<ion-header>
  <ion-navbar>
    <ion-title>
      Ionic Blank
    </ion-title>
  </ion-navbar>
</ion-header>

<ion-content padding>
  <button ion-button (click)="onWriteFile()">WRITE FILE</button>
  <button ion-button (click)="onReadFile()">READ FILE</button>
  <button ion-button (click)="onDeleteFile()">DELETE FILE</button>
  <p>
    {{ filecontent }}
  </p>
</ion-content>
```

*home.ts*

```javascript
import { DataServiceProvider } from './../../providers/data-service/data-service';
import { Component } from '@angular/core';
import { NavController } from 'ionic-angular';
import {File} from '@ionic-native/file';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {

  filecontent: string = "";
  filename: string = "log.txt";

  constructor(public navCtrl: NavController, 
              private file: File) {
  }

  onWriteFile(){
    // Create file if does not exists
    this.file.createFile(this.file.externalDataDirectory,this.filename,false);

    // Write some text
    this.file.writeFile(this.file.externalDataDirectory,this.filename,'test ' + new Date() + "\r\n", {replace: false, append: true})
    .then(() => {
      console.log('write success');
    })
    .catch((err) => console.log("error " + JSON.stringify(err)))
  }

  onReadFile(){
    this.filecontent = "";
    this.file.readAsText(this.file.externalDataDirectory,this.filename)
    .then((data) => {
      this.filecontent += data;
    })
    .catch((err) => {
      console.log("Error " + JSON.stringify(err))
    })
  }

  onDeleteFile(){
    this.file.removeFile(this.file.externalDataDirectory,this.filename)
    .then(() => {console.log('success')})
    .catch((err) => {console.log('Delete file error : ' + JSON.stringify(err))})
  }

}

```

**Note :** In the example, we are using **exertnalDataDirectory** wich is not a private repository allows us to see the file with a file browser in the app repository. You can also use **dataDirectory** which is a private directory in order to hide your file in the file browser.

[Cordova plugin file documentation](https://github.com/apache/cordova-plugin-file)    

Next let see an other example of photo storage in a new file

*Controller file*

```javascipt
import { ModalController } from "ionic-angular";
import { Camera, File, Entry, FileError } from "ionic-native";

/**************************** 
 * IMPORTANT
 ***************************/
declare var cordova: any;	// trick wich inform typescript that this cordova variable will available at the runtime

export class HomePage {
    imageUrl = '';

    constructor(private modalCtrl: ModalController) {}

    onTakePhoto() {
        // See the documentation for more camera options
        Camera.getPicture({
            encodingType: Camera.EncodingType.JPEG,
            correctOrientation: true
        })
        .then(
            imageData => {
	    	// Save image to File
		const currentName = imageData.replace(/^.*[\\\/]/, '');	// extract the image name
		const path = imageData.replace(/[^\/]*$/, '');	// extract the image path
		const newFileName = new Date().getUTCMilliseconds() + ".jpg";
		File.moveFile(path, currentName, cordova.file.dataDirectory, newFileName)	// warning on cordova variable
		.then(
			(data: Entry) => {
				this.imageUrl = data.nativeURL;
				Camera.cleanup();
			}
		)
		.catch(
			(err: FileError) => {
			this.imageUrl = '';
			Camera.cleanup();
		});
                this.imageUrl = imageData;
            }
        )
        .catch(
            err => {console.log(err);}
        );
    }
    
    removeFile(place: Place){
	const currentName = place.imageUrl.replace(/^.*[\\\/]/, '');	// extract the image name
	File.removeFile(cordova.file.dataDirectory, currentName)
	.then(
		() => console.log("removed");
	)
	.catch(
		() => {
			console.log("error");
		}
	);
    }
}
```
