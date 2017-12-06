# Prerequisites

## Start with ionic

### Typical installation

[CLI 3 Command cheat sheet](https://docs.google.com/document/d/1r8nTAaJ5hLIJ1DCwBozU-JGV480Du0xCMIg2dj3JRQo/edit#heading=h.kk1966kbedef)     

**Install / update Node**

First, ensure you have latest version of node

```
$ sudo npm install npm -g
```
**Install cordova**

```
$ sudo npm install -g cordova
```

**Install ionic**

ionic 2 is not yet available in final version, but is it possible to create ionic projects with the ionic 2 beta which can be installed as followed CLI

```
$ sudo npm install -g ionic
```

### Configure Android environment variable

```
nano ~/.bash_profile
```

```
export ANDROID_HOME=/Users/gsoulie/Library/android-sdk-macosx
export PATH=${PATH}:/Users/gsoulie/Library/android-sdk-macosx/tools:/Users/gsoulie/Library/android-sdk-macosx/platform-tools
```

**refresh bash_profile**

```
$ source .bash_profile
```

Then, restart your computer and test ANDROID_HOME ```$ANDROID_HOME``` don't forget **$** on the command


### Start with ionic

**ionic 2 project creation**

```
$ ionic start myionic2project --v2 --ts
```

**--ts** to work with TypeScript

**ionic 2 tutorial app**

```
$ ionic start myTutorial tutorial --v2
```

**add platform support to project**

(run it inside you're project folder)

```
$ ionic cordova platform add android
```

**Generate new page**

It creates new folder in the project treeview with js, html and scss files

```
$ ionic g page myNewPage
```

**Generate new provider**

It creates new folder in the project treeview

```
$ ionic g provider myProvider
```

**Generate new component**

```
$ ionic g component myComponent
```

You can use it with 

```javascript
<ion-content>
	<mycomponent>
	
	</mycomponent>
</ion-content>
```
**Display log with ionic serve**

```
$ ionic serve -l -s -c
```

```
[--consolelogs|-c] ......  Print app console logs to Ionic CLI
[--serverlogs|-s] .......  Print dev server logs to Ionic CLI
[--port|-p] .............  Dev server HTTP port (8100 default)
[--livereload-port|-i] ..  Live Reload port (35729 default) ========> automatic reload on device
[--nobrowser|-b] ........  Disable launching a browser
[--nolivereload|-r] .....  Do not start live reload
[--noproxy|-x] ..........  Do not add proxies
```

**Testing app on multiple screen sizes and platform**

```
$ ionic serve --lab
```

**Running Emulator**

```
$ ionic cordova build ios
$ ionic cordova emulate ios
```

**Run on device**

After app is running on device, you can inspect it with chrome inspect device

[chrome://inspect/#devices](chrome://inspect/#devices)    

![alt tag](https://s-media-cache-ak0.pinimg.com/originals/8e/52/17/8e5217a74089f046435655d0e0477517.png)


**Installing Gulp**

Gulp is a packet manager for JS libraries. You can use it to add libraries to your Ionic app or to update the Ionic JavaScript library itself.

```
$ sudo npm install -g bower
```

## Visual Studio Code
[Back to top](#ionic-2) 

Another very good alternative to Atom is [visual studio code] (https://code.visualstudio.com/c?utm_expid=101350005-27.GqBWbOBuSRqlazQC_nNSRg.2&utm_referrer=https%3A%2F%2Fwww.google.fr%2F)

### Some useful extensions

* [Ionic2 extension](https://marketplace.visualstudio.com/items?itemName=Thavarajan.ionic2)    
* [Auto Import extension](https://marketplace.visualstudio.com/items?itemName=steoates.autoimport)    
* [Angular 2 - TypeScript snippets](https://marketplace.visualstudio.com/items?itemName=johnpapa.Angular2)    
* [Git History] (https://marketplace.visualstudio.com/items?itemName=donjayamanne.githistory)    
* [custom icons theme](https://code.visualstudio.com/blogs/2016/09/08/icon-themes)    
* [npm intellisense](https://marketplace.visualstudio.com/items?itemName=christian-kohler.npm-intellisense)    
* [sass lint](https://marketplace.visualstudio.com/items?itemName=glen-84.sass-lint)    
* [TSlint](https://marketplace.visualstudio.com/items?itemName=eg2.tslint)    
