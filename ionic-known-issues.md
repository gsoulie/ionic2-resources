[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Known issues

* [Clicking item does not work](#)     
* [Slow tap on component](#slow-tap-on-component-and-element)     
* [Conflict with "this" on callback](#conflict-with-"this"-on-callback)    
* [Scrolling when entering input field](#scrolling-when-entering-input-field)    
* [Display carriage return](#display-carriage-return)    
* [Remove iOS cache](#remove-ios-cache)    
* [Gradle build failure](#gradle-build-failure-app:debugCompileClasspath)    

### Clicking in list item in simulator sometimes(!) doesn’t work on device
[Back to top](#known-issues)    

Sometimes,  on the emulator or on device, 2 out of 3 clicks it doesn't fire the click function. To solve the issue, change ```<ion-item>``` to ```<button>``` with the styling of ```ion-item```.

```xml
<ion-list>
	<button ion-item *ngFor="let audience of audience" (click)="pushProfilesList(audience)">
		<h2 class="list-title">{{audience.segment}}</h2>
		<span class="list-info">{{audience.amount}}</span>
	</button>
</ion-list>
```

### Slow tap on component and element
[Back to top](#known-issues)    

If you create custom components or html element (div or ion-card) and then use click handler like ```(click)="itemTap()"``` you may notice a delay if the element are not an anchor tag or button.
To fix it, add **tappable** as an attribute to you element or component like ```(click)="itemTap($event)" tappable``` or add it on your view file like ```<ion-card tappable="true" (click)="onOpen()">```

see also : button role on non button clickable elements ```role="button"```

### Conflict with "this" on callback
[Back to top](#known-issues)    

refering to **this** has traditionally been a pain point in JavaScript. Fat arrows fix it by capturing the meaning of this from the surrounding context. So it is very useful in case of calling asynchronous callback after a window closing event.

**use case :** We want to refresh a list of items after adding a new item on it (via a "new item" modal)

*List.ts*

```javascript
...
// Refresh item list
refreshRouteList(){
    this.Db.getRoutesList().then((data)=> this.routesList = data);
}

//Open new item modal
newItem(){
    // Open DetailPage with callback function wich refresh the list after adding a new item
    /* below : traditional callback syntax --> does not work because 'this' conflict
    this.navCtrl.push(DetailPage,{callback: function(){
            this.refreshRouteList();
        }
    });*/
    
    // New syntax with Arrow function
    this.navCtrl.push(DetailPage,{callback:
        () => {this.refreshRouteList();}
    });
 }
...
```

An other solution consist in declare a variable wich contain **this** :

*List.js*

```javascript
var myThis = this;

newItem(){
    // Open DetailPage with callback function wich refresh the list after adding a new item
      
    this.navCtrl.push(DetailPage,{callback:function(){
        myThis.refreshRouteList();
    });
 }
...
```


*DetailPage.js*

```javascript
...
save(){
    this.callback(); // refreshRouteList() callback
}
...
```

### Scrolling when entering input field
[Back to top](#known-issues)    

To remove scrolling when entering input field focus, add the code below in you *app.component.ts*

```javascript
 imports: [
    BrowserModule,
    IonicModule.forRoot(MyApp,{
      scrollAssist: false, 
      autoFocusAssist: false
    })    
  ],
  ```
  
  ### Display carriage return
  [Back to top](#known-issues)    
  
  To insert ```\r\n``` tags, use that syntax :
  
  ```
  <p  class="pClass">{{someTextWithCarriageReturn}}</p>
  ```
  
  ```
  .pClass{
        white-space: pre-line;
        overflow: auto;
  }
  ```
  
  ### Remove iOS cache
  [Back to top](#known-issues)    
  
  **iOS**
  
  ```javascript
  import { File } from '@ionic-native/file';
  
  if(platform.is('ios')){
      this.file.removeRecursively(this.file.cacheDirectory, "Webkit").catch(err => console.log(err));
  }
  ```
  
  **Android**
  
  For Android, use *disable-http-cache-cache* plugin
  
### Gradle build failure app:debugCompileClasspath
  
```
FAILURE: Build failed with an exception.
 
* What went wrong:
Could not resolve all files for configuration ':app:debugCompileClasspath'.
> Could not find support-v4.aar (com.android.support:support-v4:27.1.1).
  Searched in the following locations:
      https://jcenter.bintray.com/com/android/support/support-v4/27.1.1/support-v4-27.1.1.aar
```

To fix it, modify your */platforms/android/build.gradle* to reflects the following :

```javascript
 allprojects {
    repositories {
    	// JUST REPLACE repositories content by the following
	mavenLocal()
	maven { url 'https://maven.google.com' }
	jcenter()
	maven {
	// All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
	url "$rootDir/../node_modules/react-native/android"
	}
	maven {
		url 'https://maven.google.com/'
		name 'Google'
	} 
    }
    project.ext{
	defaultBuildToolsVersion="25.0.2"
	defaultMinSdkVersion=19
	defaultTargetSdkVersion=26
	defaultCompileSdkVersion=26
    }

}
  ```

