
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
[Back to top](#angularfire)  

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
[Back to top](#angularfire)  

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

## Authentification
[Back to top](#angularfire)  

Exemple service authentification

*auth.service.ts*

````typescript
import { Injectable } from '@angular/core';
import { Auth, createUserWithEmailAndPassword, getAuth, signInWithEmailAndPassword, signOut, User } from '@angular/fire/auth';
import { ToastController } from '@ionic/angular';
import { BehaviorSubject } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class AuthService {

  currentUser$: BehaviorSubject<User> = new BehaviorSubject(null);
  private user: User;
  private auth: Auth;

  constructor(public afAuth: Auth, private toastCtrl: ToastController) {
    this.auth = getAuth();
  }

  async signup(email: string, password: string) {
    //const auth = getAuth();
    createUserWithEmailAndPassword(this.auth, email, password)
    .then((userCredentials) => {
      const user = userCredentials.user;
    })
    .catch(async (error) => {
      const toast = await this.toastCtrl.create({
        message: error,
        duration: 2000
      });
      await toast.present();
    });
  }

  async signin(email: string, password: string) {
    //const auth = getAuth();
    signInWithEmailAndPassword(this.auth, email, password)
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

  signout() {
    return signOut(this.auth)
    .then(() => this.currentUser$.next(null));
  }

  isLogged(): boolean {
    return this.auth.currentUser !== null ? true : false;
  }
}

````
[Back to top](#angularfire)  
