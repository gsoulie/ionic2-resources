# File storage
[Back to top](#ionic-2)  

[File Documentation here](https://ionicframework.com/docs/v2/native/file/)    

First install the plugin :

```
$ ionic cordova plugin add cordova-plugin-file
```

Next let see an example of photo storage in a new file

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