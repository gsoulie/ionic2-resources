[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Directive

* [Attributes directives](#attributes-directives)       

## Attributes directives

https://www.learn-angular.fr/les-directives/         
https://www.digitalocean.com/community/tutorials/angular-using-renderer2          

Create directive with ````ng g d directives/<your-directive>```` and import it into your app.module.ts file

### Example 1

*directive.ts*

````
import { Directive, ElementRef, Renderer2, Input, HostListener } from '@angular/core';

@Directive({
  selector: '[appColor]'
})
export class ColorDirective {

  @Input('appColor') highlightColor: string;

  private _defaultColor = 'blue';

  // Directive permettant de changer la couleur d'un élément
  constructor(private el: ElementRef, private renderer: Renderer2) {
    renderer.setStyle(el.nativeElement, 'color', this._defaultColor);
  }

  // mouseenter listener
  @HostListener('mouseenter', ['$event']) onMouseEnter(event: Event) {
    this.renderer.setStyle(this.el.nativeElement, 'color', this.highlightColor);
  }

  // mouseleave listener
  @HostListener('mouseleave', ['$event']) onMouseLeave(event: Event) {
    this.renderer.setStyle(this.el.nativeElement, 'color', this._defaultColor);
  }

}

````

*app.component.html*

````
<div mat-subheader [appColor]="'red'">Directives typescript</div>
````
