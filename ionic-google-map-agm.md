[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Google Map integration with agm-core

[Official agm-core documentation](https://angular-maps.com/api-docs/agm-core/)    

## Map options

### disable default UI buttons

According to the google map documentation, you can disable default UI buttons (like zoom, streetview...) with the *disableDefaultUI* options.

However, with agm-core,the disableDefaultUI is apparently not working. So you have to disable each control himself :

*use*
```
[zoomControl]="false"
[streetViewControl]="false"
```

*instead of*
```[disableDefaultUI]="true"```

