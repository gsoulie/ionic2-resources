[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# HTTP queries

* [api-helper](#api-helper)     
* [Http on PWA with HttpClient](#http-on-pwa)    
* [CORS and CORB issue](#cors-and-corb-issue)    


## api-helper

The best way to manage http queries is to create a new layer (api-helper.ts service) aimed to manipulate HttpClient like this https://github.com/gsoulie/Ionic2-snippets/blob/master/api-helper.ts

*api-helper.ts*

````typescript
import { Injectable } from '@angular/core';
import {HttpClient, HttpHeaders} from '@angular/common/http';
import { environment, SERVER_URL } from './../../environments/environment';

@Injectable({
  providedIn: 'root'
})
export class ApiHelperService {

  private _url: string;

  constructor(private http: HttpClient) {
    this._url = SERVER_URL;
  }

  // Manage HTTP Quesries
  requestApi({action, method = 'GET', datas = {}, httpHeader = {}}:
  { action: string, method?: string, datas?: any, httpHeader?: any }): Promise<any> {
    const methodWanted = method.toLowerCase();
    const urlToUse = this.url + action;

    const httpOptions = httpHeader !== {} ? httpHeader : {
      headers: new HttpHeaders({
        'Content-Type': 'application/json'
      })
    };

    let req = null;

    switch (methodWanted) {
      case 'post' :
        req = this.http.post(urlToUse, datas, httpOptions);
        break;
      case 'put' :
        req = this.http.put(urlToUse, datas, httpOptions);
        break;
      case 'delete' :
        req = this.http.delete(urlToUse, httpOptions);
        break;
      default:
        req = this.http.get(urlToUse + '?' + datas, httpOptions);
        break;
    }

    return req 
            .toPromise(); // convert to promise
  }

  get url(): string {
    return this._url;
  }
}

````

Then in your dataService :

````typescript  
  fetchItems(userId = '') {
    const httpOptions = {
      headers: new HttpHeaders({'Access-Control-Allow-Origin': '*', 'Content-Type': 'application/json',
      Authorization: 'Bearer ' + this.token})
    };
    
    return this.apiHelper.requestApi({ action: '/getItems', method: 'get', httpHeader: httpOptions, datas: parameters });
  }

  deleteItem(itemId = -1) {
    const httpOptions = {
      headers: new HttpHeaders({'Access-Control-Allow-Origin': '*', 'Content-Type': 'application/json',
      Authorization: 'Bearer ' + this.token})
    };

    return this.apiHelper.requestApi({ action: '/deleteItem/' + itemId,
                                        method: 'delete', httpHeader: httpOptions });
  }
````

### Handle errors on dialog

https://www.digitalocean.com/community/tutorials/how-to-use-angular-interceptors-to-manage-http-requests-and-error-handling

## Http on PWA

Http queries requires *HttpClient* instead of Native Cordova Http module which is used for Android and iOS app.

### Initialization

*app.module.ts*

```typescript
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  ...
  imports: [BrowserModule,..., HttpClientModule];
  ...
})
```

**optional** : You can install also *corsproxy*

```
npm install -g corsproxy
```

### GET

*data.service.ts*

```typescript
import { HttpClient, HttpHeaders } from '@angular/common/http';

constructor(private http: HttpClient) { }

fetchData(params) {
  const url = '<MY_URL>' + params
  const headerOptions = {
    headers: new HttpHeaders({'Access-Control-Allow-Origin': '*', 'Content-Type': 'application/json'})
  };
  
  return this.http.get(url, headerOptions);
}
```

**Important** import HttpClient module from ```import { HttpClient, HttpHeaders } from '@angular/common/http';```

*home.page.ts*

```typescript
async fetchData() {
  await this.dataService.fetchData()
  .subscribe( data => {
    const jsonData = data as any[];
    
    for(let i = 0; i < jsonData.length; i++) {
      // some stuff here...
    }
  });
}
```

### POST

*data.service.ts*

```typescript
import { HttpClient, HttpHeaders } from '@angular/common/http';
...

constructor(private http: HttpClient) { }
/**
* WS waiting for { Login: login, Password: password } JSON object parameter
*
*/
onLogin(login, password) {
  const url = '<MY_URL>';
  const postData = {
    Login: login,
    Password: password
  };
  
  const httpOptions = {
    headers: new HttpHeaders({
      'Content-Type': 'application/json'
    })
  };
  
  this.http.post(url, postData, httpOptions)
  .subscribe(
    data => {
      console.log('Success');
    },
    error => {
      console.log('Error', error);
    }
  );
}
```

[link : using http](http://www.joshmorony.com/using-http-to-fetch-remote-data-from-a-server-in-ionic-2/)

[link : reddit API url for testing app](https://www.reddit.com/r/gifs/top/.json?limit=10&sort=hot)

**Warning** You may encountered a ```Uncaught (in promise): Error: No provider for Http!``` error during serving the app. To avoid this, you have to include HttpModule in your *app.module.ts*

*app.module.ts*

```typescript
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

```typescript
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
```typescript
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

```typescript
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

## PUT query

In order to avoid *http.put* query issue when sending binary file, you can use *FileTransfer*(https://ionicframework.com/docs/native/file-transfer/) to send your *PUT* query like below :
 
```typescript
import { FileTransfer, FileUploadOptions, FileTransferObject } from '@ionic-native/file-transfer';

@Injectable()
export class MyProvider { 

constructor(public transfer: FileTransfer){ }

onSendHttpPutQuery(myPictureFullPathURI){
const fileTransfer: FileTransferObject = this.transfer.create(); 
 
let options: FileUploadOptions = { 
    fileKey: 'file', 
    headers: { 'Content-Type': 'image/jpg', 'Access-Control-Allow-Origin': '*' }, 
    httpMethod: 'PUT', 
    mimeType: 'image/jpg' 
} 

return fileTransfer.upload(myPictureFullPathURI,myWebServiceURL, options) 
.then((result) => { 
    return result; 
}, (err) => { 
    alert("request failure - ", JSON.stringify(err)); 
}); 
}
}
```

## CORS and CORB issue

You may encounter CORS and CORB issue with Http queries. To avoid these issues, you need to add on your server the url of your PWA (and your localhost:8100 for ionic serve) in the authorized app. 
