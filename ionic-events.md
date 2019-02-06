[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Events

## Page event

| Page event    | Description   |
| ------------- |---------------|
| ionViewLoaded      | Runs when the page has loaded. This event only happens once per page being created and added to the DOM. If a page leaves but is cached, then this event will not fire again on a subsequent viewing. The ionViewLoaded event is good place to put your setup code for the page. |
| ionViewWillEnter      | Runs when the page is about to enter and become the active page.      |
| ionViewDidEnter | Runs when the page has fully entered and is now the active page. This event will fire, whether it was the first load or a cached page.      |
| ionViewWillLeave | Runs when the page is about to leave and no longer be the active page. |	
| ionViewDidLeave | Runs when the page has finished leaving and is no longer the active page. |
| ionViewWillUnload | Runs when the page is about to be destroyed and have its elements removed. |
| ionViewDidUnload | Runs after the page has been destroyed and its elements have been removed. |

## Event propagation
[Back to top](#events)  

This code show how to stop event propagation

*View file*

```xml
<ion-content padding class="page1">
  <ion-list>
    <ion-item *ngFor="let item of items" (click)="openDetail(item.idDevice, item.nom)">
      <ion-button fill="clear" slot="end" (click)="pickDevice($event,item.idDevice)">
        <ion-icon green name="phone-portrait" slot="end"></ion-icon>
      </ion-button>
      <h2>{{item.nom}}</h2>
    </ion-item>
  </ion-list>
</ion-content>
```

*Controller file*

```javascript
pickDevice(event,_idDevice){
    event.stopPropagation();
}
```
