[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# HTTP query

[link : using http](http://www.joshmorony.com/using-http-to-fetch-remote-data-from-a-server-in-ionic-2/)

[link : reddit API url for testing app](https://www.reddit.com/r/gifs/top/.json?limit=10&sort=hot)

**Warning** You may encountered a ```Uncaught (in promise): Error: No provider for Http!``` error during serving the app. To avoid this, you have to include HttpModule in your *app.module.ts*

*app.module.ts*

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule, ErrorHandler } from '@angular/core';
import { HttpModule } from '@angular/http';
...
  imports: [
    BrowserModule,
    HttpModule,
    IonicModule.forRoot(MyApp),
    IonicStorageModule.forRoot()
  ],
...
```

**Retrieve remote data**

First, create a provider with the code below :

```javascript
import {Injectable} from '@angular/core';
import {Http} from '@angular/http';
import 'rxjs/add/operator/map';
 
@Injectable()
export class httpProvider {

  constructor(public http: Http) {
 	console.log("hello");
  }
  
  getRemoteData(){
    this.http.get('https://www.reddit.com/r/gifs/new/.json?limit=10').map(res => res.json()).subscribe(data => {
        console.log(data);
    });
  }
}
```

> Note : The **map** lib is only imported because we need is http.get function

**Usage**

In your controller, inject your provider like below :
```javascript
import {Component} from '@angular/core';
import {NavController} from 'ionic-angular';
import {httpProvider} from '../../providers/http-provider';

@Component({
    selector: 'page-home',
    templateUrl: 'home.html'
})
export class HomePage {
    constructor(public httpProvider: HttpProvider){
    }
    
    ionViewDidLoad(){
    	this.httpProvider.getRemoteData();
    }
}
```

**Retrieve local data**

As the same way, you can retrieve local data. First, create a "data" sub folder under *assets* folder which contain a *localData.json*. Then use the http with the local path

```javascript
import {Injectable} from '@angular/core';
import {Http} from '@angular/http';
import 'rxjs/add/operator/map';
 
@Injectable()
export class httpProvider {

  constructor(public http: Http) {
 	console.log("hello");
  }
  
  getRemoteData(){
    this.http.get('https://www.reddit.com/r/gifs/new/.json?limit=10').map(res => res.json()).subscribe(data => {
        console.log(data);
    });
  }
  
  getLocalData(){
    this.http.get('assets/data/localData.json').map(res => res.json()).subscribe(data => {
        console.log(data);
    });
  }
}
```
