[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Concepts

* [Angular 2](#angular-2)  
* [Decorators](#decorators)    	
* [Pipe](#pipe)    
* [RxJS operators](#rxjs-operators)      
* [Promise vs Observable](#promise-vs-observable)    
* [Observable and operators](#observable-and-operators)      
* [BehaviorSubject](#behaviorsubject)    
* [Arrow function](#arrow-function)     
* [Async / Await functions](#await-async-functions)     
* [Resolver](#resolver)     
* [Environments DEV/PROD](#environments)    
* [Function declaration](#function-declaration)       

## Angular 2
[Back to top](#concepts)  

[link : become a ninja with Angular 2](https://books.ninja-squad.com/public/samples/Become_a_ninja_with_Angular2_sample.html)    

[link : new Angular 2 concepts and syntax](http://www.joshmorony.com/ionic-2-first-look-series-new-angular-2-concepts-syntax/)    

[link : Angular 2 syntax demistified](http://blog.thoughtram.io/angular/2015/08/11/angular-2-template-syntax-demystified-part-1.html)    

[link : AngularUI](https://angular-ui.github.io/)

AngularJS 2 is the backbone of Ionic 2, is being written for ECMAScript 6

### Some Angular 2 concepts

**Binding a Property to a value**
```xml
<input [value]="firstName">
```

This will set the elements property to fisrtName. More used to see ```{{firstName}}```

**Calling a Function on an Event**

```xml
<ion-button (click)="someFunction($event)">
```

This will call the someFunction function and pass in the event whenever the button is clicked on. You can replace click with any native or custom event you like. You can also use the following syntax:

```xml
<ion-button (^click)="someFunction($event)">
```
to make the event bubble up to other elements.

**Rendering Expressions**

```xml
<p>Hi, {{name}}</p>
```

Just like in Angular 1, this will evaluate the expression and render the result. There’s still some things that are the same!

**Two Way Data Binding**

In Angular 1 we could set up two way data binding by using ng-model, so if we changed a value in an input field with an ng-model it would immediately be updated elsewhere in the application if we were using that value.

We can achieve the same two way data binding in Angular 2 like this:

```xml
<input [value]="name" (input)="name = $event.target.value">
```

This sets the value to the expression name and when we detect the input event we updated name to be the new value that was entered. To make this easier, we can still use ng-model in Angular 2 like this to achieve the same thing:

```xml
<input [(ng-model)]="name">
```

**Creating a Variable to Access an Element**

```xml
<p #myParagraph></p>
```
This creates a local variable that we can use to access the element, so if I wanted to add some content into this paragraph I could do the following:

```xml
<ion-button (click)="myParagraph.innerHTML = 'Once upon a time...'">
```

**Directives**

```javascript
<section *ngIf="showSection">
 
<li *ngFor="let item of items">
```
We can also create embedded templates using directives like ngIf and ngFor.

**Import & Export**

ES6 allows us to Import and Export components. Take the following component for example:

```javascript
import {IonicView, NavController} from 'ionic/ionic';
 
@IonicView({
  templateUrl: 'app/hello-ionic/hello-ionic.html'
})
export class HelloIonicPage {
  constructor(nav: NavController) {
    this.nav = nav;
  }
}
```
This component is making use of the IonicView and NavController components so it imports them. The HelloIonicPage component that is being created here is then exported.

Now you would be able to access HelloIonicPage by importing it elsewhere:

```javascript
import {HelloIonicPage} from './pages/hello-ionic/hello-ionic';
```
It’s a similar concept to Dependency Injection in Angular 1, where we would inject services we were using into the controller like this:

```javascript
.controller('ExampleCtrl', function($scope, $state, $myService) {
```

**Promises**

If you’ve used ngCordova then you will probably already be familiar with promises (a lot of JavaScript libraries implement promises) and why they are easier to use than callbacks. Promises provide a much nicer format for grabbing asynchronous data (e.g. data you need to wait for when you fetch something from a server or device), the current format of callbacks leads to ugly nested code that can become a nightmare to maintain.

ES6 adds native support for promises, which look like this:

```javascript
function msgAfterTimeout (msg, who, timeout) {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve(`${msg} Hello ${who}!`), timeout)
    })
}
msgAfterTimeout("", "Foo", 100).then((msg) =>
    msgAfterTimeout(msg, "Bar", 200)
).then((msg) => {
    console.log(`done after 300ms:${msg}`)
})

this.http.get(url).subscribe(
 (data) => {
 console.log(data);
 },
 (err) => {
 console.log(err);
 },
 () => {
 console.log("completed");
 }
);
```

rather than callbacks which can end up looking like this:

```javascript
function msgAfterTimeout (msg, who, timeout, onDone) {
    setTimeout(function () {
        onDone(msg + " Hello " + who + "!");
    }, timeout);
}
msgAfterTimeout("", "Foo", 100, function (msg) {
    msgAfterTimeout(msg, "Bar", 200, function (msg) {
        console.log("done after 300ms:" + msg);
    });
});
```

**Block Scoping**

Currently, if I define a variable in JavaScript it is available anywhere within the function that I defined it in. The new block scoping features in ES6 allow you to use the new let keyword to define a variable only within a single block of code like this:

```javascript
for (let i = 0; i < a.length; i++) {
    let x = a[i]
    //etc.
}
```
## Decorators
[Back to top](#concepts)  

[link : Decorators by Josh MORONY](https://www.joshmorony.com/building-mobile-apps-with-ionic-2/decorators-in-ionic2.html)


#### @Component

#### @Directive

The @Directive decorator allows you to create your own custom directives. Typically, the decorator would look something like this:

```javascript
@Directive({
    selector: '[my-selector]'
})
```

Then in your template you could use that selector to trigger the behaviour of the directive you have created by adding it to an element:
```xml
<some-element my-selector></some-element>
```
It might be a little confusing as to when to use @Component and @Directive, as they are both quite similar. The easiest thing to remember is that if you want to modify the behaviour of an existing component use a directive, if you want to create a completely new component use a component.

#### @Input

*View File*
```xml
<ion-content>
	<my-component [myText]="something"></my-component>
</ion-content>
```

*Controller File*
```javascript
@Component({
...
})
export class MyComponent {
	@Input('myText') textToUse;
	
	text: string;
	
	constructor() {
		this.text = "Hello";
	}
	
	ngAfterViewInit(){
		this.text = this.textToUse;
	}
}
```

#### @ViewChild - @ViewChildren

[link : ViewChild - ViewChildren](https://www.joshmorony.com/the-difference-between-viewchildren-and-contentchildren/)    

You can supply these with either a local variable or you can provide it with the class of the component you are attempting to grab. So, you could do something like this:

```@ViewChildren(Item, {static: false}) items;```

so that you could access all of the ```<ion-item>``` elements in your template through this.items

This would return the ```<ion-item>``` elements that are inside of the ```<ion-content>``` but it will also grab the ```<ion-item>``` elements that are inside of the ```<expandable-header>``` tags. This is an important distinction, because as you will see in the next section, the elements that are inside of the ```<expandable-header>``` custom component can also be grabbed with @ContentChildren, but only from the context of the ExpandableHeader component, not the HomePage component (remember, a page in Ionic is still just a normal component). I will elaborate on this in the next section, but in this case, I have added those elements directly to the HomePage template so I can grab them with @ViewChildren.

If I just wanted to grab the <ion-content> area, I could select it in a similar way to the way that I selected the Item‘s, but I could also use the #mycontent local variable that I added by doing this:

```@ViewChild('mycontent', {static: false}) contentArea;``` or just ```@ViewChild(Content) contentArea;```

Or you could do:

```@ViewChild('someVariable', {static: false}) something;```

to grab a reference to an element that you have set up a local template variable on, like this:

```<some-component #someVariable></some-component>```

**When to use @ViewChildren**

You should use @ViewChildren when you have added the element you are trying to grab directly to the component yourself, in other words, if the element you are grabbing is not added to the component you are working with through content projection with ```<ng-content>```. If you are working with pages in an Ionic application, then this will likely be the most common scenario, and unless you are building your own custom components then you likely would only need to use @ViewChild and @ViewChildren.

#### @Pipe

[link : Understanding Pipe](http://mcgivery.com/understanding-ionic-2-pipe/)

**@Pipe** allows you to create your own custom pipes to filter data that is displayed to the user, which can be very handy. The decorator might look something like this:
```javascript
@Pipe({
  name: 'myPipe'
})
```
which would then allow you to implement it in your templates like this:
```xml
<p>{{someString | myPipe}}</p>
`````
Now someString would be run through your custom myPipe before the value is output to the user.

## Pipe
[Back to top](#concepts)  

[link : Understanding Pipe](http://mcgivery.com/understanding-ionic-2-pipe/)      
[link : Josh Morony Pipe documentation](http://www.joshmorony.com/how-to-use-pipes-to-manipulate-data-in-ionic-2/)

### Generate pipe

```ionic g pipe pipes/my-custom-pipe```
```ng g pipe pipes/my-custom-pipe```

### Implement pipe

Here is a basic pipe

```javascript
import {Pipe, PipeTransform} from '@angular/core';

@Pipe({
	name: 'helloWorld'	// pipe name
})
export class HelloWorld implements PipeTransform{
	// Pipe function
	transform(value) {
		return "Hello " + value + "!";
	}
}
```

**IMPORTANT** The first letter of the pipe class name must be in upper case. But notice that during the usage, you must use lower case on the first letter.

**Create Pipe module**

To use your pipe in all pages, you need to create a *pipes.module.ts* file into the pipes directory

```
import { MyCustomPipe } from './my-custom-pipe';
import { NgModule } from '@angular/core';

@NgModule({
	declarations: [MyCustomPipe],
	imports: [],
	exports: [MyCustomPipe],
})

export class PipesModule {}
```

**Add declaration in app.module.ts**

```
declarations:...,
entryComponents: [],
imports: [],
providers: [PipesModule],
...
```

**Add declaration in each page.module.ts**

```
@NgModule({
	imports: [
		CommonModule,
		IonicModule,
		PipesModule,
		...
	]
})
```

**Usage**

*View file*

Pipe inline syntax : ```value | myPipe:args[0]:args[1]```

```xml
<ion-label>{{name | helloWorld}}</ion-label>
```

> Note: The first letter of the pipe's name in the view file is in lower case

### Other example

*Pipe file*

```javascript
import {Pipe} from '@angular/core';

@Component({
	name: 'addInt'
})
export class AddInt {
	transform(value, args) {
		return value + args[0];
	}
}
```

*Page file*

```javascript
@Component({
	templateUrl: 'myPage/myPage.html'
})
export class MyPage {
	constructor(){
		this.myIntArray = [1,3,7,15,31];
		this.mySingleInt = 40;
	}
}
```

*View file*

```
The meaning of life: {{mySingleInt | addInt:2}}

<ul>
<li *ngFor="let myInt of myIntArray">
{{myInt | addInt:1}}
</li>
</ul>
```


### Other example Date format pipe

*Pipe file* (app/pipe/datePipe.js)

```javascript
import {Pipe, PipeTransform} from '@angular/core';

@Pipe({
    name: 'datePipe'  // pipe name
})
/**
 * Formattage de la date en JJ/MM/AAAA
 */
export class datePipe implements PipeTransform{
    // Pipe function
    transform(value) {
        if(value.length == 10){
            return value.substring(8,10) + "/" + value.substring(5,7) + "/" + value.substring(0,4);
        } else {
            return value;
        }
    }
}
```
*View file*

```xml
...
<ion-label>{{c.dateConso | datePipe}}</ion-label>
...
```

### Pipe with parameters

```javascript
import {Pipe, PipeTransform} from '@angular/core';

@Pipe({
    name: 'truncatePipe'  // pipe name
})
export class truncatePipe implements PipeTransform{
    // Pipe function
    transform(value, fixed) {
        if(value){
            fixed = fixed || 3;
            return value.toFixed(fixed);
        }
    }
}
```

#### Conclusion

Pipes are very useful for string formatting like date, hour, regexp...

## RxJS operators
[Back to top](#concepts)  

[Understanding RxJS operators](https://www.digitalocean.com/community/tutorials/rxjs-operators-for-dummies-forkjoin-zip-combinelatest-withlatestfrom)    

*Starting code sample*
````
// 0. Import Rxjs operators
import { forkJoin, zip, combineLatest, Subject } from 'rxjs';
import { withLatestFrom, take, first } from 'rxjs/operators';

// 1. Define shirt color and logo options
type Color = 'white' | 'green' | 'red' | 'blue';
type Logo = 'fish' | 'dog' | 'bird' | 'cow';

// 2. Create the two persons - color and logo observables,
// They will communicate with us later (when we subscribe)
const color$ = new Subject<Color>();
const logo$ = new Subject<Logo>();

// 3. We are ready to start printing shirt. Need to subscribe to color and logo observables to produce shirts, we will write code here later
...

// 4. The two persons(observables) are doing their job, picking color and logo
color$.next('white');
logo$.next('fish');

color$.next('green');
logo$.next('dog');

color$.next('red');
logo$.next('bird');

color$.next('blue');

// 5. When the two persons(observables) has no more info, they said bye bye.. We will write code here later
...
````

### zip

zip : les observables sont inséparables, il n'y aura pas de résultat tant que tous les observables n'auront pas répondus. Tous les observables s'attendent.

````
zip(color$, logo$)
    .subscribe(([color, logo]) => console.log(`${color} shirt with ${logo}`));
	
// color$ est un observable (un Subject) qui retourne une couleur de type "color"
// logo$ est un observable qui retourne un logo de type "logo"	
````	

### combineLatest

combineLatest : les observables ne s'attendent pas après leur première exécution. Au premier appel, couleur ET logo s'attendent et exécutent le log. une fois fait 
la première fois, à chaque fois que la couleur OU le logo changera, le log sera déclenché même si un seul des 2 est modifié.

````
combineLatest(color$, logo$)
    .subscribe(([color, logo]) => console.log(`${color} shirt with ${logo}`));
````

### withLatestFrom

withLatestFrom : de type master - slave. Au début, le master doit rencontrer l'esclave. Après cela, le mater prendra les devants, donnant le commandement, 
l'action est déclenchée à chaque fois uniquement lorsque le maître renvoie une nouvelle valeur

````
color$.pipe(withLatestFrom(logo$))
    .subscribe(([color, logo]) => console.log(`${color} shirt with ${logo}`));
````

Au début (une seule fois), la couleur (master) recherchera le logo (esclave). Une fois que le logo (esclave) a répondu, la couleur (master) prendra la tête.
Le journal sera déclenché chaque fois que la valeur de couleur (principale) suivante est modifiée. Les modifications de la valeur du logo (esclave) ne déclenchent pas le journal de la console.

### forkJoin

forkJoin : ne déclenche uniquement lorsqu'il est certain que tous les observables ont répondus.
````
forkJoin(color$, logo$)
    .subscribe(([color, logo]) => console.log(`${color} shirt with ${logo}`));
````	
Aucun log ne sera déclenché dans ce cas étant donné que les observables color$ et logo$ ne sont jamais terminé (on appelle toujours un .next);

Pour les considérés comme terminés, il faut appeler :

````
// 5. When the two persons(observables) ...
color$.complete();
logo$.complete();
````

## Promise vs Observable
[Back to top](#concepts) 

[link : RxJS Observables vs Promises](https://egghead.io/lessons/rxjs-rxjs-observables-vs-promises)

> **Update : Observable changes** [See this topic on Observable changes](https://www.joshmorony.com/using-observables-in-ionic-3-9-x-and-angular-5/)    

Both Promises and Observables provide us with abstractions that help us deal with the asynchronous nature of our applications. However, there are important differences between the two:

- Observable can emit multiple values over time
- Observables can define both the setup and teardown aspects of asynchronous behavior.
- Observables are cancellable.
Moreover, Observables can be retried using one of the retry operators provided by the API, such as retry and retryWhen. On the other hand, 
- [Observable operators](https://gist.github.com/btroncone/d6cf141d6f2c00dc6b35#map)    
    
- Promises will only emit a value once
- Promises require the caller to have access to the original function that returned the promise in order to have a retry capability.
- Use *then* with promises and *subscribe* with Observables

<p align="center">
<img src="https://i.stack.imgur.com/Ewn3b.png" align="center" width="600">
</p>     


A promise resolves to a single value asynchronously, an observable resolves to (or emits) multiple values asynchronously (over time).

Concrete examples:

- Promise: Response from an Ajax call
- Observable: Click events


For example, **http.get** return Observable, so you **must** use **subscribe** instead of **then**

```javascript
this.http.get('location/of/data').map(res => res.json()).subscribe(data => {
    console.log(data);
});
```

The code below will not work 

```javascript
this.http.get('location/of/data').then((data) => {
    console.log(data);
});
```

*Example :* using Promise or Observable

```javascript
var promise = new Promise((resolve) => {
    setTimeout(() => {
        console.log("timeout hit");
        resolve(42);
    }, 500);
    console.log("promise started");
});

promise.then(x => console.log(x));


var source = Rx.Observable.create((observer) => {
    setTimeout(() => {
        observer.onNext(42);
    }, 500);
    console.log("observable started");
});

source.forEach(x => console.log(x));

Instead of Promise, Observable can be cancelled

In the case below, the observable method is cancelled about 500ms.

var source = Rx.Observable.create((observer) => {
    var id = setTimeout(() => {
        observer.onNext(42);
    }, 1000);
    console.log("observable started");

    return () => {
        console.log("dispose called");
        clearTimeout(id);
    }
});

var disposable = source.forEach(x => console.log(x));

setTimeout(() => {
    disposable.dispose();
}, 500);
```
The log output :

> "observable started"
> "dispose called"

If we change timeout value of 500 ms to 1500

> "observable started"
> "42"


### map operator

While *http.get* returns the result of the request in the form of an *Observable*, **map** converts the result into a **JSON decoded** version of the result, and **subscribe** allows us to access the data that is returned

### Examples of promises

*Provider file*

```javascript
  /**
   * Display prompt alert
   * @param {String} _args.title
   * @param {String} _args.message
   * @param {String} _args.inputPlaceholder
   * @param {String} _args.okButtonLabel
   * @param {String} _args.cancelButtonLabel
   */
  prompt(_args: any = {}){
    return new Promise((resolve,reject) => {
      this.alertCtrl.create({
        title: _args.title || "",
        message: _args.message || "",
        inputs: [
          {
            name: 'value',
            placeholder: _args.inputPlaceholder || "Your value"
          },
        ],
        buttons: [
          {
            text: _args.cancelButtonLabel || 'Cancel',
            handler: data => {
              reject(null);
            }
          },
          {
            text: _args.okButtonLabel || 'Ok',
            handler: data => {
              resolve(data.value)
            }
          }
        ]
    }).present();})
  }
```

*Controller file*

```javascript
this.myProvider.prompt({title: "Input value", message:"Enter your hexadecimal value"})
.then((data) => {
	console.log("Returned : " + data);
});
```
## Observable and operators
[Back to top](#concepts) 

### Resources 

[RxJS operators](https://www.learnrxjs.io/learn-rxjs/operators)     
[RxJS Best practices](https://blog.strongbrew.io/rxjs-best-practices-in-angular/)     

### Chaining observables

In this example, we "chain" a promise converted as an observable with an other observable.

Use case : The *requestApi* function return an observable which is the result of a http request. But, all of our http requests need a bearer token to secure the api. So, before sending the http request, we need to fetch the user's accessToken which is stored into the local storage. 
Fetching data from local storage is an asynchonous task and is performed by using a Promise.

The problem : Waiting the end of the promise before sending the http request.

The solution : Converting the promise and chain it with the http obsevable by using **switchMap** RxJS operator

````
// Manage HTTP Quesries
  requestApi({action, method = 'GET', datas = {}}:
  { action: string, method?: string, datas?: any}): Observable<any> {
    const methodWanted = method.toLowerCase();
    const urlToUse = this.url + action;
    let req: Observable<any>;// = null;

    return from(this.auth.getValidToken()).pipe(switchMap((res: TokenResponse) => {
      console.debug(res.accessToken);
      const httpOptions = {
        headers: new HttpHeaders({'Access-Control-Allow-Origin': '*', 'Content-Type': 'application/json',
        Authorization: 'Bearer ' + res.accessToken})
      };      
  
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
        case 'patch' :
          req = this.http.patch(urlToUse, datas, httpOptions);
          break;
        default:
          req = this.http.get(urlToUse + '?' + datas, httpOptions);
          break;
      }
      return req;
    }));
  }
````

*data.service.ts*

````
fetchNewPublications({filterUserQueryIds = [], start = 0, size = apiConfig.defaultLength, sortBy = apiConfig.SORT.idte, disableStats = true}:
  {filterUserQueryIds?: number[], start?: number, size?: number, sortBy?: string, disableStats?: boolean}): Observable<ServiceResult> {

    let parameters = `SortOrder=${sortBy}&StartIndex=${start}&Length=${size}`;
    parameters += filterUserQueryIds.length > 0 ? `&filterUserQueryIds=${filterUserQueryIds}` : '';

    return this.apiHelper.requestApi({
      action: apiConfig.services.scientificstudy.getNewScientificStudies,
      method: 'get', datas: parameters});
  }
````

*controller.ts*

````
ngOnInit(): void {
	this.dataService.fetchNewPublications()
	.subscribe((res) => {
		// some stuff here
	});
}
````

## BehaviorSubject
[Back to top](#concepts)  

[link : josh morony tutorial](https://www.joshmorony.com/using-behaviorsubject-to-handle-asynchronous-loading-in-ionic/)

BehaviorSubject is a type of Observable (ie : a stream of data that we can subscribe to like the observable returned from HTTP requests in Angular)

It will always return a value, even if no data has been emitted from its stream yet. And, when you subscribe to it, it will immediately return the last value that was emitted immediately (or the initial value if no data has been emitted yet)

*Example*

```
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs';
import { Storage } from '@ionic/storage';
import { SomeType } from '../interfaces/movie-category';

@Injectable({
  providedIn: 'root'
})
export class DataService {

    public myData: BehaviorSubject<SomeType[]> = new BehaviorSubject<SomeType[]>([]);	// subject initialization
    // public myData = new BehaviorSubject([]); // without custom type

    constructor(private storage: Storage) {  }

    load(): void {

        this.storage.get('myData').then((data) => {
            this.myData.next(data);	// update subject data with next()
        });
    }

    updateData(data): void {
        this.storage.set('myData', data);
        this.myData.next(data);	// update subject data with next()
    }
}
```

**Anytime we want to update that data we just need to call the** ```next()```

This will cause the BehaviorSubject to **emit the new value**, and **anything that is subscribed to it will be instantly notified**. 

Let’s say that we were relying on this to populate a list in Ionic:

```
ngOnInit(){
    this.myDataService.myData.subscribe((data) => {
        this.myListData = data;
    });
}

ngOnDestroy() {
	this.myDataService.myData.unsubscribe();
}
```

Initially, the list will be empty because it will just receive an empty array, but as soon as the load method completes myListData will be instantly updated with the new data.

## Arrow function
[Back to top](#concepts) 

[link : official documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

[link : other useful doc](https://basarat.gitbooks.io/typescript/content/docs/arrow-functions.html)

*definition* : An arrow function expression has a shorter syntax compared to function expressions and does not bind its own this, arguments, super, or new.target. Arrow functions are always anonymous. These function expressions are best suited for non-method functions and they can not be used as constructors.

*summarize* : 

- You don't need to keep typing function
- It lexically captures the meaning of **this**
- It lexically captures the meaning of arguments

*notation* :  

```javascript
() => {/*statement*/}

//With parameters
(param1, param2,...) => {/*statement*/}
```

### Usage

this has traditionally been a pain point in JavaScript. Fat arrows fix it by capturing the meaning of this from the surrounding context. So it is very useful in case of calling asynchronous callback after a window closing event.

use case : 

We want to refresh a list item window after adding a new item (via a "new item" modal)

*List.ts*

```javascript
...

// Refresh item list
refreshRouteList(){
	this.Db.getRoutesList().then((data)=> this.routesList = data);
}

//Open new item modal
newItem(){
	this.navCtrl.push(DetailPage,{callback:
		() => {this.refreshRouteList();}
	});
 }
 
 ...
```


*DetailPage.ts*

```javascript
...
ionViewDidLeave(){
	console.log("close page");
	this.callback();
}
...
```


## Await async functions
[Back to top](#concepts)  

[link : MDN definition](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Instructions/async_function)    
[link : asyncawait-with-angular](https://labs.encoded.io/2016/12/08/asyncawait-with-angular/)    

### Understand the concept of *async/await* asynchronous functions. 

Since ES6, you can use the keyword **async** on an asynchronous function (**not on Observable !**). Then the keyword **await** becomes available. 
When **await** is used as a prefix *on a promise*, the result of the expression is "paused" until the promise is resolved or rejected.

*example*

```javascript
async function myFunction(){
    await myPromise;
    return res;  // This line will be reached only when myPromise will be resolved / rejected
}
```

*full example 1*

```javascript
async function sendEmails(query) {
  const users = await getUsers(query);
  const emails = users.map(u => u.email);
  const sentP = emails.map(email => sendMail(email, "Good day"));
  return await Promise.all(sentP);
}

// WARNING : we can not use "await" outside a "async" function
// So we have to "wrap" our code around asynchronous function
async function main() {
  try {
    await sendEmails({ firstName: "Nicolas" });
    console.log("OK");
  } catch (e) {
    console.error("FAIL");
  }
}

main();
```

*Full example 2*

```javascript
function resolveAfter2Seconds(x) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x);
    }, 2000);
  });
}

// With add1() the second timeout is created when the first one is finished
async function add1(x) {
  const a = await resolveAfter2Seconds(20); 
  const b = await resolveAfter2Seconds(30); 
  return x + a + b;
}

add1(10).then(v => {
  console.log(v);  // display 60 after 4 seconds.
});

// --------------------------------------------------------

// With add2() the two timeout are created at the same time
async function add2(x) {
  const a = resolveAfter2Seconds(20); 
  const b = resolveAfter2Seconds(30); 
  return x + await a + await b;
}

add2(10).then(v => {
  console.log(v);  // display 60 after 2 seconds.
});
```

### Conclusion

Using *await/async* function is just syntactic sugar, it is not better or worst, it is a matter of personal preference.

## Resolver
[Back to top](#concepts)  

[Resolver tutorial](https://ionicthemes.com/tutorials/about/improved-ux-for-ionic-apps-with-skeleton-loading-screens?utm_campaign=Ionic%20Dev%20Newsletter&utm_source=hs_email&utm_medium=email&utm_content=72678826&_hsenc=p2ANqtz-9QWSeTR97KwevKMUcvzKEnRYTLjx5Ugy1aS6vxYqFQFWg8sqlfJy6xbewh6Gy_7omSyC_J4HmXrz50Ufwq09hsC-E_gOU2QJaK_tXVpq5z7bQQEP8&_hsmi=72678826)    


## Environments
[Back to top](#concepts)  

[Josh morony's tutorial](https://www.joshmorony.com/production-development-environment-variables-in-ionic-angular/)    

You can manage development and production environments with the following files available in your *environments* folder

### Declaration

*environment.prod.ts*

```
export const environment = {
	production: true,
	message: 'PROD MODE'
	// here some other stuff
};

export const SERVER_URL = 'https://my-server/api';
```

*environment.ts*

```
export const environment = {
	production: false,
	message: 'DEV MODE'
	// here some other stuff
};

export const SERVER_URL = 'http://localost:8080';
```

### Usage

Be careful of the import path, select the value from environment.ts **not** environment.prod.ts 

*home.ts*

```
import { environment, SERVER_URL } from '../../environments/environment';
...

ngOnInit() {
	console.log('Env mode : ' + environment.message);
	console.log('Env URL : ' + SERVER_URL);
	
}
```

If you take a look at the angular.json file in your project, you will find the following configuration:
````
"production": {
  "fileReplacements": [
    {
      "replace": "src/environments/environment.ts",
      "with": "src/environments/environment.prod.ts"
    }
  ],
````

The production build configuration is set up to replace the environment.ts file with environment.prod.ts

## Function declaration

Best practice to declare function with multiple parameters

````
async saveUserQuery(
{ mode = Cst.CRUD_MODE.create, searchTerm = '', activeFilters = '', filtersSelected, callback = () => {}} :
{mode?: number, searchTerm?: string, activeFilters?: string, filtersSelected?: any, callback?: () => void }) { ... }
````
