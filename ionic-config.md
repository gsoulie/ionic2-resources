[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Config
[Back to top](#ionic-2)  

The Config lets you configure your entire app or specific platforms. You can set the tab placement, icon mode, animations, and more for each platform. 

[link : Config official documentation](http://ionicframework.com/docs/v2/api/config/Config/)

*app.module.ts*

```javascript
import { IonicApp, IonicModule } from 'ionic-angular';

@NgModule({
  declarations: [ MyApp ],
  imports: [
    IonicModule.forRoot(MyApp, {
      backButtonText: 'Go Back',
      iconMode: 'ios',
      modalEnter: 'modal-slide-in',
      modalLeave: 'modal-slide-out',
      tabsPlacement: 'bottom',
      pageTransition: 'ios'
    }, {}
  )],
  bootstrap: [IonicApp],
  entryComponents: [ MyApp ],
  providers: []
})
```

Or multiple config :

```javascript
import { IonicApp, IonicModule } from 'ionic-angular';

@NgModule({
  declarations: [ MyApp ],
  imports: [
    IonicModule.forRoot(MyApp, {
      platforms: {
      	ios: {
		backButtonText: 'Go Back',
      		iconMode: 'ios',
      		modalEnter: 'modal-slide-in',
      		modalLeave: 'modal-slide-out',
      		tabsPlacement: 'bottom',
      		pageTransition: 'ios'
	},
	android: {
	
	}
      }
      
    }, {}
  )],
  bootstrap: [IonicApp],
  entryComponents: [ MyApp ],
  providers: []
})
```

## Mapping
[Back to top](#ionic-2)

In Javascript, mapping is a method available on arrays which allows you to “map” or “transform” each value in that array. Keep in mind that in the example above we are not mapping an array, we are mapping an observable – this functionality is provided by the RxJS library (which is included in Ionic 2 & Angular 2), it is not in Javascript by default.

To give you an example of how normal array mapping works (the concept is basically the same for observables), I could take an array of the following numbers:

**1**
```
[1, 2, 3, 4, 5]
```
and map the array using a function that increments each value by one, which would transform the array into the following:

**2**
```
[2, 3, 4, 5, 6]
```

This would look something like this:
```
[0, 1, 2, 3, 4, 5].map(function(item){
    return item+1;
});
```

We supply map with a function, and then the map will run that function for every value in the array. Whatever we return from that function is what the new value of the item will be – in this case, that is the same number incremented by one. If we instead did something like return 5;, it would turn every value in the array into a 5.

We use map to transform the response into something we can work with. To do that, we create a function that will be applied to each item returned from our observable (which is just the single Response object in this case). The function looks like this:

```javascript
res => res.json()
```

which is shorthand for:

```javascript
(res) => {
    return res.json();
}
```

**Remember** that map is a method that is available on arrays anywhere, it doesn’t need to be inside of an observables map function, and it doesn’t even need to be inside of an Ionic or Angular project

As an aside, filtering and reducing are also available on both observables and arrays, and like map they also return a new observable, so you can create long complex chains like this:

```javascript
this.http.get('location/of/data')
    .map(res => res.json())
    .filter(/*some function*/)
    .reduce(/*some function*/)
    .subscribe(data => {
    console.log(data);
});
```

**filter** can be used to remove values from an array, and **reduce** can be used to calculate some value based on all the values in an array.

```javascript
[0, 1, 2, 3, 4, 5].filter((item) => {
    return item % 2 === 0;
});

//return [0, 2, 4]

['cats', 'kittens', 'puppies', 'dogs'].filter((item) => {
    return item.length < 5;
});

//return ['cats', 'dogs']
```

**Reduce**

Like map and filter, reduce also applies a function to each value in the array, but it doesn’t necessarily result in a new array. Reduce not only supplies you with the value of the current value being operated on, but also the previous value that was returned. This allows you to either compare values in the array, or combine them in some way.

We could create the following reduce function to figure out the highest number in an array:

```javascript
[0, 1, 2, 3, 4, 5].reduce((previous, current) => {
    return Math.max(previous, current);
});
```

The above example compares the current and previous value to see which is higher and then passes that value into the next iteration of the function. The result will be the highest number, which is 5.

We could also figure out the sum of all numbers in the array with the following function:

```javascript
[0, 1, 2, 3, 4, 5].reduce((previous, current) => {
    return current + previous;
});
```
Now with each iteration of the function, the sum of the previous and current will be passe along, resulting in a sequence like:

```
0 + 1 = 1
1 + 2 = 3
3 + 3 = 6
6 + 4 = 10
10 + 5 = 15
```
So, the result of the reduce function will be 15 in this case.

Note that you can also supply an initial value for previous, rather than using the first value of the array, if you add an additional parameter as shown below:

```javascript
[0, 1, 2, 3, 4, 5].reduce((previous, current) => {
    // do something...
}, 5);
```
