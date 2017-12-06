# Third party lib
[Back to top](#ionic-2)

## ES6
To use a custom lib file in your project, follow the next steps 

### 1 Create "lib" folder under app

```
/app/lib/utils.js
```

### 2 implement lib file

*utils.js*
```javascript
var UI = {};

UI.info = function(_titre, _message){
  _titre = _titre != "" ? _titre : "";
  _message = _message != "" ? _message : "";
  
  console.log(`[--- ${_titre} ---] ${_message}`);
};

export UI;

```
or export only some functions

```javascript
export function getSquare(_value){	// Only this function will be available from external
   return square(_value);
}

function square(_value){	// Not exported
   return _value * _value;
}
```

### 3 Using the custom lib in the whole project

*From Home.js*
```javascript
import * as UI from '../../lib/utils';

@Component({
  templateUrl: 'build/pages/home/home.html'
})
export class HomePage {

  //Chargement de la base de données
  constructor() {
    UI.info("test","toto"); 
  }
}
```

## TypeScript

### 1 Create "lib" folder under app

```
/app/lib/utils.ts
```

### 2 implement lib class

*utils.ts*
```javascript
import {Component, Injectable} from '@angular/core';

@Injectable()
export class UTILS {
    
    constructor() {}
    public info(_elt: string, _msg: string){
        _elt = _elt || "";
        _msg = _msg || "";
        console.log('[--- ' + _elt + ' ---] ' + _msg);
    }
}
```
**Be careful** and don't forget **@Injectable** decorator

### 3 Inject class in other component

```javascript
import {UTILS} from '../../lib/utils';

export class HomePage {
	utils: UTILS;
	constructor(){
		this.utils = new UTILS();
		this.utils.info("TEST","my first trace");
	}
}
```
