
[< Back to main Menu](https://github.com/gsoulie/angular-resources/blob/master/ng-sheet.md)    

# Angularfire (update 2021)

Utilisation d'**Angularfire** avec **Firestore**

* [Installation et configuration](#installation-et-configuration)
* [Règles d'accès](#règles-dacces)    
* [Requêtage](#requêtage)     
* [Utilisation](#utilisation)     
* [Authentification](#authentification)     
* [app.component.ts](#app-component-ts)     
* [Routing](#routing)      
* [Guard](#guard)       
* [Infos asynchrones](#infos-asynchrones)      
* [Règles de sécurité Firebase](#règles-de-sécurité-firebase)       
* [Utiliser des Promises](#utiliser-des-promises)      

## Installation et configuration

- 1 - Créer le projet dans la console firebase

- 2 - Installation du package ````ng add @angular/fire````

- 3 - Ajouter les clés dans les fichiers *environment.ts* (ou ailleurs)

````typescript
export const environment = {
  production: false,
  firebaseConfig: {
    apiKey: "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    authDomain: "xxxxxxx.firebaseapp.com",
    databaseURL: "https://xxxxxxxx.firebaseio.com",
    projectId: "xxxxxxxxxxxx",
    storageBucket: "xxxxxxxxxx.appspot.com",
    messagingSenderId: "xxxxxxxxxxxxx",
    appId: "xxxxxxxxxxxxxxxxxxxxxxxxxx"
  }
};
````
[Back to top](#angularfire-update-2021)  

- 4 - Importer les dépendances dont vous avez besoin dans le *app.module.ts*

````typescript
import { initializeApp,provideFirebaseApp } from '@angular/fire/app';
import { environment } from '../environments/environment';
import { provideAuth,getAuth } from '@angular/fire/auth';
import { provideFirestore,getFirestore } from '@angular/fire/firestore';

@NgModule({
  declarations: [AppComponent],
  entryComponents: [],
  imports: [
    BrowserModule,
    IonicModule.forRoot(),
    AppRoutingModule,
    provideFirebaseApp(() => initializeApp(environment.firebaseConfig)),	//<---
    provideAuth(() => getAuth()),	//<---
    provideFirestore(() => getFirestore())	//<---
  ],
  providers: [{ provide: RouteReuseStrategy, useClass: IonicRouteStrategy }],
  bootstrap: [AppComponent],
})
export class AppModule {}
````

## Règles d'accès

Définir les règles d'accès à la base :

````typescript
{
  "rules": {
    ".read": "now < 1703977200000",  // 2023-12-31
    ".write": "now < 1703977200000",  // 2023-12-31
  }
}
````
Utiliser les sites suivants pour définir la date voulue

* convert milliseond to date : https://www.timecalculator.net/milliseconds-to-date      
* convert date to milliseconds : https://currentmillis.com/      


## Requêtage
[Back to top](#angularfire-update-2021)  

Exemple de service manipulant les données firebase (CRUD)
*data.service.ts*

````typescript
import { ListTypeEnum } from './../enums/listType.enum';
import { CollectionEnum } from './../enums/collection.enum';
import { Injectable } from '@angular/core';
import { Firestore, collectionData, docData } from '@angular/fire/firestore';
import { addDoc, deleteDoc, collection, CollectionReference, doc, DocumentData,
  DocumentReference, updateDoc, orderBy, query, where } from '@firebase/firestore';
import { BehaviorSubject, Observable } from 'rxjs';
import { first, tap } from 'rxjs/operators';
import { CategoriesDto } from '../dto/categories.dto';
import { GameDto } from '../dto/game.dto';

@Injectable({
  providedIn: 'root'
})
export class DataService {

  categories$: BehaviorSubject<CategoriesDto[]> = new BehaviorSubject([]);
  currentGame$: BehaviorSubject<GameDto> = new BehaviorSubject(null);
  constructor(private firestore: Firestore) { }

  //#region games
  fetchGames(): Observable<GameDto[]> {
    const collectionRef = this.getCollectionRef(CollectionEnum.games);
    const orderConstraint = orderBy('title');
    const finalQuery = query(collectionRef, orderConstraint);
    return collectionData(finalQuery, { idField: 'id' }) as Observable<GameDto[]>;
  }

  fetchGameById(id): Observable<GameDto> {
    const documentRef = this.getDocumentRef(CollectionEnum.games, id);
    return docData(documentRef, { idField: 'id' }) as Observable<GameDto>;
    //.pipe(tap(res => this.currentGame$.next(res as GameDto))) as Observable<GameDto>;
  }

  fetchGameByListType(listType: ListTypeEnum): Observable<GameDto[]> {
    const collectionRef = this.getCollectionRef(CollectionEnum.games);
    const orderConstraint = orderBy('title');
    const whereConstraint = where('listType', '==', listType);
    const finalQuery = query(collectionRef, orderConstraint, whereConstraint);
    return collectionData(finalQuery, { idField: 'id' }) as Observable<GameDto[]>;
    //.pipe(tap(res => this.currentGame$.next(res as GameDto))) as Observable<GameDto>;
  }


  // Convertir observable en promise : NE SEMBLE PAS FONCTIONNER AVEC FIRESTORE
  async fetchGameByIdPromise(id) {
    const documentRef = this.getDocumentRef(CollectionEnum.games, id);
    await docData(documentRef, { idField: 'id' })
    .pipe(first())
    .toPromise()
    .then(res => {
      console.log('loading...');
      this.currentGame$.next(res as GameDto);
    });
  }

  addGame(game: GameDto) {
    const collectionRef = this.getCollectionRef(CollectionEnum.games);
    return addDoc(collectionRef, game);
  }

  deleteGame(game: GameDto) {
    const documentRef = this.getDocumentRef(CollectionEnum.games, game.id);
    return deleteDoc(documentRef);
  }

  updateGame(game: GameDto) {
    const documentRef = this.getDocumentRef(CollectionEnum.games, game.id);
    return updateDoc(documentRef, {
      title: game.title,
      duration: game.duration,
      price: game.price,
      players: game.players,
      listType: game.listType,
      editor: game.editor,
      comment: game.comment,
      categories: game.categories
    });
  }
  //#endregion games

  //#region categories
  /**
   * { idField: id } permet de mettre le guid généré automatiquement par firebase dans la colonne id.
   * Evite de devoir créer sois-même à la main une colonne 'id'
   * @returns catégories ordonnées par label
   */
  fetchCategories(): Observable<CategoriesDto[]> {
    const collectionRef = this.getCollectionRef(CollectionEnum.categories);
    const orderConstraint = orderBy('label');
    const finalQuery = query(collectionRef, orderConstraint);
    return collectionData(finalQuery, { idField: 'id' })
    .pipe(tap(res => this.categories$.next(res as CategoriesDto[]))) as Observable<CategoriesDto[]>;

  }
  //#endregion categories


  //#region private methods
  private getCollectionRef(collectionName: string): CollectionReference<DocumentData> {
    return collection(this.firestore, collectionName);
  }

  private getDocumentRef(collectionName: string, itemId: string): DocumentReference<DocumentData> {
    return doc(this.firestore, `${collectionName}/${itemId}`);
  }
  //#endregion private methods
}

````

## Utilisation
[Back to top](#angularfire-update-2021)  

````typescript
 categoriesSubscription$: Subscription;
 games$: Observable<GameDto[]>;
 categories: CategoriesDto[] = [];
  
 initializeData(): void {
    this.categories = this.dataService.categories$.getValue();

    if (this.categories.length <= 0) {
      this.categoriesSubscription$ = this.dataService.fetchCategories()
      .subscribe(res => {
        this.categories = this.dataService.categories$.getValue();
      });
    }

    this.games$ = this.dataService.fetchGameByListType(this.listType);
  }
````
[Back to top](#angularfire-update-2021)  

## Authentification

### Exemple avec url simple et localStorage

*auth.service.ts*
````typescript

export interface AuthResponseData {
  kind?: string;
  idToken: string;
  displayName?: string;
  email: string;
  refreshToken: string;
  expiresIn: string;
  localId: string;
  registered: boolean;
}

const STORAGE_KEY_USER = 'UserData';

@Injectable({
  providedIn: 'root'
})
export class AuthService {

  signinEndpoint = 'https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=';
  user = new BehaviorSubject<User>(null);
  tokenExpirationTimer: any; // permet de gérer l'auto-logout

  constructor(private http: HttpClient, private router: Router) {}

  autoLogin() {
    const userData: {
      email: string,
      id: string,
      _token: string,
      _tokenExpirationDate: string
    } = JSON.parse(localStorage.getItem(STORAGE_KEY_USER));

    if (!userData) { return; }

    const loadedUser = new User(
      userData.email,
      userData.id,
      userData._token,
      new Date(userData._tokenExpirationDate)
    );

    if (loadedUser.token) {
      this.user.next(loadedUser);
      const expirationDuration = new Date(userData._tokenExpirationDate).getTime() - new Date().getTime();
      this.autoLogout(expirationDuration);
    }
  }

  autoLogout(expirationDuration: number) {
    this.tokenExpirationTimer = setTimeout(() => {
      this.logout();
    }, expirationDuration);
  }

  login(email, password): Observable<AuthResponseData | any> {
    return this.http.post<AuthResponseData>(this.signinEndpoint + environment.firebaseConfig.apiKey, {
      email,
      password,
      returnSecureToken: true
    })
      .pipe(
        take(1),  // permet de se désabonner dès la réception de la réponse
        catchError(this.handleError),
        tap((res: AuthResponseData) => {
          this.handleAuthentication(
            res.email,
            res.localId,
            res.idToken,
            +res.expiresIn)
        }
        )
    );
  }

  logout() {
    this.user.next(null);
    this.router.navigate(['/login']);
    localStorage.removeItem(STORAGE_KEY_USER);
    if (this.tokenExpirationTimer) {
      clearTimeout(this.tokenExpirationTimer)
    }
    this.tokenExpirationTimer = null;
  }

  private handleAuthentication(email: string, userId: string, token: string, expiresIn: number) {
    const expirationDate = new Date(new Date().getTime() + expiresIn * 1000)
    const loggedUser = new User(
      email,
      userId,
      token,
      expirationDate
    );

    this.user.next(loggedUser);
    this.autoLogout(expiresIn * 1000);
    localStorage.setItem(STORAGE_KEY_USER, JSON.stringify(loggedUser));
  }

  private handleError(e: HttpErrorResponse) {
    if (!e.error || !e.error.error) {
      return throwError(() => new Error('Error'));
    }
    switch (e.error.error.message) {
      case 'EMAIL_NOT_FOUND':
        return throwError(() => new Error('User email not found !'));
      case 'INVALID_PASSWORD':
        return throwError(() => new Error('User password is invalid !'));
      case 'USER_DISABLED':
        return throwError(() => new Error('User account is disabled !'));
      default:
        return throwError(() => new Error('Unknown Error !'));
    }
  }
}
````

*user.model.ts*
````typescript
export class User {
  constructor(
    public email: string,
    public id: string,
    private _token: string,
    private _tokenExpirationDate: Date
  ) { }

  /**
   * Retourner le token s'il existe et qu'il n'est pas expiré
   */
  get token(): string {
    if (!this._tokenExpirationDate || new Date() > this._tokenExpirationDate) {
      return null;
    }
    return this._token;
  }
  get tokenExpirationDate(): Date { return this._tokenExpirationDate; }
}
````

*app.component.ts*
````typescript
export class AppComponent implements OnInit {
  username = '';
  user$: BehaviorSubject<User>;

  constructor(private authService: AuthService) { }

  ngOnInit(): void {
    this.user$ = this.authService.user;
    this.authService.autoLogin();
  }

  logout() { this.authService.logout(); }
}
````

*auth.guard.ts*
````typescript
export class AuthGuard implements CanActivate {
  constructor(private authService: AuthService, private router: Router) { }

  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {

    return this.authService.user
      .pipe(
        take(1),
        map(user => {
          const isAuth = !!user;
          if (isAuth) {
            return true;
          }
          return this.router.createUrlTree(['/login']);
        })
      );
  }
````

### Exemple service authentification AngularFire

*auth.service.ts*

````typescript
import { Injectable } from '@angular/core';
import { Auth, createUserWithEmailAndPassword, getAuth, onAuthStateChanged,
  sendEmailVerification,
  signInWithEmailAndPassword, signOut, User } from '@angular/fire/auth';
import { ToastController } from '@ionic/angular';
import { BehaviorSubject } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class AuthService {

  currentUser$: BehaviorSubject<User> = new BehaviorSubject(null);
  userIsLogged$: BehaviorSubject<boolean> = new BehaviorSubject(false);
  private userLoggedIn = false;
  private auth: Auth;

  constructor(public afAuth: Auth, private toastCtrl: ToastController) {
    this.auth = getAuth();

    this.afAuth.onAuthStateChanged((user) => {
      this.userLoggedIn = user ? true : false;
      this.userIsLogged$.next(user ? true : false); // Avec behaviourSubject
    });
  }

  isLogged(): boolean {
    return this.auth.currentUser !== null ? true : false;
  }

  async signup(email: string, password: string): Promise<any> {
    return createUserWithEmailAndPassword(this.auth, email, password)
    .then((userCredentials) => {
      const user = userCredentials.user;

      // Send verification email to user
      sendEmailVerification(userCredentials.user);
    })
    .catch(async (error) => {
      const toast = await this.toastCtrl.create({
        message: error,
        duration: 2000
      });
      await toast.present();
    });
  }

  async signin(email: string, password: string): Promise<any> {
    return signInWithEmailAndPassword(this.auth, email, password)
    .then((userCredentials) => {
      const user = userCredentials.user;
      this.currentUser$.next(user);
    })
    .catch(async (error) => {
      const toast = await this.toastCtrl.create({
        message: error,
        duration: 2000
      });
      await toast.present();
    });
  }

  signout(): Promise<void> {
    return signOut(this.auth)
    .then(() => this.currentUser$.next(null));
  }
}
````

## app.component.ts
[Back to top](#angularfire-update-2021)  

*app.component.ts*

````typescript
import { DataService } from './shared/services/data.service';
import { Component, OnInit } from '@angular/core';
import { Router } from '@angular/router';
import { Auth } from '@angular/fire/auth';

@Component({
  selector: 'app-root',
  templateUrl: 'app.component.html',
  styleUrls: ['app.component.scss'],
})
export class AppComponent implements OnInit {
  constructor(
    private auth: Auth,
    private dataService: DataService,
    private router: Router) {}

  ngOnInit(): void {
    if (this.auth.currentUser) {
      this.dataService.fetchCategories();
      this.router.navigate(['tabs']);
    }
  }
}
````

## Routing
[Back to top](#angularfire-update-2021)  

*app-routing.module.ts*

````typescript
  {
    path: '',
    pathMatch: 'full',
    redirectTo: 'tabs'
  },
  {
    path: 'tabs',
    loadChildren: () => import('./tabs/tabs.module').then(m => m.TabsPageModule),
    canActivate: [AuthGuard]
  },
  {
    path: 'login',
    loadChildren: () => import('./pages/login/login.module').then( m => m.LoginPageModule)
  },
````

## Guard
[Back to top](#angularfire-update-2021)  

````typescript
import { Injectable } from '@angular/core';
import { Auth } from '@angular/fire/auth';
import { ActivatedRouteSnapshot, CanActivate, Router, RouterStateSnapshot, UrlTree } from '@angular/router';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  constructor(private auth: Auth, private router: Router) {}

  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {

      return new Promise((resolve, reject) => {
        this.auth.onAuthStateChanged((user) => {
          if (user) {
            resolve(true);
          } else {
            this.router.navigate(['/login']);
            resolve(false);
          }
        });
      });
  }
}
````
[Back to top](#angularfire-update-2021)  

## Infos asynchrones

*home.view.html*

````html
<mat-list *ngIf="(user | async)">
    <mat-list-item>Account Type: {{ (user | async)?.accountType }}</mat-list-item>
    <mat-list-item>Display Name: {{ (user | async)?.displayName }}</mat-list-item>
    <mat-list-item>Display Name (lower): {{ (user | async)?.displayName_lower }}</mat-list-item>
    <mat-list-item>Email: {{ (user | async)?.email }}</mat-list-item>
    <mat-list-item>Email (lower): {{ (user | async)?.email_lower }}</mat-list-item>
    <mat-list-item>Email Verification Status: {{ (afAuth.user | async)?.emailVerified }}</mat-list-item>
</mat-list>
````

*home.component.ts*

````typescript
import { Firestore, collection, doc, collectionData, docData } from '@angular/fire/firestore';
import { Auth } from '@angular/fire/auth';

user: Observable<any>;              // Example: store the user's info here (Cloud Firestore: collection is 'users', docId is the user's email, lower case)

    constructor(private afAuth: Auth, private firestore: Firestore) {
        this.user = null;
    }

    ngOnInit(): void {
        this.afAuth.authState.subscribe(user => {
            console.log('Dashboard: user', user);

            if (user) {
                let emailLower = user.email.toLowerCase();
                const ref = doc(this.firestore, 'users/' + emailLower)
                this.user = docData(ref)
            }
        });
    }
````
[Back to top](#angularfire-update-2021)  

## Règles de sécurité Firebase

Pour terminer, il faut rédiger les bonnes règles de sécurité d'accès à la base Firestore depuis le dashboard Firebase pour activer l'accès en fonction de l'authentification de l'utilisateur

````typescript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if
          request.auth != null;
          //request.time < timestamp.date(2021, 12, 25);
    }
  }
}
````

## Utiliser des Promises
[Back to top](#angularfire-update-2021)  

Dans certain cas, on peut ne pas avoir besoin de la connexion bi-directionnelle permanente avec Firestore (temps réel) et il est préférable de récupérer les données via des Promises.

**ATTENTION** il faudra gérer à la main le refresh des données après chaque opération de CRUD

*service.ts*

````typescript
  games: GameDto[] = []; // On peut également utiliser un BehaviourSubject à la place

  async fetchGamesByListTypeWithPromise(listType: ListTypeEnum): Promise<void> {
      const collectionRef = this.getCollectionRef(CollectionEnum.games);
    const orderConstraint = orderBy('title');
    const whereConstraint = where('listType', '==', listType);
    const finalQuery = query(collectionRef, orderConstraint, whereConstraint);
    await collectionData(finalQuery, { idField: 'id' })
    .pipe(first())
    .toPromise()
    .then(res => {
      this.games = res as GameDto[];
    })
    .catch((e) => console.warn(e));
  }
````

*controller.ts*

````typescript
  games: GameDto[] = [];
  
  async initializeData(): Promise<void> {
    await this.dataService.fetchGamesByListTypeWithPromise(this.listType);
    this.games = this.dataService.games;
 }
````

[Back to top](#angularfire-update-2021)  
