[< Back to main Menu](https://github.com/gsoulie/angular-resources/blob/master/ng-sheet.md)    

# PouchDB

PouchDB est une implémentation JavaScript de CouchDB. Son objectif est d'émuler l'API CouchDB avec une fidélité quasi parfaite, lors de l'exécution dans le navigateur ou dans Node.js.
PouchDB est donc une base de données NoSQL orientée objet qui utilise la structure de données du navigateur pour stocker les données localement. Contrairement à CouchDB, PouchDB est une base de données JavaScript qui fonctionne dans le navigateur et permet de stocker les données sur l'appareil de l'utilisateur. Elle est conçue pour offrir une expérience de développement plus facile et plus fluide pour les développeurs d'applications web et mobiles. Les données stockées dans PouchDB peuvent être facilement synchronisées avec une base de données CouchDB, ce qui permet la synchronisation des données entre différentes applications et appareils.

**CouchDB** est une base de données NoSQL orientée documents qui stocke des données au format JSON. Elle utilise un modèle de stockage clé-valeur pour stocker des documents qui peuvent être facilement indexés, consultés et filtrés. CouchDB a été conçu pour offrir une haute disponibilité, une tolérance aux pannes et une réplication facile des données entre différents nœuds. Les données dans CouchDB sont stockées sur disque et peuvent être consultées via une interface HTTP RESTful. CouchDB est également compatible avec les protocoles de synchronisation tel que CouchReplication et CouchDB Sync, qui permettent la synchronisation des données avec d'autres bases de données CouchDB.

https://pouchdb.com/getting-started.html

https://www.youtube.com/watch?v=y_dMYfmVI5M&ab_channel=HGSTUFF

https://www.youtube.com/watch?v=-Z7UF2TuSp0&ab_channel=NolanLawson

*Installation*
````
npm install pouchdb --save
npm i --save-dev @types/pouchdb
````

*tsconfig.json*

````json
{
  "compilerOptions": {
    "allowSyntheticDefaultImports": true
  }
}
````

*Import dans les composants*

````typescript
import * as PouchDB from 'pouchdb';
````

Si une erreur ````ReferenceError: global is not defined```` est levée, ajouter le code suivant dans le fichier *polyfills.ts*

````
(window as any).global = window;
````

Il est "préférable" d'utiliser la méthode ````put()```` pour insérer un nouvel élément dans la base plutôt que ````post()````. C'est une question d'optimisation.
En utilisant ````post()````, pouchDB va créé lui même le uuid de l'élément sous le format standard. Hors ce format est plus "lourd" qu'un id de type ````new Date().toISOString()````, et 
on gagne en optimisation lors de l'indexation d'un id de type date plutôt qu'une chaîne de type uuid standard.


*Installation CouchDB server pour Windows*
````
npm i couchdb
npm i -g pouchdb-server	// dans le projet

pouchdb-server -p 5984
````

````
navigate to http://127.0.0.1:3000/_utils for the Fauxton UI.
accès interface : http://localhost:5984/_utils
accès base : http://localhost:5984/users	
````

[Back totop](#pouchdb)      
