[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# News

* [Ionic 7.6](#ionic-76)
* [Ionic 7.5](#ionic-75)
* [Ionic 7.4](#ionic-74)
* [Ionic 7.3](#ionic-73)
* [Ionic 7.2](#ionic-72)
* [Ionic 7.1](#ionic-71)
* [Ionic 7](#ionic-7)
* [Chrome 108 impact sur clavier virtuel](#chrome-108-impact-sur-clavier-virtuel)

# Ionic 7.6

<details>
  <summary>Article</summary>

**Ionic 7.6** est là, avec des mises à jour d'Input, Radio, Toast et bien plus encore ! Cette version facilite le style des composants et introduit des améliorations de l'expérience utilisateur.

> [Source Ionic Blog](https://ionic.io/blog/announcing-ionic-7-6)    

## Toujours plus d'options de theming

Ionic 7.6 apporte de nouvelles options pour personnaliser les labels sur Checkbox, Radio, Range et Toggle. Bien que le contenu du label puisse actuellement être stylisé avec CSS, il était auparavant difficile de styliser le conteneur du label. Le nouveau label Shadow Part résout ce problème en donnant un accès direct au conteneur depuis leur CSS.

````typescript
ion-checkbox::part(label) {
  padding: 10px;
}
````

## Boutons, icônes et texte à l'intérieur des champs de texte

Une autre nouveauté de cette version est la possibilité d'ajouter des boutons, des icônes et du texte au début ou à la fin de la zone de saisie et de la zone de texte. C'est idéal pour ajouter un bouton pour afficher dynamiquement le contenu d'une entrée de mot de passe ou pour ajouter une unité à une entrée numérique.

````typescript
<ion-input label="Password" label-placement="stacked" fill="outline" type="password">
  <ion-button fill="clear" slot="end">
    <ion-icon slot="icon-only" name="eye"></ion-icon>
  </ion-button>
</ion-input>
````

````typescript
<ion-input label="Order Quantity" label-placement="stacked" fill="outline" value="10000">
  <div slot="end">cupcakes</div>
</ion-input>
````

## Swipe sur Toast

Les toasts peuvent désormais être swipés pour être fermés à l'aide de la nouvelle propriété **swipeGesture**.

````typescript
 <ion-toast
    message="This toast can be swiped to dismiss"
    trigger="open-toast"
    swipeGesture="vertical"
    position="bottom"
    positionAnchor="footer"
  >
</ion-toast>
````
## Comparaison avancée des Radios boutons
La nouvelle propriété **compareWith** sur *Radio Group*. Cela permet d'effectuer des opérations de comparaison plus complexes lorsque la valeur de chaque radio n'est pas une valeur primitive.

````html
<ion-list>
  <ion-radio-group [compareWith]="compareWith">
    <ion-item *ngFor="let food of foods; trackBy: trackItems">
      <ion-radio [value]="food">{{ food.name }}</ion-radio>
    </ion-item>
  </ion-radio-group>
</ion-list>
````

````typescript
foods = [
  { id: 1, name: 'Apples', type: 'fruit' },
  { id: 2, name: 'Carrots', type: 'vegetable' },
  { id: 3, name: 'Cupcakes', type: 'dessert' },
];

compareWith(o1, o2) {
  return o1.id === o2.id;
}
````
</details>

# Ionic 7.5

<details>
  <summary>Article</summary>

Ionic 7.5 est sorti de presse !

> [Blog officiel : Ionic 7.5](https://ionic.io/blog/announcing-ionic-7-5)


## Dynamic Font scalling

Dans le cadre de ce changement, la plupart des composants de l'interface utilisateur Ionic ont été mis à jour pour utiliser des unités ````rem```` au lieu des unités ````px````, le cas échéant. 

Cela permet de **mettre à l'échelle** tous les composants qui participent à la mise à l'échelle dynamique des polices de manière cohérente.

> Cette feature sera activée par défaut dans **Ionic 8** et ne nécessitera 

[Lien vers la documentation](https://ionicframework.com/docs/layout/dynamic-font-scaling?__hstc=13779304.69521e917fe19e1f23e75f08a96aa9df.1660035654876.1697095218497.1697114439992.86&__hssc=13779304.1.1697114439992&__hsfp=645712254&_gl=1*173izby*_ga*MzE0MTkxMjMzLjE2NjAwMzU2NTQ.*_ga_REH9TJF6KF*MTY5NzE3NzYwMC4xMDkuMC4xNjk3MTc3NjAwLjAuMC4w)    

> **Important** : cette fonctionnalité est activée par défaut sous Android. Pour l'activer sous iOS  il faut déclarer le code suivant dans la feuille de style globale. **En version 8 le code suivant ne sera plus nécessaire** 

*global.scss*
````css
html {
  --ion-dynamic-font: var(--ion-default-dynamic-font);
}
````



## Angular standalone component

Ionic 6.3 a introduit la prise en charge de l'architecture en standalone component (notion introduite par Angular 15). 

A partir de Ionic 7.5, il est désormais possible d'importer des composants UI Ionic en tant que composants standalone Angular.

L'utilisation des composants UI Ionic en tant que composants standalone présente plusieurs avantages :

* Réduction de la taille finale du bundle en raison d'un meilleur tree-shaking. Cela inclut la suppression des SVG Ionicon non utilisés.

* Le chargement des applications est amélioré. Les composants standalone Ionic utilisent le même système de compilation que Ionic React et Ionic Vue, ce qui évite l'hydratation.

* Étant donné que les composants autonomes Ionic peuvent être analysés statiquement, les développeurs pourront profiter de nouvelles fonctionnalités dans leurs applications Angular, telles qu'ESBuild.

## Ancrage Toast message

Afin d'améliorer le composant Toast, il est désormais possible de spécifier le composant sur lequel on souhaite ancrer le toast, via la propriété ````positionAnchor```` et ainsi éviter que le toast recouvre malencontreusement d'autres composants

````typescript
<ion-toast message="Your playlist was created successfully" position-anchor="my-tabbar"></ion-toast>

<ion-tabs>
  <ion-tab-bar id="my-tabbar" slot="bottom">
    …
  </ion-tab-bar>
</ion-tabs>
````

## Datetime support des cycles h11 et h24

Le composant Datetime a été mis à jour pour prendre en charge les cycles d'heures h11 et h24 pour l'affichage des heures. Cette fonctionnalité rejoint la prise en charge existante des cycles horaires h12 et h23.

````typescript
<!-- This would render the time as "0:00 AM" –>
<ion-datetime hour-cycle="h11" locale="en-US" presentation="time" value="2023-01-01T00:00"></ion-datetime>
````

</details>

# Ionic 7.4

<details>
  <summary>Article</summary>

Ionic 7.4 vient d'arriver avec son lot de petites améliorations sur les **Datetime**, **Checkbox**, **Radio** et **Toggle**

> [Blog officiel : Ionic 7.4](https://ionic.io/blog/announcing-ionic-7-4)

## Nouvelles options de customisation des Datetime

Un nouveau paramètre css shadow part ````calendar-day```` permet de customiser plus facilement le bouton de sélection du jour. Combiné à ce nouveau sélecteur, les propriétés ````active````, ````today```` et ````disabled```` ont été ajoutées pour customiser plus finement les différents états

````typescript
ion-datetime::part(calendar-day) {
  color: purple;
}

ion-datetime::part(calendar-day disabled) {
  color: gray;
}

ion-datetime::part(calendar-day today) {
  color: purple;
  border: 1px solid purple;
}

ion-datetime::part(calendar-day active) {
  background: purple;
  color: white;
}
````

[![calendar.png](https://wiki-collab.groupe-isia.com/uploads/images/gallery/2023-09/scaled-1680-/calendar.png)](https://wiki-collab.groupe-isia.com/uploads/images/gallery/2023-09/calendar.png)

## Prise en charge des valeurs asynchrones dans les Datetime

Dans les versions précédentes de Ionic, l'affectation d'une valeur asynchrone dans le composant Datetime n'était pas rafraichie si le composant était déjà affiché.

Dans la version 7.4, le composant sera automatiquement réactualisé à la réception d'une valeur asynchrone

## Nouveau slot de positionnement Stacked label

Un nouveau slot de positionnement ````stacked```` a été ajouté pour les composants **Checkbox, Radio, Range et Toggle**.

Ce nouveau slot permet de positionner le label au-dessus du composant et l'alignement peut être ajusté via la propriété ````alignment````

````typescript
<ion-toggle label-placement="stacked" alignment="start">
Enable Notifications
</ion-toggle>
````

</details>

# Ionic 7.3

<details>
  <summary>Article</summary>

Ionic 7.3 (version mineure) vient d'arriver avec quelques petites améliorations

> [Blog officiel : Ionic 7.3](https://ionic.io/blog/announcing-ionic-7-3)     

## Nouveaux attributs HTML sur les Boutons des Alert, Action Sheet et Toast

Les composants de type *overlay* comme les Alert, Action Sheet et Toast permettent de définir des boutons d'actions (bouton confirmation, annulation...).

Auparavant, il était impossible de customiser ses boutons tel que les définir comme 
des boutons *icon-only*, leur ajouter des attributs html comme par exemple *aria-label* etc...

Dans cette nouvelle release, il est désormais possible de customiser ses boutons :

````typescript
const toast = await toastController.create({
  message: 'This is a toast',
  buttons: [
    { text: 'Confirm' },
    {
      role: 'cancel', 
      icon: 'close', 
      htmlAttributes: {
        'aria-label': 'Close Toast'
      }
    }
  ]
});
````

## Customisation des boutons sur les Toasts

Auparavant il n'était pas possible de customiser le bouton cancel dans les Toast. Ionic 7.3 permet maintenant cette customisation via l'ajout d'un **shadow part** css sur le bouton cancel

````css
ion-toast::part(button) {	// customisation des autres boutons
  color: purple;
}

ion-toast::part(button cancel) {	// customisation du bouton cancel
  color: orange;
}
````

</details>

# Ionic 7.2

<details>
  <summary>Article</summary>

> [Source : Ionic 7.2](https://ionic.io/blog/announcing-ionic-7-2)

La version 7.2 est arrivée, voici les nouveautés qu'elle apporte 

## Button Wrapping

À partir d'**Ionic v8**, les boutons avec un texte long auront un retour à la ligne plutôt que d'être tronqués avec des points de suspension. Cette modification est issue d'une collaboration avec Todd Libby, un expert en accessibilité du W3C, afin d'assurer la lisibilité du texte tronqué.

Cette fonctionnalité n'est **pas activée par défaut dans Ionic 7.2** pour permettre aux développeurs de s'adapter progressivement.

### Comment activer cette feature ?

Il est désormais possible d'activer cette fonctionnalité soit en utilisant la classe ````ion-text-wrap```` du ````<ion-button>````

````html
<ion-button class="ion-text-wrap">Some text with a really long label</ion-button>
````

Soit en définissant le style css suivant :

````css
ion-button {
  white-space: normal;
}
````

## Nouvelle propriété "name" pour la Searchbar

La Searchbar dispose maintenant d'une propriété ````name```` pour faciliter l'identification des données de recherche lors de la soumission d'un formulaire.

````html
<ion-searchbar placeholder="Search for an Artist" name="artist-search"></ion-searchbar>
````

## Binding des paramètres de route Angular

Ionic 7.2 prend en charge la fonctionnalité introduite dans **Angular 16**, qui permet aux développeurs de *lier des paramètres de route en tant qu'entrées de composant*. 

Pour rappel, cela permet d'accéder aux paramètres pour /my-route/:id en utilisant le décorateur @Input dans le composant associé

Pour utiliser cette fonctionnalité, il faut mettre à jour la configuration du router en activant le paramètre **bindToComponentInputs** de la manière suivante :

````typescript
RouterModule.forRoot(routes, {
  //... other features
  bindToComponentInputs: true
});
````

ou bien de la manière suivante **si vous utilisez les standalone components**

````typescript
provideRouter(appRoutes, withComponentInputBinding());
````

</details>


[Back to top](#news)     

# Ionic 7.1

<details>
  <summary>Article</summary>

## Principales évolutions de Ionic 7.1

> [Ionic blog - annoucing ionic 7.1](https://ionic.io/blog/announcing-ionic-v7-1)     

### Gestion des masques de saisie avec Maskito

Ionic recommande l'utilisation de la librairie **Maskito** pour la gestion des masques de saisie

````npm install @maskito/core @maskito/angular````

````html
<ion-list>
  <ion-item>
    <ion-input
      label="Card number"
      placeholder="0000 0000 0000 0000"
      [maskito]="cardMask"
      [maskitoElement]="maskPredicate"
    ></ion-input>
  </ion-item>
  <ion-item>
    <ion-input
      label="US phone number"
      placeholder="+1 (xxx) xxx-xxxx"
      [maskito]="phoneMask"
      [maskitoElement]="maskPredicate"
    ></ion-input>
  </ion-item>
</ion-list>
````

````typescript
import { Component } from '@angular/core';

import { MaskitoOptions, MaskitoElementPredicateAsync } from '@maskito/core';

@Component({
  selector: 'app-example',
  templateUrl: 'example.component.html',
})
export class ExampleComponent {
  readonly phoneMask: MaskitoOptions = {
    mask: ['+', '1', ' ', '(', /\d/, /\d/, /\d/, ')', ' ', /\d/, /\d/, /\d/, '-', /\d/, /\d/, /\d/, /\d/],
  };

  readonly cardMask: MaskitoOptions = {
    mask: [
      ...Array(4).fill(/\d/),
      ' ',
      ...Array(4).fill(/\d/),
      ' ',
      ...Array(4).fill(/\d/),
      ' ',
      ...Array(4).fill(/\d/),
      ' ',
      ...Array(4).fill(/\d/),
    ],
  };

  readonly maskPredicate: MaskitoElementPredicateAsync = async (el) => (el as HTMLIonInputElement).getInputElement();
}
````
### Nouveau slot "label"

De nouveaux "slot" ont été ajoutés pour les éléments ````<ion-select>````, ````<ion-input>```` et ````<ion-textarea>````. Ce nouveau slot permet de renseigner des labels avec du code html customisé contrairement à la propriété ````label```` existante qui ne fourni que du texte brut

````html
<ion-select fill="outline" label-placement="floating" value="apple">
  <div slot="label">Favorite Fruit <ion-text color="danger">(Required)</ion-text></div>
  <ion-select-option value="apple">Apple</ion-select-option>
  <ion-select-option value="banana">Banana</ion-select-option>
  <ion-select-option value="orange">Orange</ion-select-option>
</ion-select>

<ion-input fill="outline" label-placement="floating" value="hi@ionic.io">
  <div slot="label">Email <ion-text color="danger">(Required)</ion-text></div>
</ion-input>

<ion-textarea fill="outline" label-placement="floating" value="Lorem Ipsum">
  <div slot="label">Comments <ion-text color="danger">(Required)</ion-text></div>
</ion-textarea>
````

### Ajout de style sur les date pickers

De nouvelles propriétés permettent maintenant de modifier le style des rouleaux, ainsi que cibler l'élément actif via la propriété ````active````

Voir les propriétés css ````--wheel-highlight-background, --wheel-fade-background-rgb```` ainsi que les sélecteurs css ````month-year-button, time-button````:

````css
ion-datetime {
  --background: #333;
  --wheel-highlight-background: #111;
  --wheel-fade-background-rgb: 51, 51, 51;

  border-radius: 8px;

  width: 350px;

  color: white;
}

ion-datetime::part(month-year-button) {
  --color: white;
}

ion-datetime::part(time-button) {
  background: #111;
  color: white;
}
````

### Nouveau style shadow dom pour les select

Afin de gagner en customisation, les sélecteurs suivants ont été ajouté aux éléments ````<ion-select>````

````css
ion-select::part(container) {
  width: 100%;
}

ion-select::part(label) {
  color: #6e5afd;
}
````

### Icône personalisée pour les ion-select

Il est désormais possible de contrôler l'icône utilisée dans le ````<ion-select>```` de manière simple avec les propriétés ````toggleIcon```` et ````expandedIcon````.
Cette propriété supporte les icônes SVG et les icônes provenant de **ionicons**

````html
<style>
ion-select::part(icon) {
  width: 22px;

  color: #0088cc;
}
</style>

<ion-select toggle-icon="caret-down-circle-outline" expanded-icon="caret-up-circle-outline" fill="icon" label="Favorite Fruit" label-placement="floating">
  <ion-select-option value="apple">Apple</ion-select-option>
  <ion-select-option value="banana">Banana</ion-select-option>
  <ion-select-option value="orange">Orange</ion-select-option>
</ion-select>
````

</details>


[Back to top](#news)   

# Ionic 7

<details>
  <summary>Article</summary>

`30/03/2023`

> [ionic 7 is here](https://ionic.io/blog/ionic-7-is-here)

Ionic 7 apporte des améliorations dans l'expérience de développement en ce qui concerne les overlays, les formulaires et les événements. Les performances ont également été améliorées pour les applications Angular, React et Vue.

### Inline overlay

Après les Modal et Popover, c'est au tour des **Action sheet, Alert, Loading, Picker et Toast** de pouvoir être déclarées directement dans le template html.

````typescript
<ion-button id="open-loading">Open Loading</ion-button>
<ion-loading trigger="open-loading" message="Loading…"></ion-loading>
````

A ceci s'ajoute les propriétés **isOpen** et **trigger** permettant de gérer plus facilement les intéractions.

### Émissions d'événements cohérentes

Dans le passé, l'évènement `ionChange` se déclenchait de manière inattendue en raison des changements de valeur de propriété. Au lieu de cela, le comportement a été modifié pour mieux correspondre aux éléments d'entrée natifs, et `ionChange` ne sera déclenché qu'en réponse à l'interaction de l'utilisateur sur le champ actif.

### Syntaxe de contrôle de formulaire simplifiée

Très bonne nouvelle que cette simplification qui était très attendue ! Les champs imactés sont les suivants : *checkbox, input, radio, range, select et textarea*

Nous allons pouvoir simplifier le code suivant :

```html
<ion-item fill=”outline”>
  <ion-label>Email:</ion-label>
  <ion-input placeholder=”hi@ionic.io”></ion-input>
  <div slot=”helper”>Please enter a valid email address</div>
</ion-item>

```

Par cette nouvelle syntaxe :

```html
<ion-input
  label=”Email:” 
  placeholder=”hi@ionic.io” 
  fill=”outline” 
  helper-text=”Please enter a valid email address”
></ion-input>

```

### Améliorations de performances

Les changement d'onglets sont jusqu'à 70% plus rapides qu'avec Ionic React et Ionic Vue v6. Grâce à des optimisations dans Stencil, les composants Ionic s'initialisent plus rapidement qu'ils ne le faisaient avec Ionic Angular v6.

### IonSlides

Les **ion-slides** sont définitivement retirés au profit de l'utilisation de la librairie JS **Swiper.js**.

### IonVirtualScroll

Le composant **ion-virtual-scroll** a lui aussi été supprimé laissant chaque framework utiliser sa propre solution. Par exemple, Angular utilise le `import { ScrollingModule } from '@angular/cdk/scrolling';`

[Back to top](#news)    
</details>

# Chrome 108 impact sur clavier virtuel

## Impact sur le viewport avec clavier virtuel

En **novembre 2022** l'arrivée de chrome 108 a modifié la façon dont le viewport est redimensionné lors de l'affichage du clavier virtuel sous Android.

> [Lien de l'article](https://ionic.io/blog/preparing-your-ionic-app-for-changes-coming-to-chrome-for-android)

![](https://img.shields.io/badge/Important-DD0031.svg?logo=LOGO) Ce changement impactera uniquement les applications ionic web, c'est à dire qui utilisent chrome sous Android. Les applications **natives** Capacitor / Cordova ne seront pas concernées

### Changements induits

En migrant vers la version **6.3.5** ou supérieure de Ionic, aucune modification ne sera nécessaire sur l'application.

Dans le cas ou une migration ne serait pas possible, la solutions suivante permet de gérer ce changement :

Ajouter le meta tag suivant `interactive-widget=resizes-content` dans le fichier *index.html*

*index.html*

```html
<meta name="viewport" content="viewport-fit=cover, width=device-width, initial-scale=1.0, interactive-widget=resizes-content" />

```

[Back to top](#news)     
