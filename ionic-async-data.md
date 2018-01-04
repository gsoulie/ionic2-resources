[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Async data

It is possible to use **async pipe** to get asynchronous data  : 

*View file*
```html
<ion-list>
  <ion-item *ngFor="let song of songs | async">
    {{song.title}}
  </ion-item>
</ion-list>
```
That snippet will create a list and populated with all the songs it finds in the songs array, we’ll create it next.

It’s also using the **async pipe**, because AngularFire2 returns Observables, which means it’s going to be retrieving the list items and sending them to the view as soon as they become available.
