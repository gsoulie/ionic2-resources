# Bluetooth    

* [Bluetooth](https://github.com/gsoulie/ionic2-resources/blob/master/ionic-bluetooth.md)    
	* [BLE](#ble)    
	* [Bluetooth Serial](#bluetooth-serial)  

## BLE    

Plugin installation

```
ionic cordova plugin add cordova-plugin-ble-central
npm install --save @ionic-native/ble
```

[native ble](https://ionicframework.com/docs/native/ble/)    

[personal BLE repo](https://github.com/gsoulie/ionicBLE)

### Reading advertising data

When you scan for BLE device, each device return some informations like : name, id, rssi and advertising data. Advertising packet gives you some informations which don't require device connection.

You can use the following plugin to retrieve and decode the advertising packet :
[link : advlib](https://github.com/reelyactive/advlib)

**Installation**

```npm install advlib```

**Usage**

*Controller file*

```javascript
import { xxxx } from '@ionic-native/BLE';
...

declare function require(name: string);		// Needed to use 'require'
var adv = require('advlib');

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
	...
  onGetAdvertisingData(device){
    var adData = new Uint8Array(device.advertising)
    var hexabuffer = this.buf2hex(adData);

    alert(JSON.stringify(adv.ble.process(hexabuffer),null," "));
  }
  
  // Convert UINT8Array buffer to hexa string
  buf2hex(buffer) { // buffer is an ArrayBuffer
    return Array.prototype.map.call(new Uint8Array(buffer), x => ('00' + x.toString(16)).slice(-2)).join('');
  }
	
}
```

### Scan

[Back to top](#bluetooth)   

*Home Controller file*

```javascript
import { BLE } from '@ionic-native/BLE';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
  devices: any[] = [];

  constructor(private ble: BLE){}
  
  onScan(){
    
    //TODO : add test on bluetooth activation
    this.devices = [];
    this.ble.startScan([]).subscribe(device => {
        this.devices.push(device);
    });
    setTimeout(() => {
        this.ble.stopScan().then(() => {
          this.isScanning = false;
        });
    }, 10000);    
  }
}
```

### Connect

[Back to top](#bluetooth)   

*Device Controller file*

```javascript
import { BLE } from '@ionic-native/BLE';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
  devices: any{} = {};
  dataset = [];

  constructor(private ble: BLE, public loadingCtrl: LoadingController){
      this.device = this.navParams.get('device');
  }
  
  onConnect(){
    var loader = this.loadingCtrl.create({
      content: "Connecting..."
    });
    loader.present();
    this.ble.connect(this.device.id)
    .subscribe(
      (peripheralData) => {
        loader.onDidDismiss(()=>{
          this.dataset = peripheralData.characteristics;
        });
        loader.dismiss();
      },
      (err) => {
        loader.dismiss();
        alert("connection failed " + JSON.stringify(err));
        this.navCtrl.popToRoot();
      },
      () => {
        loader.dismiss();
        alert("state undefined");
    }); 
  }
}
```

### Read

[Back to top](#bluetooth)   

*Device Controller file*

```javascript
import { BLE } from '@ionic-native/BLE';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
  devices: any{} = {};
  dataset = [];

  constructor(private ble: BLE, public loadingCtrl: LoadingController){
      this.device = this.navParams.get('device');
  }
  
    /**
   * Read characteristic info
   * @param service 
   * @param characteristic (objet characteristic)
   */
  onReadCharacteristic(service,characteristic) {
    
    this.ble.read(this.device.id,service,characteristic.characteristic).then(
      function(buffer){
        var databuffer = new Uint8Array(buffer);
        switch(characteristic.type){	// See GATT definition to know the type (string, number, complex)
          case 'string':
            characteristic.value = String.fromCharCode.apply(null,databuffer)+characteristic.unit;
            break;
          case 'number':
            characteristic.value = databuffer[0]+characteristic.unit;
            break;
          default:
            characteristic.value = JSON.stringify(databuffer)+characteristic.unit;
            break;
        }        
      }
    );
  }
}
```

### Write

[Back to top](#bluetooth)   

*Device Controller file*

```javascript
import { BLE } from '@ionic-native/BLE';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
  devices: any{} = {};
  dataset = [];

  constructor(private ble: BLE, public loadingCtrl: LoadingController){
      this.device = this.navParams.get('device');
  }
  
  /**
   * Write on characteristic
   * @param service 
   * @param characteristic 
   * @param value 
  */
  onWriteCharacteristic(service, characteristic, value){
    this.ble.write(this.device.id,service,characteristic,value).then(
      function(res){
         alert("Ecriture : " + JSON.stringify(res));
      }
    );
  }
  
  onEnableTemperature(){
      var value = new Uint8Array(1);
      value[0] = 0x01;
      
      // !!! IMPORTANT Use value.buffer !!
      this.onWriteCharacteristic("F000AA00-0451-4000-B000-000000000000","F000AA02-0451-4000-B000-000000000000",value.buffer);
  }
  
}
```

## Bluetooth serial
[Back to top](#bluetooth)   

[link : official documentation](https://ionicframework.com/docs/native/bluetooth-serial/)    

First, install *cordova-plugin-bluetooth-serial* plugin according to the documentation

```
$ ionic cordova plugin add cordova-plugin-bluetooth-serial
$ npm install --save @ionic-native/bluetooth-serial
```

[personnal bluetooth serial repo](https://github.com/gsoulie/bluetooth-serial)    

### Scan

*Controller file*

```javascript
 /**
  * Scan for bounded devices and just displaying devices which name contains "BLUE-READ"
  */
  onScan(refresher = null){
    this.devices = [];
    this.bluetoothSerial.list()
    .then(data => {
      for(let i = 0; i<data.length; i++){
        if(data[i].name.toUpperCase().indexOf("BLUE-READ") >= 0){
          this.devices.push(data[i]);
        }
      }
      if(refresher){refresher.complete();}
    })
    .catch(error => {
      alert(JSON.stringify(error));
      this.devices = [];
    })
  }
```

### Connect

[Back to top](#bluetooth)   

Connect selected device

*Controller file*

```javascript
 /**
  * Connect to bluetooth device
  * @param btObject : selected device
  */
  onConnect(btObject){
    var loader = this.loadingCtrl.create({
      content: "Connecting " + btObject.name + "...",
      dismissOnPageChange: true
    });

    this.bluetoothSerial.isEnabled()
    .then(data => {
      this.btEnabled = true;
      this.macAddress = btObject.address.toUpperCase();
      loader.present();

      // Connection
      this.bluetoothSerial.connect(this.macAddress)
      .subscribe(
        (data) => {
          this.bluetoothSerial.isConnected()
          .then(data => {
            this.blueReadConnected = true;
            this.btName = btObject.name;
            this.btMac = btObject.address;

            loader.onDidDismiss(() => {
              this.navCtrl.push(ModeExpertPage,{btName:btObject.name, btMac: btObject.address})
            });
            loader.dismiss();
          })
          .catch(error => {
            loader.dismiss();
            alert("\r\nBlueRead disconnected\r\n");
            this.blueReadConnected = false;
          });
      },
      (error) => {
        alert("\r\nConnection status : " + JSON.stringify(error));
        loader.dismiss();
      });
        // Tiemout function to refresh connexion status
        setTimeout(function(){
        },4500);
    })
    .catch(err => {
      this.btEnabled = false;
      loader.dismiss();
    });    
  }
  
  
   /**
   * Return true if the bluetooth device is connected
   */
  isConnected(){
    var res = false;
    this.bluetoothSerial.isConnected()
    .then(data => {
      alert("Blue-Read connected");
      this.blueReadConnected = true;
      res = true;
    })
    .catch(error => {
      alert("Blue-Read disconnected");
      this.blueReadConnected = false;
    });

    return res;
  }
```

### Read

[Back to top](#bluetooth)   

*Controller file*

```javascript
 /**
  * Subscribe to be notified when data is received.
  */
  onRawData(){
    this.bluetoothSerial.subscribeRawData().subscribe(
      (data) => {
        var temp = new Uint8Array(data);
        for(let x=0; x < temp.length; x++){
          this.output += ("0" + parseInt(temp[x].toString(16),16).toString(16)).slice(-2).toUpperCase() + " "; 
        }            
      },
      (error) => {
        alert("\r\nReception error : " + JSON.stringify(error) + "\r\n");
    });
  }
```

### Write

[Back to top](#bluetooth)   

*Controller file*

```javascript
  /**
   * Write data from input field to the bluetooth device
   */
  onWrite(){
    var frame = this.onBuildFrame(this.stringToWrite); // optionnal, in this case the function onBuildFrame generate CRC of the hexadecimal frame
    
    this.bluetoothSerial.isConnected()
    .then(data => {
      this.bluetoothSerial.write(new Buffer(frame,"hex"))
      .then(data => {
        this.output += "\r\n\r\nTx : " + frame + "\r\n";  
        this.onRawData();	// Read the response
        setTimeout(function(){},1500);
      })
      .catch(error => {this.output += "\r\nWrite Error : " + JSON.stringify(error);});
    })
    .catch(error => {
      this.output += "\r\nBT disconnected";
    });    
  }
  ```
