[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# File

* [File storage](#file-storage)    
* [File size](#file-size)    
* [PWA : write file](#pwa-write-file)     
* [Loading json file](#loading-json-file)    
* [Upload file with file explorer](#upload-file-with-file-explorer)     

## File storage
[Back to top](#file)  

[File Documentation here](https://ionicframework.com/docs/v2/native/file/)    

First install the plugin :

```
$ ionic cordova plugin add cordova-plugin-file
$ npm install --save @ionic-native/file
```

Then declare your module in the *app.module.ts*

```typescript
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
  <ion-button (click)="onWriteFile()">WRITE FILE</ion-button>
  <ion-button (click)="onReadFile()">READ FILE</ion-button>
  <ion-button (click)="onDeleteFile()">DELETE FILE</ion-button>
  <p>
    {{ filecontent }}
  </p>
</ion-content>
```

*home.ts*

```typescript
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

  onDeleteFile(filename: string){
    if(filename !== ""){
      this.file.checkFile(this.file.externalDataDirectory, filename)
      .then((data) => {
        if(data){
          this.file.removeFile(this.file.externalDataDirectory, filename)
          .then(()=> {})
          .catch((error) => this.logError("File deletion error : " + JSON.stringify(error)))
        }
      })
    }
  }

}

```

**Note :** In the example, we are using **exertnalDataDirectory** wich is not a private repository allows us to see the file with a file browser in the app repository. You can also use **dataDirectory** which is a private directory in order to hide your file in the file browser.

[Cordova plugin file documentation](https://github.com/apache/cordova-plugin-file)    

Next let see an other example of photo storage in a new file

*Controller file*

```typescript
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

## File size
[Back to top](#file)  


```typescript
import { Component } from '@angular/core';
import { NavController, NavParams } from 'ionic-angular';
import { Camera } from '@ionic-native/camera';
import { File } from '@ionic-native/file';
declare var window;

@Component({
  selector: 'page-photo',
  templateUrl: 'photo.html',
})
export class PhotoPage {
  fileSize;
  consoleLog: string = "";

  constructor(public navCtrl: NavController, public navParams: NavParams, private camera: Camera, private file: File) {
  }

  ionViewDidLoad() {
  }
  onTakePhoto(){
    this.camera.getPicture({
      quality: 100,
      targetWidth: 900,
      targetHeight: 600,
      destinationType: this.camera.DestinationType.FILE_URI,
      encodingType: this.camera.EncodingType.JPEG,
      mediaType: this.camera.MediaType.PICTURE,
      saveToPhotoAlbum: false,
      allowEdit: false,
      sourceType: this.camera.PictureSourceType.CAMERA
    }).then((imagedata) => {
      var tempName = imagedata.substr(imagedata.lastIndexOf('/') + 1);
      var tempPath = imagedata.substr(0, imagedata.lastIndexOf('/') + 1);
      this.consoleLog += "imageData : " + JSON.stringify(imagedata) + "\r\n";
      this.consoleLog += "tempName : " + tempName + "\r\n";
      this.consoleLog += "tempPath : " + tempPath + "\r\n";

      this.file.resolveLocalFilesystemUrl(imagedata).then(fileEntry => {
        fileEntry.getMetadata((metadata) => {
            this.fileSize = metadata.size;	// size in bytes
        })
      })
    })
  }
}

```

## PWA write file
[Back to top](#file)  

Here's how to write file in a PWA

*Controller file*

```typescript
onWriteFile(){
    let filename = "myfile.csv"
    let data = "my text to write in file. Could be anything you want";
    var blob = new Blob([dataToWrite],{type:'text/plain'}),
    e = document.createEvent('MouseEvents'),
    a = document.createElement('a');

    // For IE	
    if (window.navigator && window.navigator.msSaveOrOpenBlob) {
      window.navigator.msSaveOrOpenBlob(blob, filename);
    } else {
        a.download = filename;
        a.href = window.URL.createObjectURL(blob);
        a.dataset.downloadurl = ['text/plain', a.download, a.href].join(':');
        e.initEvent('click', true, false);
        a.dispatchEvent(e);
    }
  }
```

## Loading Json File
[Back to top](#file)

Add the following options into *tsconfig.json* config file

*tsconfig.json*

```json
{
	"compileOnSave": false,
	"compilerOptions": {
		...
		"resolveJsonModule": true,
		"esModuleInterop": true
	}
}
```
Then import your json data as below 

*controller file*

```typescript
import MyJsonData from '../../assets/myJsonFile.json';

export class MyClass {
	getJsonData() {
		return MyJsonData
		//return MyJsonData.slice(0, 50)
	}
}
```

## Upload file with file explorer
[Back to top](#file)

*View file*

````html
<div class="import-div">
  <input type="file" accept=".json" (change)="change($event)" id="file" />
  <label for="file">Import json file</label>
</div>
````

*Controller file*

````typescript
async change(event: any) {
    const file = event.target.files[0];
    const fileContent = await this.readFileContent(file);	
    const convertedData = JSON.parse(fileContent) as any[]; // example : Convert string result as Json array
}

// Return file content in string format
async readFileContent(file: File): Promise<string> {
    return new Promise<string>((resolve, reject) => {
        if (!file) {
            resolve('');
        }
        const reader = new FileReader();
        reader.onload = (e) => {
            const text = reader.result.toString();
            resolve(text);
        };
        reader.readAsText(file);
    });
}
````

*Style file*

````css
.import-div {
    width: 100%;
    padding-left: 7px;
    padding-right: 7px;
}
[type="file"] {
    height: 0;
    overflow: hidden;
    width: 0;
}

[type="file"] + label {
    background: white;    
    border-radius: 40px;
    border: 3px solid #58b937;
    color: #58b937;
    cursor: pointer;
    display: inline-block;
    font-family: 'Poppins', sans-serif;
    font-size: 14px;
    font-weight: 600;
    margin-bottom: 1rem;
    outline: none;
    padding-top: 8px;
    padding-bottom: 8px;
    text-align: center;
    position: relative;
    transition: all 0.3s;
    vertical-align: middle;
    width: 100%;
    &:hover {
      background-color: #58b937;
      color: white;
    }
}
````

