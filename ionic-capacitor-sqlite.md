[< Back](https://github.com/gsoulie/ionic2-resources/edit/master/ionic-capacitor.md)    

# Building an SQLite Ionic App with Capacitor

[Simon grimm tutorial](https://www.youtube.com/watch?v=2kTT3k8ztL8&ab_channel=SimonGrimm)     
[Simon grimm code](https://devdactic.com/sqlite-ionic-app-with-capacitor/)   

## Install the Capacitor plugin
````npm install @capacitor-community/sqlite````

## Add sqlite plugin into Android MainActivity

````java
package io.ionic.starter;
import com.getcapacitor.community.database.sqlite.CapacitorSQLite;
 
import android.os.Bundle;
 
import com.getcapacitor.BridgeActivity;
import com.getcapacitor.Plugin;
 
import java.util.ArrayList;
 
public class MainActivity extends BridgeActivity {
  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
 
    // Initializes the Bridge
    this.init(savedInstanceState, new ArrayList<Class<? extends Plugin>>() {{
      // Additional plugins you've installed go here
      add(CapacitorSQLite.class);
    }});
  }
}
````

## Preparing a Database Export as JSON

The cool feature of the plugin is that it allows you to import a JSON dump of your data. There are also Cordova plugins that allow a SQL import, but since your database is usually behind an API anyway you could create this on the server and host the file for seeding your apps database somewhere.

The file specifies some general information like the name, and then the tables which can also directly hold some values. In our case we define two tables called vendors and products, where each product has the vendorid as foreign key.

When you want to sync your tables later you also need to include a last_modified column, but more on the whole https://github.com/capacitor-community/sqlite/blob/master/docs/ImportExportJson.md


I’ve hosted the following file for testing here: https://devdactic.fra1.digitaloceanspaces.com/tutorial/db.json

json : 
````json
{
  "database": "product-db",
  "version": 1,
  "encrypted": false,
  "mode": "full",
  "tables": [
    {
      "name": "vendors",
      "schema": [
        { "column": "id", "value": "INTEGER PRIMARY KEY NOT NULL" },
        { "column": "company_name", "value": "TEXT NOT NULL" },
        { "column": "company_info", "value": "TEXT NOT NULL" },
        { "column": "last_modified", "value": "INTEGER DEFAULT (strftime('%s', 'now'))" }
      ],
      "values": [
        [1, "Devdactic", "The main blog of Simon Grimm", 1587310030],
        [2, "Ionic Academy", "The online school to learn Ionic", 1590388125],
        [3, "Ionic Company", "Your favourite cross platform framework", 1590383895]
      ]
    },
    {
      "name": "products",
      "schema": [
        { "column": "id", "value": "INTEGER PRIMARY KEY NOT NULL" },
        { "column": "name", "value": "TEXT NOT NULL" },
        { "column": "currency", "value": "TEXT" },
        { "column": "value", "value": "INTEGER" },
        { "column": "vendorid", "value": "INTEGER" },
        { "column": "last_modified", "value": "INTEGER DEFAULT (strftime('%s', 'now'))" },
        {
          "foreignkey": "vendorid",
          "value": "REFERENCES vendors(id)"
        }
      ],
      "values": [
        [1, "Devdactic Fan Hat", "EUR", 9, 1, 1604396241],
        [2, "Ionic Academy Membership", "USD", 25, 2, 1604296241],
        [3, "Ionic Sticker Swag", "USD", 4, 3, 1594196241],
        [4, "Practical Ionic Book", "USD", 79, 1, 1603396241]
      ]
    }
  ]
}
````

## Building a Database Service

Before you can actually use the database, you should check whether you are running on Android, since you need to request permissions in that case!

*database.service.ts*

````typescript
import { Injectable } from '@angular/core';
import { Plugins } from '@capacitor/core';
import '@capacitor-community/sqlite';
import { AlertController } from '@ionic/angular';
import { HttpClient } from '@angular/common/http';
import { BehaviorSubject, from, of } from 'rxjs';
import { switchMap } from 'rxjs/operators';
 
import { JsonSQLite } from '@capacitor-community/sqlite';
const { CapacitorSQLite, Device, Storage } = Plugins;
 
const DB_SETUP_KEY = 'first_db_setup';
const DB_NAME_KEY = 'db_name';
 
@Injectable({
  providedIn: 'root'
})
export class DatabaseService {
  dbReady = new BehaviorSubject(false);
  dbName = '';
 
  constructor(private http: HttpClient, private alertCtrl: AlertController) { }
 
  async init(): Promise<void> {
    const info = await Device.getInfo();
 
    if (info.platform === 'android') {
      try {
        const sqlite = CapacitorSQLite as any;
        await sqlite.requestPermissions();
        this.setupDatabase();
      } catch (e) {
        const alert = await this.alertCtrl.create({
          header: 'No DB access',
          message: 'This app can\'t work without Database access.',
          buttons: ['OK']
        });
        await alert.present();
      }
    } else {
      this.setupDatabase();
    }
  }
 
  private async setupDatabase() {
    const dbSetupDone = await Storage.get({ key: DB_SETUP_KEY });
 
    if (!dbSetupDone.value) {
      this.downloadDatabase();
    } else {
      this.dbName = (await Storage.get({ key: DB_NAME_KEY })).value;
      await CapacitorSQLite.open({ database: this.dbName });
      this.dbReady.next(true);
    }
  }
 
  // Potentially build this out to an update logic:
  // Sync your data on every app start and update the device DB
  private downloadDatabase(update = false) {
    this.http.get('https://devdactic.fra1.digitaloceanspaces.com/tutorial/db.json').subscribe(async (jsonExport: JsonSQLite) => {
      const jsonstring = JSON.stringify(jsonExport);
      const isValid = await CapacitorSQLite.isJsonValid({ jsonstring });
 
      if (isValid.result) {
        this.dbName = jsonExport.database;
        await Storage.set({ key: DB_NAME_KEY, value: this.dbName });
        await CapacitorSQLite.importFromJson({ jsonstring });
        await Storage.set({ key: DB_SETUP_KEY, value: '1' });
 
        // Your potential logic to detect offline changes later
        if (!update) {
          await CapacitorSQLite.createSyncTable();
        } else {
          await CapacitorSQLite.setSyncDate({ syncdate: '' + new Date().getTime() })
        }
        this.dbReady.next(true);
      }
    });
  }
  
  getProductList() {
      return this.dbReady.pipe(
	switchMap(isReady => {
	  if (!isReady) {
		return of({ values: [] });
	  } else {
		const statement = 'SELECT * FROM products;';
		return from(CapacitorSQLite.query({ statement, values: [] }));
	  }
	})
       )
   }
	 
   async getProductById(id) {
       const statement = `SELECT * FROM products LEFT JOIN vendors ON vendors.id=products.vendorid WHERE products.id=${id} ;`;
       return (await CapacitorSQLite.query({ statement, values: [] })).values[0];
   }

   getDatabaseExport(mode) {
       return CapacitorSQLite.exportToJson({ jsonexportmode: mode });
   }

   addDummyProduct(name) {
       const randomValue = Math.floor(Math.random() * 100) + 1;
       const randomVendor = Math.floor(Math.random() * 3) + 1
       const statement = `INSERT INTO products (name, currency, value, vendorid) VALUES ('${name}','EUR', ${randomValue}, ${randomVendor});`;
       return CapacitorSQLite.execute({ statements: statement });
   }

   deleteProduct(productId) {
       const statement = `DELETE FROM products WHERE id = ${productId};`;
       return CapacitorSQLite.execute({ statements: statement });
   }

   // For testing only..
   async deleteDatabase() {
       const dbName = await Storage.get({ key: DB_NAME_KEY });
       await Storage.set({ key: DB_SETUP_KEY, value: null });
       return CapacitorSQLite.deleteDatabase({ database: dbName.value });
   }
}
````

Note: As a reader pointed out, it might be required to call this to open the database additionally:

````typescript await CapacitorSQLite.createConnection({ database: DB_NAME })````

## Initialize database

*app.component.ts*

````typescrpt
import { Component } from '@angular/core';
import { LoadingController, Platform } from '@ionic/angular';
import { SplashScreen } from '@ionic-native/splash-screen/ngx';
import { StatusBar } from '@ionic-native/status-bar/ngx';
import { DatabaseService } from './services/database.service';
 
@Component({
  selector: 'app-root',
  templateUrl: 'app.component.html',
  styleUrls: ['app.component.scss']
})
export class AppComponent {
  constructor(
    private platform: Platform,
    private splashScreen: SplashScreen,
    private statusBar: StatusBar,
    private databaseService: DatabaseService,
    private loadingCtrl: LoadingController
  ) {
    this.initializeApp();
  }
 
  async initializeApp() {
    this.platform.ready().then(async () => {
      const loading = await this.loadingCtrl.create();
      await loading.present();
      this.databaseService.init();
      this.databaseService.dbReady.subscribe(isReady => {
        if (isReady) {
          loading.dismiss();
          this.statusBar.styleDefault();
          this.splashScreen.hide();
        }
      });
    });
  }
} 
````

> IMPORTANT : I’ve noticed that the first page loads anyway already, so you could even add a guard to your page to prevent access until the database is ready, which would be the best solution I guess.

## Building the SQLite Ionic APP Logic

*home.page.ts*

````typescript
import { Component } from '@angular/core';
import { DatabaseService } from '../services/database.service';
 
@Component({
  selector: 'app-home',
  templateUrl: 'home.page.html',
  styleUrls: ['home.page.scss'],
})
export class HomePage {
  products = [];
  export = null;
  newProduct = 'My cool product';
 
  constructor(private databaseService: DatabaseService) {
    this.loadProducts();
  }
 
  loadProducts() {
    this.databaseService.getProductList().subscribe(res => {
      this.products = res.values;
    });
  }
 
  // Mode is either "partial" or "full"
  async createExport(mode) {
    const dataExport = await this.databaseService.getDatabaseExport(mode);
    this.export = dataExport.export;
  }
 
  async addProduct() {
    await this.databaseService.addDummyProduct(this.newProduct);
    this.newProduct = '';
    this.loadProducts();
  }
 
  async deleteProduct(product) {    
    await this.databaseService.deleteProduct(product.id);
    this.products = this.products.filter(p => p != product);    
  }
 
  // For testing..
  deleteDatabase() {
    this.databaseService.deleteDatabase();
  }
}
````

*home.page.html*

````html
<ion-header>
  <ion-toolbar color="primary">
    <ion-title>
      My SQL App
    </ion-title>
  </ion-toolbar>
</ion-header>
 
<ion-content>
 
  <ion-item>
    <ion-input [(ngModel)]="newProduct"></ion-input>
  </ion-item>
  <ion-button expand="full" (click)="addProduct()">Add dummy product</ion-button>
 
  <ion-list>
    <ion-list>
      <ion-item-sliding *ngFor="let p of products">
        <ion-item [routerLink]="['product', p.id]">
          <ion-label>
            {{ p.name }}
            <p>{{ p.value | currency:p.currency }}</p>
          </ion-label>
        </ion-item>
        <ion-item-options side="end">
          <ion-item-option (click)="deleteProduct(p)" color="danger">Delete</ion-item-option>
        </ion-item-options>
      </ion-item-sliding>
    </ion-list>
 
  </ion-list>
 
  <ion-button expand="full" (click)="createExport('full')">Create Full JSON Export</ion-button>
  <ion-button expand="full" (click)="createExport('partial')">Create Partial JSON Export</ion-button>
  <ion-button expand="full" (click)="deleteDatabase()" color="danger">Delete Database</ion-button>
 
  <ion-card *ngIf="export">
    <ion-card-content>
      {{ export | json}}
    </ion-card-content>
  </ion-card>
 
</ion-content>
````


run with : ````ionic cap run ios --livereload --external````

## Debugging

see debugging section of the following tutorial : https://devdactic.com/ionic-4-sqlite-queries/

## IMPORTANT

Note: The Capacitor SQLite plugin will add a specific name (SQLite.db) to your database file, so if your database name is “product-db” (like in my JSON), the file on your device would be called “product-dbSQLite.db”.

Keep that in mind especially for the Android DB which you directly extract from the device using the ADB shell.
