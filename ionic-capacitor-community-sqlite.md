[< Back](https://github.com/gsoulie/ionic2-resources/blob/master/ionic-capacitor.md#sqlite-ionic-app-with-capacitor)    

# Ionic Capacitor Community sqlite working example

## Source

https://github.com/capacitor-community/sqlite/blob/master/docs/Ionic-Angular-Usage.md       
permission issue : https://github.com/capacitor-community/sqlite/issues/55      

## Installation

````
npm install @capacitor-community/sqlite
npm run build
npx cap sync
npx cap add android
npx cap add ios
````

## sqlite service

*sqlite.service.ts*

````typescript
import { Injectable } from '@angular/core';
import { Capacitor } from '@capacitor/core';
import { CapacitorSQLite, SQLiteDBConnection, SQLiteConnection, capSQLiteSet,
         capSQLiteChanges, capSQLiteValues, capEchoResult, capSQLiteResult
        } from '@capacitor-community/sqlite';

@Injectable()

export class SQLiteService {
    sqlite: SQLiteConnection;
    isService = false;
    platform: string;
    handlerPermissions: any;

    constructor() {
    }
    /**
     * Plugin Initialization
     */
    initializePlugin(): Promise<boolean> {
        return new Promise (resolve => {
            this.platform = Capacitor.getPlatform();
            console.log('*** platform ' + this.platform);
            const sqlitePlugin: any = CapacitorSQLite;

            // Check for android permission
            if (this.platform === 'android') {
              console.log('>>>>>>>>>>>>>>>>>>>> PERMISSION ANDROID <<<<<<<<<<<<<<<<<<<');
              const permission = CapacitorSQLite as any;  // obligatoire sinon la ligne addListener sera en erreur
              this.handlerPermissions = permission.addListener(
                'androidPermissionsRequest', async (data: any) => {
                  console.log('>>>>>>>>>>>>>>>>>>>> PERMISSION ANDROID Granted : ' + data.permissionGranted);
                if (data.permissionGranted === 1) {
                    this.handlerPermissions.remove();
                    resolve(true);
                } else {
                    console.log('Permission not granted');
                    this.handlerPermissions.remove();
                    this.sqlite = null;
                    resolve(false);
                }
              });
            }
            this.sqlite = new SQLiteConnection(sqlitePlugin);
            this.isService = true;
            console.log('$$$ in service this.isService ' + this.isService + ' $$$');
            resolve(true);
        });
    }
    /**
     * Echo a value
     * @param value
     */
    async echo(value: string): Promise<capEchoResult> {
        console.log('&&&& in echo this.sqlite ' + this.sqlite + ' &&&&');
        if(this.sqlite != null) {
            return await this.sqlite.echo(value);
        } else {
            return null;
        }
    }
    /**
     * addUpgradeStatement
     * @param database
     * @param fromVersion
     * @param toVersion
     * @param statement
     * @param set
     */
    async addUpgradeStatement(database: string, fromVersion: number,
                              toVersion: number, statement: string,
                              set?: capSQLiteSet[]): Promise<void> {
        if(this.sqlite != null) {
            try {
                await this.sqlite.addUpgradeStatement(database, fromVersion, toVersion,
                                                      statement, set ? set : []);
                return Promise.resolve();
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open for ${database}`));
        }
    }
    /**
     * Create a connection to a database
     * @param database
     * @param encrypted
     * @param mode
     * @param version
     */
    async createConnection(database: string, encrypted: boolean,
                           mode: string, version: number
                           ): Promise<SQLiteDBConnection> {
      if(this.sqlite != null) {
        try {
          const db: SQLiteDBConnection = await this.sqlite
          .createConnection(database, encrypted, mode, version);
          if (db != null) {
              return Promise.resolve(db);
          } else {
              return Promise.reject(new Error(`no db returned is null`));
          }
        } catch (err) {
          return Promise.reject(new Error(err));
        }
      } else {
        return Promise.reject(new Error(`no connection open for ${database}`));
      }
    }
    /**
     * Close a connection to a database
     * @param database
     */
    async closeConnection(database:string): Promise<void> {
        if(this.sqlite != null) {
            try {
                await this.sqlite.closeConnection(database);
                return Promise.resolve();
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open for ${database}`));
        }
    }
    /**
     * Retrieve an existing connection to a database
     * @param database
     */
    async retrieveConnection(database: string):
            Promise<SQLiteDBConnection> {
        if(this.sqlite != null) {
            try {
                return Promise.resolve(await this.sqlite.retrieveConnection(database));
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open for ${database}`));
        }
    }
    /**
     * Retrieve all existing connections
     */
    async retrieveAllConnections():
                    Promise<Map<string, SQLiteDBConnection>> {
        if(this.sqlite != null) {
            try {
                const myConns =  await this.sqlite.retrieveAllConnections();
                const keys = [...myConns.keys()];
                keys.forEach( (value) => {
                    console.log('Connection: ' + value);
                });
                return Promise.resolve(myConns);
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open`));
        }
    }
    /**
     * Close all existing connections
     */
    async closeAllConnections(): Promise<void> {
        if(this.sqlite != null) {
            try {
                return Promise.resolve(await this.sqlite.closeAllConnections());
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open`));
        }
    }
    /**
     * Check if connection exists
     * @param database
     */
     async isConnection(database: string): Promise<capSQLiteResult> {
        if(this.sqlite != null) {
            try {
                return Promise.resolve(await this.sqlite.isConnection(database));
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open`));
        }
    }
    /**
     * Check Connections Consistency
     * @returns
     */
    async checkConnectionsConsistency(): Promise<any> {
        if(this.sqlite != null) {
            try {
                console.log(`in Service checkConnectionsConsistency`);
                return Promise.resolve(await this.sqlite.checkConnectionsConsistency());
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open`));
        }
    }
    /**
     * Check if database exists
     * @param database
     */
    async isDatabase(database: string): Promise<capSQLiteResult> {
        if(this.sqlite != null) {
            try {
                return Promise.resolve(await this.sqlite.isDatabase(database));
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open`));
        }
    }
    /**
     * Get the list of databases
     */
    async getDatabaseList() : Promise<capSQLiteValues> {
        if(this.sqlite != null) {
            try {
                return Promise.resolve(await this.sqlite.getDatabaseList());
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open`));
        }
    }
    /**
     * Add "SQLite" suffix to old database's names
     */
    async addSQLiteSuffix(folderPath?: string): Promise<void>{
        if(this.sqlite != null) {
            try {
                const path: string = folderPath ? folderPath : 'default';
                console.log(`in service path: ${path} `);
                return Promise.resolve(await this.sqlite.addSQLiteSuffix(folderPath));
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open`));
        }
    }
    /**
     * Delete old databases
     */
    async deleteOldDatabases(folderPath?: string): Promise<void>{
        if(this.sqlite != null) {
            try {
                const path: string = folderPath ? folderPath : 'default';
                console.log(`in service path: ${path} `);
                return Promise.resolve(await this.sqlite.deleteOldDatabases(folderPath));
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open`));
        }
    }

    /**
     * Import from a Json Object
     * @param jsonstring
     */
    async importFromJson(jsonstring:string): Promise<capSQLiteChanges> {
        if(this.sqlite != null) {
            try {
                return Promise.resolve(await this.sqlite.importFromJson(jsonstring));
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open`));
        }

    }
    /**
     * Is Json Object Valid
     * @param jsonstring Check the validity of a given Json Object
     */
    async isJsonValid(jsonstring:string): Promise<capSQLiteResult> {
        if(this.sqlite != null) {
            try {
                return Promise.resolve(await this.sqlite.isJsonValid(jsonstring));
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open`));
        }

    }
    /**
     * Copy databases from public/assets/databases folder to application databases folder
     */
    async copyFromAssets(): Promise<void> {
        if (this.sqlite != null) {
            try {
                return Promise.resolve(await this.sqlite.copyFromAssets());
            } catch (err) {
                return Promise.reject(new Error(err));
            }
        } else {
            return Promise.reject(new Error(`no connection open`));
        }
      }
}
````

## User model

````typescript
export class User {
  constructor(
    public id?: number,
    public name?: string,
    public email?: string,
    public age?: number,
    public company?: string,
    public size?: number) {}
}
````

## sqlite api service

*db-config.ts*

````typescript
export const dbConfig = {
  dbName: 'testEncryption',
  encrypted: false,
  mode: 'no-encryption',
  version: 1
};

export const dbConfigWithEncryption = {
  dbName: 'testEncryption',
  encrypted: true,
  mode: 'encryption',
  version: 1
};
````

*sqlite-api.service.ts*

````typescript
import { BehaviorSubject } from 'rxjs';
import { twoUsers } from './../utils/no-encryption-utils';
import { dbConfig } from './sqlite/db-config';
import { capSQLiteChanges, SQLiteDBConnection } from '@capacitor-community/sqlite';
import { SQLiteService } from './sqlite/sqlite.service';
import { Injectable } from '@angular/core';
import { createSchema } from '../utils/no-encryption-utils';
import { User } from '../models/user.model';
@Injectable({
  providedIn: 'root'
})
export class SqlLiteApiService {

  users$: BehaviorSubject<User[]> = new BehaviorSubject([]);
  private db: SQLiteDBConnection;


  constructor(
    private sqliteService: SQLiteService) { }

  get dbConnection() { return this.db; }

  async initializeDBConnection(): Promise<void> {

    try {
      // initialize the connection
      this.db = await this.sqliteService
              .createConnection(dbConfig.dbName, dbConfig.encrypted, dbConfig.mode, dbConfig.version);

      // open db testEncryption
      await this.db.open();
      await this.initializeTableFromSchema();
    } catch (err) {
      return Promise.reject(err);
    }
  }

  async initializeTableFromSchema(): Promise<void> {
    try {
      // create tables in db
      console.log('$$$ create schema... ');
      let ret: any = await this.db.execute(createSchema);
      console.log('$$$ ret.changes.changes in db ' + ret.changes.changes);

      if (ret.changes.changes < 0) {
        return Promise.reject(new Error('Execute createSchema failed'));
      }

      // create synchronization table
      ret = await this.db.createSyncTable();

      if (ret.changes.changes < 0) {
        return Promise.reject(new Error('Execute createSyncTable failed'));
      }

      // set the synchronization date
      const syncDate = '2020-11-25T08:30:25.000Z';
      await this.db.setSyncDate(syncDate);

    } catch (err) {
      return Promise.reject(err);
    }
  }

  async populateMockData() {
    // add two users in db
    const ret = await this.db.execute(twoUsers);
    console.log('$$$ ret ' + JSON.stringify(ret));
    console.log('$$$ ret.changes = ' + ret.changes.changes);
  }

  async fetchUsers(): Promise<User[]> {
    const ret = await this.db.query('SELECT * FROM users;');
    console.log('$$$ ret query ' + JSON.stringify(ret));
    console.log('$$$ ret query length ' + ret.values.length);
    this.users$.next(ret.values);
    return ret.values as User[];
  }

  async fetchUsersWithNoCompany(): Promise<User[]> {
    // select users where company is NULL in db
    const ret = await this.db.query('SELECT * FROM users WHERE company IS NULL;');
    if(ret.values.length !== 2 || ret.values[0].name !== 'Whiteley' ||
                                  ret.values[1].name !== 'Jones') {
      return Promise.reject(new Error('Query2 Users where Company null failed'));
    }
    this.users$.next(ret.values);
    return ret.values as User[];
  }

  async fetchUserById(id): Promise<User> {
    const ret = await this.db.query(`SELECT * FROM users WHERE id = ${id};`);
    console.log('$$$ ret query ' + JSON.stringify(ret));
    console.log('$$$ ret query length ' + ret.values.length);

    if(ret.values.length <= 0) {
      console.log('$$$ ret fetchUserById Error ');
      return Promise.reject(new Error(`fetchUserById where id=${id} failed`));
    }
    return ret.values[0] as User;
  }

  async addUserWithStatementAndValues(user: User): Promise<capSQLiteChanges> {
    console.log('=========>>>> Add user ' + JSON.stringify(user));
    // add one user with statement and values
    const sqlcmd = 'INSERT INTO users (name,email,age) VALUES (?,?,?)';
    const values: Array<any>  = [user.name,user.email,user.age];

    /*
    // Ne peut pas fonctionner car nous n'avons pas l'id du nouveau user
    // il faudrait faire une requête select avec les infos qu'on a pour récupérer l'user entier
    let items = this.users$.getValue(); // récupère les dernières valeurs connues
		items.push(user);
		this.users$.next(items);*/

    return await this.db.run(sqlcmd,values);
  }

  async addUserWithStatement(user: User): Promise<capSQLiteChanges> {
    // add one user with statement
    const sqlcmd = `INSERT INTO users (name,email,age) VALUES ` +
    `(${user.name},${user.email},${user.age})`;
    return await this.db.run(sqlcmd);
  }

  async updateUser(user: User): Promise<capSQLiteChanges> {

    const sqlcmd = `UPDATE users SET
    email = '${user.email}',
    name = '${user.name}',
    company = '${user.company}',
    size = ${user.size},
    age = ${user.age}
    WHERE id = ${user.id}`;
    const us = this.users$.getValue();
    const index = us.findIndex(findUser => findUser.id === user.id);
    if (index >= 0) {
      us[index] = user;
      this.users$.next(us);
    }
    return await this.db.run(sqlcmd);
  }

  async deleteUser(user: User): Promise<capSQLiteChanges> {
    const sqlcmd = `DELETE FROM users WHERE id = ${user.id};`;

    let items = this.users$.getValue();
		items = items.filter(i => i !== user);
		this.users$.next(items);

    return await this.db.run(sqlcmd);
  }

  async deleteAllUsers(): Promise<capSQLiteChanges> {
    const sqlcmd = `DELETE FROM users`;

    this.users$.next([]);
    return await this.db.execute(sqlcmd);
  }

  async closeConnection(): Promise<void> {
    await this.sqliteService.closeConnection('testEncryption');
  }
}

````

*no-encryption-utils.ts*

````typescript
import { capSQLiteSet } from '@capacitor-community/sqlite';
export const createSchema = `
CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY NOT NULL,
    email TEXT UNIQUE NOT NULL,
    name TEXT,
    company TEXT,
    size INTEGER,
    age INTEGER,
    last_modified INTEGER DEFAULT (strftime('%s', 'now'))
);
CREATE TABLE IF NOT EXISTS messages (
  id INTEGER PRIMARY KEY NOT NULL,
  userid INTEGER,
  title TEXT NOT NULL,
  body TEXT NOT NULL,
  last_modified INTEGER DEFAULT (strftime('%s', 'now')),
  FOREIGN KEY (userid) REFERENCES users(id) ON DELETE SET DEFAULT
);
CREATE TABLE IF NOT EXISTS images (
  id INTEGER PRIMARY KEY NOT NULL,
  name TEXT UNIQUE NOT NULL,
  type TEXT NOT NULL,
  size INTEGER,
  img BLOB,
  last_modified INTEGER DEFAULT (strftime('%s', 'now'))
);
CREATE TABLE IF NOT EXISTS test56 (
  id INTEGER PRIMARY KEY NOT NULL,
  name TEXT,
  name1 TEXT
);
CREATE INDEX IF NOT EXISTS users_index_name ON users (name);
CREATE INDEX IF NOT EXISTS users_index_last_modified ON users (last_modified);
CREATE INDEX IF NOT EXISTS messages_index_name ON messages (title);
CREATE INDEX IF NOT EXISTS messages_index_last_modified ON messages (last_modified);
CREATE INDEX IF NOT EXISTS images_index_name ON images (name);
CREATE INDEX IF NOT EXISTS images_index_last_modified ON images (last_modified);
CREATE TRIGGER IF NOT EXISTS users_trigger_last_modified
AFTER UPDATE ON users
FOR EACH ROW WHEN NEW.last_modified <= OLD.last_modified
BEGIN
    UPDATE users SET last_modified= (strftime('%s', 'now')) WHERE id=OLD.id;
END;
CREATE TRIGGER IF NOT EXISTS messages_trigger_last_modified
AFTER UPDATE ON messages
FOR EACH ROW WHEN NEW.last_modified <= OLD.last_modified
BEGIN
    UPDATE messages SET last_modified= (strftime('%s', 'now')) WHERE id=OLD.id;
END;
CREATE TRIGGER IF NOT EXISTS images_trigger_last_modified
AFTER UPDATE ON images
FOR EACH ROW WHEN NEW.last_modified <= OLD.last_modified
BEGIN
    UPDATE images SET last_modified= (strftime('%s', 'now')) WHERE id=OLD.id;
END;
PRAGMA user_version = 1;
`;

// Insert some Users
const row: Array<Array<any>> = [
  ['Whiteley', 'Whiteley.com', 30],
  ['Jones', 'Jones.com', 44],
];
export const twoUsers = `
DELETE FROM users;
INSERT INTO users (name,email,age) VALUES ("${row[0][0]}","${row[0][1]}",${row[0][2]});
INSERT INTO users (name,email,age) VALUES ("${row[1][0]}","${row[1][1]}",${row[1][2]});
`;

````

## Initialization

*app.module.ts*

````typescript
import { SQLiteService } from './shared/services/sqlite/sqlite.service';

@NgModule({
  declarations: [AppComponent],
  entryComponents: [],
  imports: [
    BrowserModule,
    IonicModule.forRoot(),
    HttpClientModule,
    AppRoutingModule],
  providers: [
    SQLiteService // <-- import here
    { provide: RouteReuseStrategy, useClass: IonicRouteStrategy },
  ],
  bootstrap: [AppComponent],
})
export class AppModule {}

````

*app.component.ts*

````typescript
import { SQLiteService } from './shared/services/sqlite/sqlite.service';
import { DatabaseService } from './shared/services/database.service';
import { Component } from '@angular/core';
import { LoadingController, Platform } from '@ionic/angular';

import { SplashScreen } from '@capacitor/splash-screen';
import { StatusBar, Style } from '@capacitor/status-bar';

@Component({
  selector: 'app-root',
  templateUrl: 'app.component.html',
  styleUrls: ['app.component.scss'],
})
export class AppComponent {
  private initPlugin: boolean;

  constructor(
    private platform: Platform,
    private databaseService: DatabaseService,
    private loadingCtrl: LoadingController,
    private sqlite: SQLiteService
  ) {
    this.platform.ready().then(async () => {
      this.initSQLiteCommunity();
    });
  }

  initSQLiteCommunity() {
    StatusBar.setStyle({ style: Style.Light });
    SplashScreen.hide();
    this.sqlite.initializePlugin().then(ret => {
      this.initPlugin = ret;
      console.log('>>>> in App  this.initPlugin ' + this.initPlugin);
    });
  }
}
````

## Home page

*home.ts*
````typescript
import { BehaviorSubject } from 'rxjs';
import { SqlLiteApiService } from './../shared/services/sqlite-api.service';
import { AfterViewInit, Component, OnDestroy, OnInit } from '@angular/core';
import { User } from '../shared/models/user.model';

@Component({
  selector: 'app-home',
  templateUrl: './home.page.html',
  styleUrls: ['./home.page.scss'],
})
export class HomePage implements AfterViewInit, OnDestroy {

  sqlite: any;
  //users: User[] = [];
  username = '';
  useremail = '';
  userage = null;
  users$: BehaviorSubject<User[]> = new BehaviorSubject([]);

  constructor(private sqliteApi: SqlLiteApiService) { }

  ngOnInit() {
    this.users$ = this.sqliteApi.users$;
  }
  async ngAfterViewInit() {
    await this.sqliteApi.initializeDBConnection()
    .then(() => {
      this.fetchUsers();
    });
  }

  async ngOnDestroy() { await this.sqliteApi.closeConnection(); }

  async intializeFirstData() {
    await this.sqliteApi.populateMockData()
    .then(async () => {
      await this.fetchUsers();
    });
  }

  async deleteUser(user: User, event) {
    event.stopPropagation();
    this.sqliteApi.deleteUser(user)
    /*.then(() => this.fetchUsers())*/;
  }

  async deleteAllUsers(): Promise<void> {
    await this.sqliteApi.deleteAllUsers()/*
    .then(() => this.fetchUsers())*/;
  }

  async addUserWithStatementAndValues(): Promise<void> {
    if (this.username === '' || this.useremail === '') {
      alert('Name and email must be filled');
      return;
    }
    await this.sqliteApi.addUserWithStatementAndValues(
      new User(null, this.username, this.useremail, this.userage))
      .then(() => {
        this.username = '';
        this.useremail = '';
        this.userage = null;
        this.fetchUsers();  // obligatoire si on veut connaître l'id du nouveau user
      });
  }

  async refreshData(ev) {
    await this.fetchUsers()
    .then(() => {
      ev.target.complete();
    });
  }

  async fetchUsers() {
    await this.sqliteApi.fetchUsers()
    /*.then((res) => this.users = res )*/;
  }
````

*home.html*
````html
<ion-header>
  <ion-toolbar>
    <ion-title>sqlite-manual</ion-title>
    <ion-buttons slot="end">
      <ion-button (click)="deleteAllUsers()">
        <ion-icon slot="icon-only" name="trash"></ion-icon>
      </ion-button>
    </ion-buttons>
  </ion-toolbar>
</ion-header>

<ion-content fullscreen>
  <ion-refresher slot="fixed" (ionRefresh)="refreshData($event)">
    <ion-refresher-content></ion-refresher-content>
  </ion-refresher>
  <div class="form-container">
    <ion-button expand="block" (click)="intializeFirstData()" color="dark">Initialize data</ion-button>
    <ion-item>
      <ion-label position="floating">Name</ion-label>
      <ion-input autocapitalize="on" [(ngModel)]="username" placeholder="Name"></ion-input>
    </ion-item>
    <ion-item>
      <ion-label position="floating">Email</ion-label>
      <ion-input [(ngModel)]="useremail" type="email" placeholder="email"></ion-input>
    </ion-item>
    <ion-item>
      <ion-label position="floating">Age</ion-label>
      <ion-input [(ngModel)]="userage" type="number" placeholder="age"></ion-input>
    </ion-item>
    <ion-button expand="block" (click)="addUserWithStatementAndValues()">Add user</ion-button>
    <ion-list>
      <ion-item *ngFor="let user of users$ | async">
        <div class="user-item" [routerLink]="['detail', user.id]">
          <h4 class="ion-text-capitalize">#{{ user?.id }} {{ user?.name }}</h4> {{ user?.age }} years
          <p>{{ user?.email }} </p>
        </div>
        <ion-button fill="clear" (click)="deleteUser(user, $event)" slot="end">
          <ion-icon name="trash" slot="icon-only"></ion-icon>
        </ion-button>
      </ion-item>
    </ion-list>
  </div>
</ion-content>

````

## Detail page

*detail.ts*
````typescript
import { SqlLiteApiService } from './../../shared/services/sqlite-api.service';
import { AfterViewInit, ChangeDetectorRef, Component, OnInit } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';
import { User } from 'src/app/shared/models/user.model';
import { LoadingController } from '@ionic/angular';

@Component({
  selector: 'app-detail',
  templateUrl: './detail.page.html',
  styleUrls: ['./detail.page.scss'],
})
export class DetailPage implements OnInit, AfterViewInit {

  user: User = new User();

  constructor(
    private activatedRoute: ActivatedRoute,
    private router: Router,
    private sqliteApi: SqlLiteApiService,
    private loadingCtrl: LoadingController) { }

  async ngOnInit() { }

  async ngAfterViewInit() {
    const loading = await this.loadingCtrl
    .create({
      message: 'Loading user infos...'
    });
    await loading.present();

    const id = this.activatedRoute.snapshot.paramMap.get('id');
    await this.sqliteApi.fetchUserById(id)
    .then((res) => {
      this.user = res;
      loading.dismiss();
    })
    .catch((e) => loading.dismiss());
  }

  async saveUser(): Promise<void> {
     await this.sqliteApi.updateUser(this.user);
    this.router.navigate(['/sqlite-manual']);
  }
}

````

*detail.html*
````html
<ion-header>
  <ion-toolbar>
    <ion-buttons slot="start">
      <ion-back-button defaultHref="/"></ion-back-button>
    </ion-buttons>
    <ion-title>User detail</ion-title>
  </ion-toolbar>
</ion-header>

<ion-content fullscreen>
  <ion-item>
    <ion-label position="floating">Name</ion-label>
    <ion-input [(ngModel)]="user.name" placeholder="Name"></ion-input>
  </ion-item>
  <ion-item>
    <ion-label position="floating">Email</ion-label>
    <ion-input [(ngModel)]="user.email" type="email" placeholder="email"></ion-input>
  </ion-item>
  <ion-item>
    <ion-label position="floating">Age</ion-label>
    <ion-input [(ngModel)]="user.age" type="number" placeholder="age"></ion-input>
  </ion-item>
  <ion-button expand="block" (click)="saveUser()">Save user</ion-button>
</ion-content>
````
