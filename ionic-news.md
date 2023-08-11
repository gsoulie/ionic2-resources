[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# News

* [Ionic 7.2](#ionic-72)
* [Ionic 7.1](#ionic-71)
* [Ionic 7](#ionic-7)
* [Chrome 108 impact sur clavier virtuel](#chrome-108-impact-sur-clavier-virtuel)    

# Ionic 7.2

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

[Back to top](#news)     

# Ionic 7.1

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

[Back to top](#news)   

# Ionic 7

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
