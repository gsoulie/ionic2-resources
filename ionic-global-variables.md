# Global variables

Here's a solution to centralize application's globals. The solution consist in using **config.js** file in provider's folder.

You can export all your variable like below :

```javascript
export let VALUE_ONE = "09:00";
export let VALUE_TWO = "192.168.0.1";
...
```

And then in the component or service that requires them, just do this :

```javascript
import {VALUE_ONE} from './config';
```

### Constant

Constant can be declared after lib import like :

```javascript
import {Component} from '@angular/core';
import {NavController, AlertController, ToastController} from 'ionic-angular';
import {Utils} from '../../providers/utils/utils';
import {File} from 'ionic-native';

const fs:string = cordova.file.dataDirectory;
```
