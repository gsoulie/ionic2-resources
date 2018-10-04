[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Known issues

### Clicking in list item in simulator sometimes(!) doesn’t work on device

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

If you create custom components or html element (div or ion-card) and then use click handler like ```(click)="itemTap()"``` you may notice a delay if the element are not an anchor tag or button.
To fix it, add **tappable** as an attribute to you element or component like ```(click)="itemTap($event)" tappable``` or add it on your view file like ```<ion-card tappable="true" (click)="onOpen()">```

see also : button role on non button clickable elements ```role="button"```

### Conflict with "this" on callback

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
  
  To insert ```\r\n``` tags, use that syntax :
  
  ```
  <p  [innerHTML]="texteAvecBalises"></p>
  ```
