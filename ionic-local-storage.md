[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Local Storage

* [Offline storage](#offline-storage)       
* [Cordova](#cordova)  
* [Capacitor](#capacitor)    

## Offline Storage
[Back to top](#local-storage) 

How you save data locally is another important consideration. There are several solutions: localStorage, IndexedDB, and Ionic Offline Storage.

### localStorage

localStorage contains built-in methods to create and store key -> value pairs. These are persisted offline and when a browser is closed. You can declare and retrieve data using setItem and getItem. The following code sets a ‘name’ value and returns it when called.

````
var offlineStore = window.localStorage;
offlineStore.setItem('name', 'John');
var name = localStorage.getItem('name');
````

Use the clear method to delete data.

````
localStorage.clear('name');
````

### IndexedDB
[Back to top](#local-storage) 

localStorage is great for storing strings, but what if you want to store more complex data like images or blobs? That’s where IndexedDB comes in. IndexedDB lets you create a new local database connection and map objects to the schema you provide. It requires more code than localStorage but you can take advantage of more features.

When creating an IndexedDB you create a “store” for your data and IndexedDB lets you go from there. Here’s an example of creating an IndexedDB of books and their authors:

````
// creates a new version of the database if needed
const dbName = "books_db";
let request = window.indexedDB.open(dbName, 1),db,tx,store, index;

request.onupgradeneeded = function() {
  let db = request.result,
  store = db.createObjectStore("booksStore", { keyPath: "isbn" }), 
  index = store.createIndex("title", "title");
} 

request.onsuccess = function() {
  // db where data goes
  db = request.result;

  // defines the transactions allowed
  tx = db.transaction("booksStore", "readwrite");

  // the store you create for the data that uses the 'isbn' key.
  store = tx.objectStore("booksStore");

  // an index you can use to search data
  index = store.index("title"); 

  // insert data
  store.put({isbn: 1234, title: "Moby Dick", author: "Herman Melville"});
  store.put({isbn: 4321, title: "Emma", author: "Jane Austen"}); 

  // retrieve data
  let book1 = store.get(1234);
  book1.onsuccess = function() {
    console.log(book1.result);
  } 

  // close the database connection once the transaction has completed
  tx.oncomplete = function() {
    db.close();
  };
};
````

You can learn more by reading the IndexedDB docs. There are other solutions like localForage which is a wrapper API for IndexedDB that aims to make it easier to work with.

### Ionic’s Offline Storage solution
[Back to top](#local-storage) 

For more advanced, robust data storage needs, check out Ionic’s Offline Storage solution. It’s a cross-platform data storage system that works on iOS and Android and powered by SQLite (a SQL database engine). Since it provides a performance-optimized query engine and on-device data encryption (256-bit AES), it’s great for data-driven apps.

Since it’s based on the industry-standard SQL, it’s easy for developers to add to their project. For example, creating a new table then inserting initial data:

````
this.database.transaction((tx) => {
  tx.executeSql('CREATE TABLE IF NOT EXISTS software (name, company)');
  tx.executeSql('INSERT INTO software VALUES (?,?)', ['offline', "ionic"]);
  tx.executeSql('INSERT INTO software VALUES (?,?)', ['auth-connect', "ionic"]);
});
Querying data involves writing SELECT statements then looping through the data results:

this.database.transaction((tx) => {
  tx.executeSql("SELECT * from software", [], (tx, result) => {
    // Rows is an array of results. Use zero-based indexing to access
    // each element in the result set: item(0), item(1), etc. 
    for (let i = 0; i < result.rows.length; i++) {
      // { name: "offline-storage", company: "ionic", type: "native", version: "2.0" }
      console.log(result.rows.item(i));

      // ionic
      console.log(result.rows.item(i).company);
    }
  });
});
````

## Cordova
[Back to top](#local-storage)  

Cordova use *Storage* plugin

*Exemple first launch detection*

```
import { Storage } from '@ionic/storage';

export class AppComponent {
  constructor(
    private platform: Platform,
    private splashScreen: SplashScreen,
    private statusBar: StatusBar,
    private storage: Storage,
    private router: Router
  ) {
    this.initializeApp();
  }

  initializeApp() {
    this.platform.ready().then(() => {
      this.storage.get('first_launch').then((val) => {
        if (val !== null) {
          // not first launch
          this.router.navigateByUrl('/home');
        } else {
          // first launch
          this.storage.set('first_launch', 'done');
          this.router.navigateByUrl('/tutorial');
        }
     });

      this.statusBar.styleDefault();
      this.splashScreen.hide();
    });
  }
}
```


## Capacitor
[Back to top](#local-storage) 

*Same example as above*

```
import { Plugins } from '@capacitor/core';
import { Router } from '@angular/router';
const { Storage } = Plugins;

export class AppComponent {
  constructor(
    private platform: Platform,
    private splashScreen: SplashScreen,
    private statusBar: StatusBar,
    private router: Router
  ) {
    this.initializeApp();
  }

  initializeApp() {
    this.platform.ready().then(() => {
      Storage.get({key: 'first_launch'}).then((val) => {
        if (val.value !== null) {
          // not first launch
          this.router.navigateByUrl('/home');
        } else {
          // first launch
          Storage.set({key: 'first_launch',
          value: 'done'});
          this.router.navigateByUrl('/tutorial');
        }
     });

      this.statusBar.styleDefault();
      this.splashScreen.hide();
    });
  }
}
```

[Back to top](#local-storage) 
