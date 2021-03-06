[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Debug Ionic Application on VSCode

* [Debug with cordova tools](#debug-with-cordova-tools)     
* [Debug with chrome](#debug-with-chrome)    

[Debugging Ionic App with Chrome for VSCode](http://www.damirscorner.com/blog/posts/20161122-DebuggingIonic2AppsInChromeFromVisualStudioCode.html)    

## Debug with cordova tools

First install *Crodova Tools* VSCode extension (this will create a launch.json file). Then in the Debug section click on the debug run combobox down arrow (near the play button) and select **Add a configuration** and select **Cordova: Serve** Option to create a serve debug configuration.

Now you just need to add breakpoints in your code and run debug by clicking on the play button in the debug section.


## Debug with chrome

First, install *Debugger for Chrome* VSCode extension, then add the following lines in your *package.json* 

```
"config": {
  "ionic_source_map": "source-map"
},
```

Then, we need to configure it, just press **F5** with the Ionic project folder as current workspace and select *Chrome* as target environment.

This will create a new *.vscode/launch.json* configuration file. You only need to modify the webRoot value of the launch configuration to point to the www folder and change the port in url value to 8100. Feel free to rename the configuration to your liking. I also removed the attach configuration since I don't plan on using it. These are the final contents of my launch.json file:

```
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Launch in Chrome",
      "type": "chrome",
      "request": "launch",
      "url": "http://localhost:8100",
      "sourceMaps": true,
      "webRoot": "${workspaceRoot}/www"
    }
  ]
}
```
Now you just have to add break points in your code and press **F5** to launch the debugger (kill all other ionic serve instances before launching debugger).

