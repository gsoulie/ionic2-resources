[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# PWA

## Resources

[ionic blog post](http://blog.ionic.io/how-to-make-pwas-with-ionic/)     
[link : josh morony tutorial PWA](https://www.joshmorony.com/the-bare-necessities-progressive-web-apps-in-ionic/)    
[link : josh morony tutorial](https://www.joshmorony.com/preparing-a-progressive-web-application-for-production/)    

[discussion with firebase hosting](https://forum.ionicframework.com/t/building-for-browsers-pwa/72689)    
[discussion](https://forum.ionicframework.com/t/building-for-browsers-pwa/72689/2)    
[discussion](https://forum.ionicframework.com/t/pwa-my-experience-creating-a-pwa-with-ionic-from-scratch-to-deployment/94541)    
[discussion](https://forum.ionicframework.com/t/deploying-pwa/73749)   
[firebase hosting](https://coryrylan.com/blog/deploy-angular-cli-apps-to-firebase)   

## Browser support

what functionalities are already supported by browser ?
[to open on wanted web browser](https://whatwebcando.today/)    

## Step 1

Add browser platform with ```ionic cordova platform add browser```    

## Step 2

Uncomment the following code in the index.html to activate service worker to enabling caching resources

```
<script>
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('service-worker.js')
      .then(() => console.log('service worker installed'))
      .catch(err => console.error('Error', err));
  }
</script>
```

## Step 3

If you need to caching some resources, add them in ```service-worker.js```

## Step 4

Run with ```ionic cordova run browser``` to test the site

**favicon** the favicon must be placed in the src/assets/icon/ folder, then use ionic cordova resources --icon

## Step 5 Hosting and deploying on Firebase

The first thing you need to do is to **create a new project in Firebase**, then navigate in the Hosting option and click on **Get started**. You will be promped to install *firebase-tool* 

```
npm install -g firebase-tools
```

And follow the steps below :

```
firebase login

firebase init
--> select "hosting" option and make sure to hit SPACE then ENTER !!

What do you want to use as your public directory ?
--> platforms/browser/www

Configure as a single-page app ?
--> Yes

File www/index.html already exists. Overwrite ?
--> No

// Build your application
ionic cordova build browser --prod

firebase deploy
```

