[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Common errors

* [Typescript error](#typescript-error)  

## Typescript error

Sometimes, you can get typescript error when you try to build / run with *--prod* flag

```
[13:48:44]  typescript error
            'ion-icon' is not a known element: 1. If 'ion-icon' is an Angular component, then verify that it is part of
            this module. 2. If 'ion-icon' is a Web Component then add 'CUSTOM_ELEMENTS_SCHEMA' to the
            '@NgModule.schemas' of this component to suppress this message. (" <ion-navbar color="secondary"> <button
            ion-button (click)="dismiss()"> [ERROR ->]<ion-icon name="md-arrow-back"></ion-icon> </button>
            <ion-title>Device List</ion-title> ")

[13:48:44]  typescript error
            'ion-title' is not a known element: 1. If 'ion-title' is an Angular component, then verify that it is part
            of this module. 2. If 'ion-title' is a Web Component then add 'CUSTOM_ELEMENTS_SCHEMA' to the
            '@NgModule.schemas' of this component to suppress this message. (" <ion-icon
            name="md-arrow-back"></ion-icon> </button> [ERROR ->]<ion-title>Device List</ion-title> </ion-navbar>
            </ion-header> ")

[13:48:44]  typescript error
            'ion-navbar' is not a known element: 1. If 'ion-navbar' is an Angular component, then verify that it is part
            of this module. 2. If 'ion-navbar' is a Web Component then add 'CUSTOM_ELEMENTS_SCHEMA' to the
            '@NgModule.schemas' of this component to suppress this message. ("<ion-header> [ERROR ->]<ion-navbar
            color="secondary"> <button ion-button (click)="dismiss()"> <ion-icon name="md-a")
```

**This is due to a bad module import. **

For example, if you create a page with ```ionic -g page <my-page>``` a new *my-page.component.ts* file is created.
If you forget to delete it, you will get the error above when you will try to build / run with prod mode.
