
[< Back to main Menu](https://github.com/gsoulie/angular-resources/blob/master/ng-sheet.md)    

# Angularfire (update 2021)

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

## Authentification
[Back to top](#angularfire-update-2021)  

Exemple service authentification

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
