# Unit testing
[Back to top](#ionic-2)

[link : Unit testing with karma](http://www.roblouie.com/article/376/ionic-2-set-up-unit-testing-the-best-way/)    
[link : Angular doc](https://angular.io/docs/ts/latest/guide/testing.html)    

*First install Karma*

```
npm install -g karma-cli
```

Install dependencies for Jasmine and Karma, including some loaders for our Webpack config and Jasmine and Node types to keep Typescript happy.

```
npm install --save-dev @types/jasmine@2.5.41 @types/node html-loader jasmine karma karma-webpack ts-loader karma-sourcemap-loader karma-jasmine karma-jasmine-html-reporter angular2-template-loader karma-chrome-launcher null-loader
```

*Configure webpack*
You’ll need a new ‘test-config’ folder containing three files that handle that configuration in the root of your project.

/test-config/karma.conf.js
/test-config/karma-test-shim.js
/test-config/webpack.test.js

Then add the next command to your package.json

```"test": "karma start ./test-config/karma.conf.js"```

And finally, add your test files to the excludes section of your tsconfig so they aren’t compiled during the regular build.

```"exclude": [
  "node_modules",
  "src/**/*.spec.ts"
],
```
You can now run your tests with ```npm test```

