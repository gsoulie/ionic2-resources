
[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Communication between Ionic and NodeJS    

* [Project structure](#project-structure)    
* [Setting up the NodeJS Server](#setting-up-the-nodejs-server)    
* [Making a REQUEST to NodeJS](#making-a-request-to-nodejs)    
* [Run](#run)    

[link : josh Morony communicating-between-an-ionic-application-and-a-nodejs-backend](https://www.joshmorony.com/communicating-between-an-ionic-application-and-a-nodejs-backend/)    

## Project Structure

How you structure your project is important – you need to keep in mind that your NodeJS backend and your Ionic front-end are two separate entities. You may feel like creating a server folder to hold your NodeJS code inside of your Ionic project, but this isn’t a good idea – you’re mixing your front-end and your back-end code and you certainly don’t want to package your server code inside of your Ionic application when sending it out for production.

Here is an example of structure :

> Create a folder called **no-homers**

> Create a folder inside of *no-homers* called **server** to hold your NodeJS server

> Generate a new Ionic application inside of *no-homers* called **client**

## Setting up the NodeJS Server
[Back to top](#communication-between-ionic-and-nodejs)  

We are going to start by setting up our NodeJS server. The basics we will need are a **package.json** file that describes the dependencies that our server will need, and a **server.js** file that will contain our servers code.

For now, just create a blank file called **server.js** inside of the server folder. You can also create your **package.json** manually, or you can run the following command:

```npm init```

inside of your **server** folder, and it will prompt you through the creation of the file. You really only need to answer the following questions right away:

```
package name: your-server-name
version: 0.0.1
description: some-description
entry-point: server.js
```

Run the following command inside of the server folder:

```
npm install --save body-parser cors del express method-override morgan
```

Now let’s set up the basic template for our server code.

Modify *server/server.js* to reflect the following:

```javascript
var express = require('express'); // express is used to set routes
var bodyParser = require('body-parser');  // body-parser will help grab information from POST requests
var logger = require('morgan'); // morgan outputs useful debugging messages
var methodOverride = require('method-override') // method-override provides support for DELETE and PUT requests
var cors = require('cors'); // cors package provides a simple way to handle CORS (Cross Origin Resource Sharing) issues
 
var app = express();
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(methodOverride());
app.use(cors());

// /checkname POST webservice
app.post('/checkname', function(req, res){
    if(req.body.name.toLowerCase() === 'homer'){
        // If username = 'homer' generate error 401 status (not recommanded !)
        res.status(401).send({message: 'Sorry, no Homer\'s!'});
 
    } else {
        // If ok, send response to client
        res.send({
            passed: true,
            message: 'Welcome, friend!'
        });
 
    }
});

// checkname GET webservice
app.get('/checkname/:name', function(req, res){
 
    if(req.params.name.toLowerCase() === 'homer'){
        // If username = 'homer' generate error 401 status (not recommanded ! Better redirect on another page)
        res.status(401).send({message: 'Sorry, no Homer\'s!'});
 
    } else {
        // If ok, send 'welcome' message
        res.json('Welcome!');
 
    }
 
});
 
app.listen(process.env.PORT || 8080); // Listening on localhost 8080
```

In both cases, we convert the supplied value to lowercase and see if it is equal to “homer”. If it is, we set the HTTP status to 401, which is the “Unauthorized” error code (you don’t have to do this, but it’s a good idea to provide useful status codes), and then in the POST route we send a response to the Ionic application by using the send method. If the name is not equal to “homer”, then we send a JSON object back that includes a message.

The GET route is very similar, except rather than using res.send we use res.json. These methods are mostly identical, and will both send a response back to the Ionic application, but the benefit of using res.json is that it will format any data supplied as valid JSON data (even if we just supply a simple string like ‘Welcome!’).

## Making a REQUEST to NodeJS
[Back to top](#communication-between-ionic-and-nodejs)   

Now that we have the basics of our project set up, we are going to create the requests that we want to send to our NodeJS server from our Ionic application. We will set up a form that allows us to enter a name, and some code to handle sending the results to the server.

Modify *home.html* to reflect the following:

```html
<ion-header>
  <ion-navbar>
    <ion-title>
      Ionic Blank
    </ion-title>
  </ion-navbar>
</ion-header>

<ion-content padding>
    <ion-list>
      <ion-item>
        <ion-label floating>Enter your name...</ion-label>
        <ion-input [(ngModel)]="name" type="text"></ion-input>
      </ion-item>
    </ion-list>
    <button ion-button primary (click)="checkName()">Enter</button>
    <ion-label>{{ response }}</ion-label>
</ion-content>
```

Modify *home.ts* to reflect the following

```javascript
import { Component } from '@angular/core';
import { NavController } from 'ionic-angular';
import { Http } from '@angular/http';
import { map } from 'rxjs/operators';
 
@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
 
    name: string;
    response: string = "";
 
    constructor(private http: Http) {
 
    }
 
    checkName() {
        this.response = "";
        let data = {
            name: this.name
        };
 
        this.http.post('http://localhost:8080/checkname', data).pipe(
            map(res => res.json())
        ).subscribe(response => {
            console.log('POST Response:', response);
        });
 
        this.http.get('http://localhost:8080/checkname/' + this.name).pipe(
            map(res => this.response = res.json())
        ).subscribe(response => {
            console.log('GET Response:', response);
        });
 
    }
 
}
```

NOTE: In order to use Http you will need to add the HttpModule to your *src/app/app.module.ts* file.

```javascript
import { BrowserModule } from '@angular/platform-browser';
import { ErrorHandler, NgModule } from '@angular/core';
import { IonicApp, IonicErrorHandler, IonicModule } from 'ionic-angular';
import { SplashScreen } from '@ionic-native/splash-screen';
import { StatusBar } from '@ionic-native/status-bar';
import { MyApp } from './app.component';
import { HomePage } from '../pages/home/home';
import { HttpModule } from '@angular/http';

@NgModule({
  declarations: [
    MyApp,
    HomePage
  ],
  imports: [
    BrowserModule,
    HttpModule,
    IonicModule.forRoot(MyApp)
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp,
    HomePage
  ],
  providers: [
    StatusBar,
    SplashScreen,
    {provide: ErrorHandler, useClass: IonicErrorHandler}
  ]
})
export class AppModule {}
```

now, we are expecting to get a JSON response in return, and we log that out to the console.

## Run
[Back to top](#communication-between-ionic-and-nodejs)   

First open a terminal and run ```npm start```

Then in an other terminal instance, run your ionic app ```ionic serve -lsc```

> **Important** : note that if you run your application on real device, this will could not communicate with the nodeJS server which is listening on localhost (8080).

### Response 

After running the NodeJS server and the ionic app, if you send **homer** as user, you will get the following response (use chrome console)

```
> GET http://localhost:8080/checkname/homer 401 (Unauthorized)
> ERROR
  Response {_body: "{message: Sorry, no Homer's}", status: 401, ok: false, statusText: "Unauthorized"...}
  POST http://localhost:8080/checkname 401 (Unauthorized)
> ERROR
  Response {_body: "{message: Sorry, no Homer's}", status: 401, ok: false, statusText: "Unauthorized"...}
```

Now, if you send a name other than *homer*, you will get the following response :

```
GET Response: Welcome !
POST Response: > {passed: true, message: "welcome friend!"}
```



