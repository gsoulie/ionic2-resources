[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Animation

## Rotating Border animation

### Base example

*html*

```
<div class="rainbow">
	Rainbow border
</div>
```

*css*

```
body {
	display: flex;
	justify-content: center;
	align-items: center;
	height: 100vh;
}

*, *::before, *::after {
	box-sizing: border-box;
}

@keyframes rotate {
	100% {
		transform: rotate(1turn);
	}
}

.rainbow {
	position: relative;
	z-index: 0;
	width: 400px;
	height: 300px;
	border-radius: 10px;
	overflow: hidden;
	padding: 2rem;
	
	&::before {
		content: '';
		position: absolute;
		z-index: -2;
		left: -50%;
		top: -50%;
		width: 200%;
		height: 200%;
		background-color: #399953;
		background-repeat: no-repeat;
		background-size: 50% 50%, 50% 50%;
		background-position: 0 0, 100% 0, 100% 100%, 0 100%;
		background-image: linear-gradient(#399953, #399953), linear-gradient(#fbb300, #fbb300), linear-gradient(#d53e33, #d53e33), linear-gradient(#377af5, #377af5);
		animation: rotate 4s linear infinite;
	}
	
	&::after {
		content: '';
		position: absolute;
		z-index: -1;
		left: 6px;
		top: 6px;
		width: calc(100% - 12px);
		height: calc(100% - 12px);
		background: white;
		border-radius: 5px;
	}
}
```


### Custom round button with round border rotate animation

*html*

```
<div class="button-container">
  <div class="border-animation"></div>
  <ion-button class="roundButton" fill="clear">
    <img src="../assets/images/round_button.png"/>
  </ion-button>
</div>
```

*css*

```
.button-container {
    position: relative;
    left: 0px;
    right: 0px;
    top: 0px;
    bottom: 0px;
}
.roundButton {
    position: absolute;
    margin: auto;
    display: block;
}

*, *::before, *::after {
	box-sizing: border-box;
}

@keyframes rotate {
	100% {
        transform: rotate(1turn);
        -webkit-transform: rotate(1turn);
	}
}

// Definition for Safari compatibility (0%, 25% and 50% are needed)
@-webkit-keyframes spin {
    0% {
        -webkit-transform: rotate(0turn);
    }
    25% {
        -webkit-transform: rotate(0.25turn);
    }
    50% {
        -webkit-transform: rotate(0.5turn);
    }
    100% {
      -webkit-transform: rotate(1turn);
  }
}
.border-animation {
  position: absolute;
  display: block;
  margin: auto;
	z-index: 0;
	width: 120px;
	height: 120px;
	border-radius: 50%;
	overflow: hidden;
	padding: 2rem;
	
	&::before {
    content: '';
    position: absolute;
		z-index: -2;
		left: -50%;
		top: -50%;
		width: 200%;
		height: 200%;
		background-color: #399953;
		background-repeat: no-repeat;
		background-size: 50% 50%, 50% 50%;
		background-position: 0 0, 100% 0, 100% 100%, 0 100%;
        //background-image: linear-gradient(#58b937, #58b937), linear-gradient(#008dab, #008dab), linear-gradient(#58b937, #58b937), linear-gradient(#008dab, #008dab);
        //background-image: linear-gradient(#58b937, #58b937), linear-gradient(#fff, #fff), linear-gradient(#58b937, #58b937), linear-gradient(#fff, #fff);
        //background-image: linear-gradient(#58b937, #e5e5e5), linear-gradient(#f5f5f5, #f5f5f5), linear-gradient(#58b937, #e5e5e5), linear-gradient(#f5f5f5, #f5f5f5);
        background-image: linear-gradient(#008dab, #f5f5f5), linear-gradient(#f5f5f5, #f5f5f5), linear-gradient(#f5f5f5, #008dab), linear-gradient(#f5f5f5, #f5f5f5);
        animation: rotate 3s linear infinite;
    }
    // Directive for Safari compatibility
    -webkit-animation-name: spin;
    -webkit-animation-duration: 2s;
    -webkit-animation-iteration-count: infinite;
    -webkit-animation-timing-function: linear;
	
	&::after {
    content: '';
    position: absolute;
		z-index: -1;
		left: 6px;
		top: 6px;
		width: calc(100% - 12px);
		height: calc(100% - 12px);
    background: var(--ion-color-light);
		border-radius: 50%;
	}
}
```


## Resources

[link : Josh morony tutorial - animating from the void](https://www.joshmorony.com/animating-from-the-void-enter-and-exit-animations-in-ionic/)     
[link : Josh morony tutorial - add to cart animation](https://www.joshmorony.com/add-to-cart-with-the-web-animations-api-in-ionic/)    
[link : Josh morony tutorial - web animation](https://www.joshmorony.com/using-the-web-animations-api-in-ionic-2/)    
[link : Josh morony tutorial - animating login screen](https://www.joshmorony.com/create-an-animated-login-screen-in-ionic-2/)    

[link : devdactic](https://devdactic.com/animations-ionic-app/)    

## Animation with Angular approach

#### Installation
First, install *web-animation-js* polyfill

```
npm install web-animations-js @angular/animations@4.0.0 --save
```

#### Include polyfill in main.ts

Then, include the polyfill into your *main.ts* file

```javascript
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import 'web-animations-js/web-animations.min';  // web animation polyfill
import { AppModule } from './app.module';

platformBrowserDynamic().bootstrapModule(AppModule);
```

#### Import BrowserAnimationsModule

Import *BrowserAnimationsModule* in *app.module.ts*

```javascript
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';

@NgModule({
  declarations: [
    MyApp,
    HomePage,
    AccordionListPage,
    CpoFlashComponent
  ],
  imports: [
    BrowserModule,
    HttpModule,
    IonicModule.forRoot(MyApp),
    BrowserAnimationsModule // import here
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp,
    HomePage,
    AccordionListPage,
    CpoFlashComponent
  ],
  providers: [
    StatusBar,
    File,
    AppVersion,
    SplashScreen,
    {provide: ErrorHandler, useClass: IonicErrorHandler},
    UtilsProvider,
    UiProvider,
    FlashProvider
  ]
})
export class AppModule {}
```
