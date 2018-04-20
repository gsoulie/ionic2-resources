[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Google Map integration with agm-core

[Official agm-core documentation](https://angular-maps.com/api-docs/agm-core/)    

## Create Google Map API Key

The first thing to use Google Map is to create a Google Map API Key [get api key](https://developers.google.com/maps/documentation/javascript/get-api-key).

Click on the **Obtain Key** button or :
1. Access to the *Google API Console*
2. Create or select a project
3. Click to continue to activate API and other services
4. On the Credentials page, get your API key and define the restrictions of the key

**Important** You need to activate **Google Map Javascript API** and **Google Map Geocoding API**

## Map options

### disable default UI buttons

According to the [google map documentation](https://developers.google.com/maps/documentation/javascript/examples/control-disableUI?hl=fr), you can disable default UI buttons (like zoom, streetview...) with the **disableDefaultUI** options.

However, with agm-core,the disableDefaultUI is apparently not working. So you have to disable each control himself :

*use*
```
[zoomControl]="false"
[streetViewControl]="false"
```

*instead of*
```
[disableDefaultUI]="true"
```

