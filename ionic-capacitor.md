# Capacitor

## What is capacitor
Capacitor is the new Ionic's native web app container that runs your web app natively in iOS, Android, Electron, PWA...
Capacitor is the successor of Cordova, it containerizes your web app and puts it into a managed native web view, then it exposes nativefunctionality to your web app in cross-platform way.

### Requirements

* XCode 10 minimum
* Java 8 SDK (Java 9 does not work at the moment)

Capacitor uses the WKWebView

### Usage

*Create new ionic capacitor project*

```ionic start myApp tabs --capacitor```

*Add capacitor in existing project*

```ionic integrations enable capacitor```

*initialize capacitor with app information* 
```npx cap init [appName] [appId]```

**You must build your ionic app at least once before adding any native platforms**
```ionic build```

*add platforms*

```npx cap add ios / npx cap add android```

*open IDE to build, run, and deploy*

```npx cap open ios```
