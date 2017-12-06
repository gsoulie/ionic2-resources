# PWA
[Back to top](#ionic-2)   

[ionic blog post](http://blog.ionic.io/how-to-make-pwas-with-ionic/)    

**1** Add browser platform with ```ionic cordova platform add browser```    

**2** Uncomment the following code in the index.html to activate service worker to enabling caching resources

```
<script>
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('service-worker.js')
      .then(() => console.log('service worker installed'))
      .catch(err => console.error('Error', err));
  }
</script>
```

**3** If you need to caching some resources, add them in ```service-worker.js```

**4** Run with ```ionic cordova run browser``` to test the site

**favicon** the favicon must be placed in the src/assets/icon/ folder, then use ionic cordova resources --icon

**Steps for hosting and deploying on Firebase**

```
Create your firebase project on Firebase, then

$ionic cordova build browser --prod

$npm install -g firebase-tools

$firebase login

$firebase init
--> select "hosting"

Choose de default directory :
--> platforms/browser/www

single page ?
--> yes

overwrite index.html 
--> no

firebase deploy
```
[josh morony tutorial](https://www.joshmorony.com/the-bare-necessities-progressive-web-apps-in-ionic/)    
[discussion with firebase hosting](https://forum.ionicframework.com/t/building-for-browsers-pwa/72689)    
[discussion](https://forum.ionicframework.com/t/building-for-browsers-pwa/72689/2)    
[discussion](https://forum.ionicframework.com/t/pwa-my-experience-creating-a-pwa-with-ionic-from-scratch-to-deployment/94541)    
[discussion](https://forum.ionicframework.com/t/deploying-pwa/73749)   
[firebase hosting](https://coryrylan.com/blog/deploy-angular-cli-apps-to-firebase)    