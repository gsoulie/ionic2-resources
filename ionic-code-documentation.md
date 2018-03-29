[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Code documentation

## Typedoc

Generate project documentation with *typedoc* 

```
$ npm install --global typedoc
```

Then, move into the root of your project and generate the documentation with the following command

```
typedoc --out "./docs" --mode file --target ES6 --ignoreCompilerErrors "./src" --includes "./src"
```

You can also create a batch file like 

```
@echo off
rmdir /S /Q docs
typedoc --out "./docs" --mode file --target ES6 --ignoreCompilerErrors "./src" --includes "./src"
@echo "Doc generation finished"
```
