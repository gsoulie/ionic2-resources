[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# File

* [Capacitor FileSystem](#capacitor-filesystem)    
* [File size](#file-size)    
* [PWA : write file](#pwa-write-file)     
* [Loading json file](#loading-json-file)    
* [Upload file with file explorer](#upload-file-with-file-explorer)     
* [Open file with native system viewer](#open-file-with-native-system-viewer)     

## Capacitor filesystem
[Back to top](#file)  

[Capacitor FileSystem Documentation](https://capacitorjs.com/docs/apis/filesystem)    

**2021 - android permission issues**

* Android documentation : https://developer.android.com/training/data-storage/use-cases#migrate-legacy-storage
* https://github.com/ionic-team/capacitor-plugins/issues/169
* https://github.com/ionic-team/capacitor/issues/4117

### Installation

```
npm install @capacitor/filesystem
npx cap sync
```

### Android permissions

If using ````Directory.Documents```` or ````Directory.ExternalStorage````, this API requires the following permissions be added to your **AndroidManifest.xml**

````
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
````

### Directories

|Directory|Detail|
|-|-|
|Documents|The Documents directory On iOS it’s the app’s documents directory. Use this directory to store user-generated content. On Android it’s the Public Documents folder, so it’s accessible from other apps. It’s not accesible on **Android 10** unless the app enables legacy External Storage by adding ````android:requestLegacyExternalStorage="true"```` in the application tag in the AndroidManifest.xml. It’s **not accesible on Android 11 or newer**|
|Data|The Data directory On iOS it will use the Documents directory On Android it’s the directory holding application files. Files will be deleted when the application is uninstalled.|
|Cache|The Cache directory Can be deleted in cases of low memory, so use this directory to write app-specific files that your app can re-create easily|
|External|The external directory On iOS it will use the Documents directory On Android it’s the directory on the primary shared/external storage device where the application can place persistent files it owns. These files are internal to the applications, and not typically visible to the user as media. Files will be deleted when the application is uninstalled.|
|ExternalStorage|The external storage directory On iOS it will use the Documents directory On Android it’s the primary shared/external storage directory. It’s not accesible on **Android 10** unless the app enables legacy External Storage by adding ````android:requestLegacyExternalStorage="true"```` in the application tag in the AndroidManifest.xml. It’s **not accesible on Android 11 or newer**|


````WARNING !!```` Note that ````Directory.Documents```` and ````Directory.ExternalStorage```` are only available on ````Android 9 or older````


### Usage

````typescript
import { Filesystem, Directory, Encoding } from '@capacitor/filesystem';
...

async writeTestFile () {
    await Filesystem.writeFile({
      path: 'text.txt',
      data: "This is a test",
      directory: Directory.External,	// --> stored in \Phone\Android\data\com.myapp.myapp\files
      //directory: Directory.Documents,	// --> stored in public \Phone\Documents (unsafe)
      encoding: Encoding.UTF8,
    });
  };

  async readTestFile () {
    const contents = await Filesystem.readFile({
      path: 'text.txt',
      directory: Directory.External,
      encoding: Encoding.UTF8,
    });

    console.log('secrets:', contents);
  };
````

### Photo storage (old example need to be updated with Capacitor 3.0)

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

## Open file with native system viewer
[Back to top](#file)

### FileOpener 2

> tested with success with ionic 6 / capacitor 3 / Android 12
> 
### installation & configuration

````
npm install cordova-plugin-file-opener2 
npm install @awesome-cordova-plugins/file-opener 
npm install @awesome-cordova-plugins/core
npx cap sync
````

*app.module.ts*
````typescript
import { FileOpener } from '@awesome-cordova-plugins/file-opener/ngx';

...
providers: [
    ...,
    FileOpener
]
````

*AndroidManifest.xml*
````
<application
		...
        android:requestLegacyExternalStorage="true">
````

### Usage

*home.component.ts*
````
import { FileOpener } from '@awesome-cordova-plugins/file-opener/ngx';

constructor(private fileOpener: FileOpener) {}
	
private async openFile(filepath) {
    if (isPlatform('hybrid')) {
      // Récupérer l'uri du fichier
      const fileUri = await Filesystem.getUri({
        directory: Directory.Document,
        path: filepath
      });

      const mime = this.fileHelper.getMimeType(filepath);
      
      this.fileOpener.open(fileUri.uri, mime)
      .then(() => console.log('File is opened'))
      .catch(e => console.log('Error opening file', e));
    }
  }
  
 private getMimeType(mimeString: string): string {

    mimeString = mimeString.toLocaleLowerCase();
    if (mimeString.indexOf('pdf') >= 0) {
      return 'application/pdf';
    } else if (mimeString.indexOf('png') >= 0) {
      return 'image/png';
    } else if (mimeString.indexOf('jpg') >= 0 || mimeString.indexOf('jpeg')) {
      return 'image/jpg';
    }
}
````
[Back to top](#file)
