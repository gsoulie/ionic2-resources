[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Azure

* [CI/CD pipelines](#ci-/-cd-pipelines)    
* [Aure msal angular with capacitor for android app](#azure-msal-angular-with-capacitor-for-android-app)      
* [Azure msal authentication with Capacitor MS auth](#azure-msal-authentication-with-capacitor-ms-auth)       
* [Azure msal authentication with Capacitor OAuth 2 client plugin](#azure-msal-authentication-with-capacitor-oauth-2-client-plugin)      

## Azure pipeline

<details>
	<summary>How it works</summary>

sources :

* https://sahansera.dev/multi-stage-builds-with-azure-pipelines-ionic/      
* complete CI/CD pipeline : https://www.codewithkarma.com/2020/10/ionic-capacitor-cicd-using-new-yaml.html      

Create a new *pipelines* folder at the root of your project, then create the following pipeline files for a basic Android build (not signed apk) :

> WARNING : be extremely careful with the syntax of yaml files !

*azure-pipelines.yml*

````yml
trigger:
- develop

variables:
  vmImageName: 'windows-latest'
  projectName: 'MyCoolApp'

stages:
  - stage: Build
    displayName: Build Ionic - Android projects
    jobs:
      # Debug build
      - job: Build_Ionic_Android_Debug
        variables:
          - name: buildConfiguration
            value: Debug
        displayName: Build Debug
        pool:
          vmImage: $(vmImageName)
        steps:
          - template: ionic-android-debug-build.yml

      # Release build
      #- job: Build_Ionic_Android_Release
      #  variables:
      #    - name: buildConfiguration
      #      value: Release
      #  displayName: Build Release
      #  pool:
      #    vmImage: $(vmImageName)
      #  steps:
      #    - template: ionic-android-release-build.yml

````

*ionic-android-debug-build.yml*

````yml
steps:
  - script: npm install -g @ionic/cli
    displayName: 'Install Ionic CLI'

  - task: Npm@1
    inputs:
      workingDir: '$(Build.SourcesDirectory)'
      command: install
    displayName: 'NPM Install'

  - powershell: |
      ionic info
      npx cap -V
      ls
    workingDirectory: $(Build.SourcesDirectory)
    displayName: 'Get environment infos'

  - powershell: |
      ionic build --configuration=production # matching with needed ionic environment
      npx cap copy android
      npx cap sync
      cd android
      ./gradlew assemble$(buildConfiguration)
    workingDirectory: $(Build.SourcesDirectory)
    displayName: 'Build Android Project'

  - task: CopyFiles@2
    inputs:
      SourceFolder: '$(Build.SourcesDirectory)/android/app/build/outputs/apk/$(buildConfiguration)'
      contents: "**/app-$(buildConfiguration).apk"
      targetFolder: "$(Build.ArtifactStagingDirectory)/$(projectName)"
    displayName: "Copy unsigned APK to staging directory"

  - task: PublishBuildArtifacts@1
    inputs:
      PathtoPublish: "$(Build.ArtifactStagingDirectory)"
      ArtifactName: "$(projectName)"
      publishLocation: "Container"
    displayName: "Publish artifacts"
````

Then in azure, got to pipeline and create a new pipeline *use the classic editor* > select your branch > *Configuration as code YAML* > select your yml file > *Save & Queue*

[Back to top](#azure)    

</details>

<details>
	<summary>Variable replacement before build process</summary>

 
https://www.youtube.com/watch?v=_iPfzH3ENAk&ab_channel=LKGforIT         

Sometimes we want to change some variables like api endpoints when publishing the app with CI/CD.

Steps to follow :

**1 create a config json file in you *assets* folder** which will be loaded with a factory and *APP_INITAILIZER*

*assets/config.json*
````typescript
{
	"api": {
		"endpoint": "httl://localhost:4200/api"
	},
	"app": {
		"infos": {
			"name": "My cool app",
			"version": "1.0"
		}
	}
}
````

**2 Modify your pipeline files**

This step consist in using a new tas **FileTransform@1** which gonna modify the *config.json* file by replacing all the variables you want.

Add the variables you want to replace (do not forget to add a dot for each level off the json hierarchy) in the pipeline file

````
"app": {
		"infos": {
			"name": "My cool app", // gives => app.infos.name
````

*pipelines/azure-pipeline.yml*

````yml
trigger:
- develop
- master

variables:
  vmImageName: 'windows-latest'
  projectName: 'azurePipeline'
  api.endpoint: http://my-prod-server/api
  app.infos.name: my cool production app
  app.infos.version: v1.0

stages:
  - stage: Build
    displayName: Build Ionic - Android projects
    jobs:
      # Debug build
      - job: Build_Ionic_Android_Debug
        variables:
          - name: buildConfiguration
            value: Debug
        displayName: Build Debug
        pool:
          vmImage: $(vmImageName)
        steps:
          - template: ionic-android-debug-build.yml

````

*ionic-android-debug-build.yml*

````yml
steps:
  # NEW TASK TO REPLACE VARIABLES IN CONFIG.JSON
  - task: FileTransform@1
    inputs:
      folderPath: '$(Build.SourcesDirectory)'
      targetFiles: '**/config.json'
      fileType: json
    displayName: 'File transformation: config.json'

  # BUILD
  - script: npm install -g @ionic/cli
    displayName: 'Install Ionic CLI'

  - task: Npm@1
    inputs:
      workingDir: '$(Build.SourcesDirectory)'
      command: install
    displayName: 'NPM Install'

  - powershell: |
      ionic info
      npx cap -V
      ls
    workingDirectory: $(Build.SourcesDirectory)
    displayName: 'Get environment infos'

  - powershell: |
      ionic build --configuration=production
      npx cap copy android
      npm install jetifier
      npx jetify
      npx cap sync
    workingDirectory: $(Build.SourcesDirectory)
    displayName: 'Ionic Build'

  - task: Gradle@2
    inputs:
      workingDirectory: '$(Build.SourcesDirectory)/android'
      gradleWrapperFile: '$(Build.SourcesDirectory)/android/gradlew.bat'
      tasks: 'build'
      publishJUnitResults: false
      javaHomeOption: 'JDKVersion'
      jdkVersionOption: '1.11'
      gradleOptions: '-Xmx3072m'
    displayName: 'Build Gradle'

  - task: CopyFiles@2
    inputs:
      SourceFolder: '$(Build.SourcesDirectory)/android/app/build/outputs/apk/$(buildConfiguration)'
      contents: "**/app-$(buildConfiguration).apk"
      targetFolder: "$(Build.ArtifactStagingDirectory)/$(projectName)"
    displayName: "Copy unsigned APK to staging directory"

  - task: PublishBuildArtifacts@1
    inputs:
      PathtoPublish: "$(Build.ArtifactStagingDirectory)"
      ArtifactName: "$(projectName)"
      publishLocation: "Container"
    displayName: "Publish artifacts"

````

That's all. After the pipeline is finished you can verify by downloading the apk and running it on a device

</details>



### Incrementing Android versionCode and versionName

See : https://stackoverflow.com/questions/62315203/how-do-i-automatically-increment-the-build-id-and-version-number-of-an-android-a      
<details>
	<summary>Complete apk signin pipeline</summary>


source : 
* https://sahansera.dev/multi-stage-builds-with-azure-pipelines-ionic/        
* https://dev.to/naagalgates/how-to-build-sign-and-distribute-your-android-app-using-azure-pipelines-2d8f      
* https://medium.com/medialesson/build-your-xamarin-android-application-with-azure-devops-9ddba3d04774      

Voici une procédure pour créer une pipeline azure permettant de générer et signer un apk depuis un projet ionic / angular. Attention cette procédure ne décrit pas les étapes de création d'un certificat keystore Android, ni le détail de création d'une pipeline.

note : la partie compilation gradle, signature, copie et génération de l'artifact sont indépendante de
la technologie de développement de l'application

#### Etape 1

Depuis Android studio, créer un certificat depuis le menu ````Build --> Generate Signed Bundle or APK````. Suivre le processus classique de création d'un *keystore* en prenant soin de conserver en lieu sûr le fichier *.jks* généré ainsi que 
l'alias et les mots de passe demandés pour la création.

#### Etape 2

Créer dans azure le jeux de variables contenant l'alias et les mots de passe liés au keystore précédemment généré. Pour se faire, se rendre dans la rubrique ````Pipelines --> Library```` d'azure Devops et créer un nouveau groupe de variable (ici nous l'appellerons *MyCoolProject-Release*). Sélectionner ensuite
le groupe créé et y ajouter les variables *keyAlias*, *keyPassword* et *keystorePassword*.

Ensuite, revenir dans le menu *Library* et cliquer dans la section *Secure files* afin d'ajouter le certificat Android keystore (*.jks) dans la liste des fichiers.

#### Etape 3

Créer une nouvelle pipeline de base au format yaml. Dans cet exemple la pipeline est rattachée à la branche "production", libre à vous de changer.

Attention aux chemins utilisés pour localiser l'apk, c'est la cause principale d'échec de compilation / signature du package

*azure-pipeline-prod.yml*

````yml
trigger:
- production

variables:
  vmImageName: 'windows-latest'
  projectName: 'MyCoolProject'
  api.endpoint: https://my-prod-api.endpoint
  ionicBuildMode: 'production' # pour rendre la méthode de compilation ionic générique s'il y a plusieurs pipelines

stages:
  - stage: Build
    displayName: Build Ionic - Android projects
    jobs:
      # Release build
      - job: Build_Ionic_Android_Release
        workspace:
          clean: all
        variables:
          - group: MyCoolProject-Release  # nom du groupe de variable créé dans azure
          - name: buildConfiguration
            value: Release
        displayName: Build Release
        pool:
          vmImage: $(vmImageName)
        steps:
          - template: ionic-android-release-build.yml
            parameters: # paramètres qui pointent vers le groupe de variables azure
              keystoreFileName: 'my_keystore.jks'
              keystorePassword: $(keystorePassword)
              keyAlias: $(keyAlias)
              keyPassword: $(keyPassword)

````

*ionic-android-release-build.yml*

````yml
parameters:
  - name: keystoreFileName
    displayName: "The keystore file name for signing the apk"
    type: string
  - name: keystorePassword
    displayName: "Password for the keystore"
    type: string
  - name: keyAlias
    displayName: "Key alias"
    type: string
  - name: keyPassword
    displayName: "Key password"

steps:
  - script: npm install -g @ionic/cli
    displayName: 'Install Ionic CLI'

  - task: Npm@1
    displayName: 'NPM Install'
    inputs:
      workingDir: '$(Build.SourcesDirectory)'
      command: install    

  # optionnel : affichage de l'environnement azure
  - powershell: |
      ionic info
      npx cap -V
      ls
    workingDirectory: $(Build.SourcesDirectory)
    displayName: 'Get environment infos'

  - powershell: |
      ionic build --configuration=$(ionicBuildMode) # permet une généricité si plusieurs env de compilation
      npx cap copy android
      npm install jetifier
      npx jetify
      npx cap sync
    workingDirectory: $(Build.SourcesDirectory)
    displayName: 'Build Ionic Android Project'

  # Génération de l'apk NON signé
  - task: Gradle@2
    displayName: 'Build Gradle'
    inputs:
      options: '-PappVersionName=$(Build.BuildNumber) -PappVersionCode=$(Build.BuildId)'  # optionnel : Ici on incrémente automatiquement le versionCode et versionName
      workingDirectory: '$(Build.SourcesDirectory)/android'
      gradleWrapperFile: '$(Build.SourcesDirectory)/android/gradlew.bat'
      tasks: 'assembleRelease'
      testResultsFiles: '**/TEST-*.xml'
      publishJUnitResults: false
      javaHomeOption: 'JDKVersion'
      jdkVersionOption: '1.11'
      jdkArchitectureOption: 'x64'
      checkStyleRunAnalysis: false
      findBugsRunAnalysis: false
      gradleOptions: '-Xmx3072m'    

  - task: AndroidSigning@3
    displayName: 'APK signin'
    inputs:
      apkFiles: '**/app-release-unsigned.apk' # Attention important doit matcher le nom de l'apk issu de la compilation gradle
      apksign: true
      apksignerKeystoreFile: '${{ parameters.keystoreFileName }}'
      apksignerKeystorePassword: '${{ parameters.keystorePassword }}'
      apksignerKeystoreAlias: '${{ parameters.keyAlias }}'
      apksignerKeyPassword: '${{ parameters.keyPassword }}'
      apksignerArguments: '--out $(Build.SourcesDirectory)/android/app/build/outputs/apk/release/tsc-$(buildConfiguration)-$(Build.BuildId).apk --verbose' # renommage de l'apk signé
      zipalign: false # mieux si true car optimise la mémoire utilisée par l'apk
    
  - task: CopyFiles@2
    displayName: "Copy signed APK to staging directory"
    inputs:
      contents: "**/*.apk"
      targetFolder: "$(Build.ArtifactStagingDirectory)/$(projectName)"
      SourceFolder: '$(Build.SourcesDirectory)/android/app/build/outputs/apk/$(buildConfiguration)'
    
  - task: PublishBuildArtifacts@1
    displayName: "Publish artifacts"
    inputs:
      PathtoPublish: "$(Build.ArtifactStagingDirectory)"
      ArtifactName: "$(projectName)"
      publishLocation: "Container" 

````

#### Etape 4 

La dernière étape consiste à exécuter la pipeline. Lors de la première exécution, la pipeline restera bloquée car elle attend l'autorisation pour utiliser le fichier sécurisé du keystore. Il faut donc entrer dans la pipeline en attente, un encart orange indique que la permission d'accès doit être donnée. Il suffit de cliquer sur le bouton et la pipeline s'exécute.

[Back to top](#azure)      
 
</details>

## Azure msal angular with capacitor for android app
[Back to top](#azure)    

Using msal v2 for angular with capacitor for Android app sample repos : https://github.com/gsoulie/ionic-angular-snippets/tree/master/msal-ionic-capacitor       

## Azure msal authentication with Capacitor MS auth
[Back to top](#azure)    

plugin source : https://github.com/recognizegroup/capacitor-plugin-msauth        

### installation 

https://www.npmjs.com/package/@recognizebv/capacitor-plugin-msauth     

````
npm i @recognizebv/capacitor-plugin-msauth
npx cap sync
````

[Back to top](#azure) 

### Azure portal configuration

1 - Go to https://portal.azure.com      
2 - Go to Azure Active Directory (use the searchbar if needed)      
3 - Go to App registrations > New registrations      
4 - Fill the app name and select an account type      
5 - Add an redirection URI of type SPA (for Angular / ionic web) with the value http://localhost:4200 or http://localhost:8100 (ionic)      
5b - For a mobile app, you need to add a new mobile app and fill your appid (com.xxx.xxx) and your hash code generated from your keystore (you will need to install keytool and openssl)      
6 - once it is finished, you can get the following infos (tenantId, clientId, redirect URI...)      

*openssl* : https://www.ssl.com/fr/comment/installer-openssl-sur-windows-avec-cygwin/ next you **must** generate your hash from the cygwin console

[Back to top](#azure) 

### Ionic Android app configuration

**Android**

*android/build.gradle*

````typescript
buildscript {
    repositories {
        google()
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:7.1.2'
        classpath 'com.google.gms:google-services:4.3.10'
        classpath 'com.microsoft.identity.client:msal:0.3.+'	// <--- Add this
    }
}

// <--- Add : start 
allprojects {
  repositories {
    google()
    jcenter()
    maven {
      url 'https://pkgs.dev.azure.com/MicrosoftDeviceSDK/DuoSDK-Public/_packaging/Duo-SDK-Feed/maven/v1'
    }
  }
}
// ---> Add : end

apply from: "variables.gradle"

task clean(type: Delete) {
    delete rootProject.buildDir
}

````
[Back to top](#azure) 

Add the new *BrowserTabActivity*

*AndroidManifest.xml*

````html
<application
	...
	<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
	<activity
	  android:name="com.microsoft.identity.client.BrowserTabActivity"
	  android:exported="true">
	  <intent-filter>
		<action android:name="android.intent.action.VIEW" />
		<category android:name="android.intent.category.DEFAULT" />
		<category android:name="android.intent.category.BROWSABLE" />
		<data
		  android:host="com.xxx.xxx"
		  android:path="/<your_sha1_hash>="
		  android:scheme="msauth"
		/>
		<!-- hash example 
		android:path="/sqlmdkqsf84fzerzf454azz8="
		-->
	  </intent-filter>
	</activity>
</application>
````
[Back to top](#azure)     

### Usage (ionic android app)

*authentication.service.ts*

````typescript
import { BehaviorSubject } from 'rxjs';
import { Injectable } from '@angular/core';
import { MsAuthPlugin  } from '@recognizebv/capacitor-plugin-msauth';
import jwt_decode from "jwt-decode";

@Injectable({
  providedIn: 'root'
})
export class AuthenticationService {

  private accessToken$ = new BehaviorSubject<string>('');

  constructor() { }

  getAccessToken() {
    return localStorage.getItem('accessToken');
  }

  async login() {
    const result = await MsAuthPlugin.login({
      clientId: <your_client_id_from_azure_portal>,
      tenant: <your_tenant_from_azure_portal>,
      scopes: [<your_scopes_azure_portal>],
      keyHash: <your_openssl_sha1_hash>,
    });

    this.accessToken$.next(result.accessToken);
    localStorage.setItem('accessToken', result.accessToken);

    return result.accessToken;
  }

  async logout() {
    const result = await MsAuthPlugin.logout({
     clientId: <your_client_id_from_azure_portal>,
      tenant: <your_tenant_from_azure_portal>,
      keyHash: <your_openssl_sha1_hash>,
    });

    this.accessToken$.next('');
    localStorage.setItem('accessToken', '');

    return '';
  }

  getTokenInfo() {
    const token = this.getAccessToken();
    let decoded: any = null;
    if (token !== null) {
      decoded = jwt_decode(token);
    }
    return decoded;
  }
}

````
[Back to top](#azure)     

*httpInterceptor.service.ts*

````typescript
import { AuthenticationService } from './authentication/authentication.service';
import { HttpErrorResponse, HttpEvent, HttpHandler, HttpInterceptor, HttpRequest, HttpHeaders } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Router } from '@angular/router';
import { Observable, throwError } from 'rxjs';
import { catchError, retry } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class HttpInterceptorService implements HttpInterceptor {

  constructor(
    private authService: AuthenticationService,
    private router: Router) { }

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    let authReq = req;

    const headers = new HttpHeaders({
      Authorization: `Bearer ${this.authService.getAccessToken()}`
    });
    authReq = req.clone({headers});

    return next.handle(authReq)
    .pipe(
      retry(2),
      catchError((error: HttpErrorResponse) => {
        let errorMessage = '';
        if (error.error instanceof ErrorEvent) {
          // client-side error
          errorMessage = `>>>Error: ${error.error.message}`;
        } else {
          // server-side error
          errorMessage = `>>>Error Status: ${error.status}\nMessage: ${error.message}`;
        }
        switch (error.status) {

          case 401 :
            this.authService.login();
            break;
          default:
            break;
        }
        return throwError(error);
      })
    );
  }
}
````
[Back to top](#azure)     

*app.module.ts*

````typescript
providers: [{ provide: RouteReuseStrategy, useClass: IonicRouteStrategy },
    {
      provide : HTTP_INTERCEPTORS,
      useClass: HttpInterceptorService,
      multi   : true,
    },
````
[Back to top](#azure)     

*auth.guard.ts*

````typescript
import { AuthenticationService } from './../services/core/authentication/authentication.service';
import { Injectable } from '@angular/core';
import { ActivatedRouteSnapshot, CanActivate, CanActivateChild, Router, RouterStateSnapshot, UrlTree } from '@angular/router';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate, CanActivateChild {
  constructor(private authService: AuthenticationService,
    private router: Router) {}

  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {

    const isAuthenticated = this.authService.getAccessToken() !== '' && this.authService.getAccessToken() !== null;
    if (!isAuthenticated) { this.router.navigate(['/']); }
    return isAuthenticated;

  }
  canActivateChild(
    childRoute: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {
    return this.canActivate(childRoute, state);
  }
}
````

[Back to top](#azure)     

## Azure msal authentication with Capacitor OAuth 2 client plugin

capacitor-oauth2 : https://github.com/moberwasserlechner/capacitor-oauth2#android-1      
capacitor secure storage : https://www.npmjs.com/package/capacitor-secure-storage-plugin      

This plugin is a **generic OAuth 2 client** plugin.

### installation

````
npm i @byteowls/capacitor-oauth2
npm install capacitor-secure-storage-plugin
npx cap sync
````
[Back to top](#azure)     

### Using Azure AD authentication

#### Add the following activity in your *AndroidManifest.xml*

*AndroidManifest.xml*

````html
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    ...>

    <application
        ...>

        <activity
            ...>

            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

		  <!-- Add the following intent-filter -->
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="@string/custom_url_scheme" android:host="@string/custom_host" />
          </intent-filter>
        </activity>

      <!-- Add the following activity -->
      <activity android:name="net.openid.appauth.RedirectUriReceiverActivity" android:exported="true">
        <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="@string/custom_url_scheme" android:host="@string/custom_host" />
        </intent-filter>

        <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data 
		  android:scheme="@string/azure_b2c_scheme" 
		  android:host="@string/package_name" 
		  android:path="@string/azure_b2c_signature_hash" />
        </intent-filter>
      </activity>
       
    </application>

    <!-- Permissions -->
	...
</manifest>
````
[Back to top](#azure)     

#### Add the following configuration in *app/src/main/res/values/string.xml*

````html
<resources>
    <string name="app_name">your app name</string>
    <string name="title_activity_main">your activity</string>
    <string name="package_name">com.myapp.name</string>
    <string name="custom_url_scheme">com.myapp.name</string>

    <string name="custom_host">foo</string><!-- any value is fine -->
    <string name="azure_b2c_scheme">msauth</string>
    <string name="azure_b2c_signature_hash">/pedl56546efAZDd5zaz4az=</string><!-- Your hask key. The leading slash is required. Copied from Azure Portal Android Config "Signature hash" field -->
</resources>
````
[Back to top](#azure)     

#### Add the following into your android/app/build.gradle

````
apply plugin: 'com.android.application

android {
    compileSdkVersion rootProject.ext.compileSdkVersion
    defaultConfig {
        ...
        manifestPlaceholders = [appAuthRedirectScheme: 'com.myapp.name']	// <-- add this placeholder
    }
    buildTypes {
        ...
    }
}
repositories {
    ...
}
dependencies {
    ...
}
apply from: 'capacitor.build.gradle'

````
[Back to top](#azure)     

### Usage

*auth.service.ts*

````typescript
import { environment } from 'src/environments/environment';
import { Injectable } from '@angular/core';
import { OAuth2AuthenticateOptions } from '@byteowls/capacitor-oauth2';

@Injectable({
  providedIn: 'root'
})
export class AuthService {

  constructor() { }

  getAzureB2cOAuth2Options(): OAuth2AuthenticateOptions {
    return {
        appId: environment.authentication.clientId,
        authorizationBaseUrl: `${environment.authentication.authority}/${environment.authentication.tenant}/oauth2/v2.0/authorize`,
        scope: environment.authentication.apiName, // See Azure Portal -> API permission
        accessTokenEndpoint: `${environment.authentication.authority}/${environment.authentication.tenant}/oauth2/v2.0/token`,
        resourceUrl: null,
        responseType: 'code',
        pkceEnabled: true,
        logsEnabled: true,
        web: {
            redirectUrl: environment.authentication.redirectUriWeb,
            windowOptions: 'height=600,left=0,top=0',
        },
        android: {
          redirectUrl: `${environment.authentication.redirectUriMsauth}`,
          responseType: 'code',
          handleResultOnNewIntent: true,
          handleResultOnActivityResult: true
        },
        ios: {
            pkceEnabled: true, // workaround for bug #111
            redirectUrl: 'msauth.com.myapp.name://auth'
        }
    };
  }
}
````

*environment.ts*

````typescript
export const environment = {
  authentication: {
    clientId: 's2az5r-d1q5-s4fe8-z54r-d4d5faz5e4aee',
    redirectUri: 'com.myapp.name://dashboard',
    redirectUriMsauth: 'msauth://com.myapp.name/pedl56546efAZDd5zaz4az=',
    redirectUriWeb: 'http://localhost:8100',
    tenant: 'mycustom.onmicrosoft.com',
    apiName: 'acces_api',
    authority: 'https://login.microsoftonline.com'
  },
  production: false
};
````

*home.page.html*

````html
<ion-button (click)="login()">login</ion-button>
<ion-button (click)="refresh()">refresh</ion-button>
<ion-button (click)="logout()">logout</ion-button>

<h4>Access token</h4>
<p>{{ accessToken | slice:0:150 }}</p>

<h4>Refresh token</h4>
<p>{{ refreshToken | slice:0:150 }}</p>
````

*home.page.ts*

````typescript
import {OAuth2Client} from '@byteowls/capacitor-oauth2';
import { SecureStoragePlugin } from 'capacitor-secure-storage-plugin';
import { AuthService } from './../shared/services/core/authentication/auth.service';

accessToken = '';
refreshToken = '';

constructor(
    private documentHelper: DocumentHelperService,
    private authService: AuthService) {}

  async ngOnInit() {
    await SecureStoragePlugin.get({key: 'my_token'})
    .then(res => this.accessToken = res.value)
    .catch(err => this.accessToken = '');
  }

  login() {
    const opt = this.authService.getAzureB2cOAuth2Options();
    OAuth2Client.authenticate(
      opt
    ).then(response => {
      console.log('login response', response);

      this.accessToken = response['access_token'];
      this.refreshToken = response['refresh_token'];
      SecureStoragePlugin.set({ key: 'my_token', value: this.accessToken });

      // only if you include a resourceUrl protected user values are included in the response!
      const oauthUserId = response['id'];
      const name = response['name'];

        // go to backend
    }).catch(reason => {
        console.error('OAuth rejected', reason);
    });
  }
  logout() {
    OAuth2Client.logout(
      this.authService.getAzureB2cOAuth2Options()
    ).then(() => {
        // do something
        SecureStoragePlugin.remove({ key: 'my_token' });
        this.accessToken = '';
    }).catch(reason => {
        console.error('OAuth logout failed', reason);
    });
  }
  refresh() {
    if (!this.refreshToken) {
      console.error('No refresh token found. Log in with OAuth first.');
    }

    const opt = this.authService.getAzureB2cOAuth2Options();
    OAuth2Client.refreshToken(
      {
        appId: opt.appId,
        accessTokenEndpoint: opt.accessTokenEndpoint,
        refreshToken: this.refreshToken,
        scope: opt.scope
      }
    ).then(response => {
      this.accessToken = response['access_token'];
      // Don't forget to store the new refresh token as well!
      this.refreshToken = response['refresh_token'];
      // Go to backend
    }).catch(reason => {
        console.error('Refreshing token failed', reason);
    });
  }
````
[Back to top](#azure)     
