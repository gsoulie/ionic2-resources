[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Third party lib

## Create lib file

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
		this.UTILS.info("TEST","my first trace");
	}
}
```

## Create file for constant

### 1 Create "lib" folder under app

```
/app/lib/const.ts
```

### 2 implement lib class

*utils.ts*
```javascript
import { Injectable } from '@angular/core';

@Injectable()

export class LibConst {

  public static SAY_HELLO = "Hello !";
  public static AGE = 35;
}
```
**Be careful** and don't forget **@Injectable** decorator

### 3 Inject class in other component

```javascript
import {LibConst} from '../../lib/const';

export class HomePage {
	
	constructor(){
		alert(LibConst.SAY_HELLO);
	}
}
