[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# PDF Export

* [html2pdf](#html2pdf)     
* [css print](#css-print)        

## html2pdf

### Installation

https://www.npmjs.com/package/html2pdf.js/v/0.9.0

````
npm i html2pdf.js
````

### Resources

https://www.youtube.com/watch?v=7LoWmGMB7e4

### Usage

In this example, we have a div **#capture** which containing 2 charts. The goal is to export the 2 chart into pdf file

*View File*

````
<ion-header>
  <ion-toolbar>
    <ion-title>chart</ion-title>
    <ion-buttons slot="end">
      <ion-button (click)="onExport()">
        export
      </ion-button>
    </ion-buttons>
  </ion-toolbar>
</ion-header>

<ion-content>
  <div #capture>
    <div id="block_container">
      <div class="line-chart-div">
        <canvas #lineCanvas></canvas>
      </div>
      <div class="line-chart-div2">
        <canvas #barCanvas></canvas>
      </div>
    </div>
  </div>
</ion-content>

````

*controller file*

````
import { Component, OnInit, ViewChild, ElementRef } from '@angular/core';
import { Chart } from 'chart.js';
import 'chartjs-top-round-bar';
import * as html2pdf from 'html2pdf.js';

@Component({
  selector: 'app-chart',
  templateUrl: './chart.page.html',
  styleUrls: ['./chart.page.scss'],
})
export class ChartPage implements OnInit {
  @ViewChild('barCanvas', {static: true}) barCanvas;
  barChart: any;
  @ViewChild('lineCanvas', {static: true}) lineCanvas;
  lineChart: any;

  @ViewChild('capture', {static: true}) capture: ElementRef;

  constructor() { }

  ngOnInit() {
  // Initialize chart's data
    this.generateLineChart();
    this.generateBarChart();
  }

  /**
   * Export view to PDF
  **/
  onExport() {

    const options = {
      filename: 'capture-test.pdf',
      imge: {type: 'jpg'},
      html2canvas: {},
      jsPDF: {orientation: 'landscape'}
    };

    html2pdf()
      .from(this.capture.nativeElement)
      .set(options)
      .save();
  }
  generateBarChart() {
    // some stuff here
  }

  generateLineChart() {
    // some stuff here
  }

}

````

## css print

[back to top](#pdf-export)      

https://www.alsacreations.com/tuto/lire/586-feuille-style-css-print-impression.html       
https://www.sitepoint.com/css-printer-friendly-pages/       
https://timdeschryver.dev/blog/print-css-with-angular     

### Dummy sample

It is recommanded to create a new specific scss stylesheet for print css inside your component in ordrer to keep clarity in your code.

If you want to save your page as PDF document, after clicking on "print" button, you may choose "save -> save as PDF" in the native print dialog.  

*home.html*

````
<ion-header [translucent]="true">
  <ion-toolbar>
    <ion-title>
      Blank
    </ion-title>
    <ion-buttons slot="end">
      <ion-button (click)="print()">print</ion-button>
    </ion-buttons>
  </ion-toolbar>
</ion-header>

<ion-content [fullscreen]="true">
  <h1>Print test with css print</h1>
  <p>lorem ipsum dolor lorem ipsum dolorlorem ipsum dolorlorem ipsum dolor
    lorem ipsum dolorlorem ipsum dolorlorem ipsum dolorlorem ipsum dolorlorem ipsum dolor
    lorem ipsum dolorlorem ipsum dolorlorem ipsum dolorlorem ipsum dolorlorem ipsum dolor
    lorem ipsum dolorvlorem ipsum dolorlorem ipsum dolorlorem ipsum dolorlorem ipsum dolorlorem ipsum dolor
    lorem ipsum dolorlorem ipsum dolorlorem ipsum dolorlorem ipsum dolorlorem ipsum dolorlorem ipsum dolor
    lorem ipsum dolorlorem ipsum dolorlorem ipsum dolorlorem ipsum dolor
  </p>
  <ion-grid>
    
    <ion-row>
      <ion-col [style.background]="'green'"></ion-col>
    </ion-row>
    <ion-row>
      <ion-col size="4" [style.background]="'gold'">col 1</ion-col>
      <ion-col size="4" [style.background]="'red'">col 2</ion-col>
      <ion-col size="4" [style.background]="'violet'">col 3</ion-col>
    </ion-row>
    <ion-row>
      <ion-col size="4" [style.background]="'red'">col 1</ion-col>
      <ion-col size="4" [style.background]="'violet'">col 2</ion-col>
      <ion-col size="4" [style.background]="'gold'">col 3</ion-col>
    </ion-row>
    <ion-row>
      <ion-col size="4" [style.background]="'violet'">col 1</ion-col>
      <ion-col size="4" [style.background]="'gold'">col 2</ion-col>
      <ion-col size="4" [style.background]="'red'">col 3</ion-col>
    </ion-row>
    <ion-row>
      <ion-col [style.background]="'purple'"></ion-col>
    </ion-row>
  </ion-grid>
</ion-content>

````

*home.ts*

````
import { Component } from '@angular/core';

@Component({
  selector: 'app-home',
  templateUrl: 'home.page.html',
  styleUrls: ['home.page.scss', 'home-print.page.scss'], // <==== link the new specific print stylesheet
})
export class HomePage {

  constructor() {}
  print() {
    window.print();
  }
}

````

*home.scss*

````
#container {
  text-align: center;

  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;

  color: #8c8c8c;

  margin: 0;
}

#container a {
  text-decoration: none;
}
````

*home-print.scss*

````
@media print {
    h1 {
        color: blue;
        padding-left: 50px;
    }
    p {
        padding: 50px;
    }
    ion-grid {
        padding: 50px;
    }
    ion-toolbar {
        display: none;
    }
    ion-content {
        --color: black;
        --background: white;
        background-color: white;
    }
    ion-header {
        &.header-md:after {
            background: none !important;
          }
    }
}
````
