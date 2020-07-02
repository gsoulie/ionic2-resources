[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# html2PDF

* [Start with ionic](#start-with-ionic)  
* [Visual Studio Code](#visual-studio-code)   
* [Android debugging with logcat](#android-debugging-with-logcat)    

## Installation

https://www.npmjs.com/package/html2pdf.js/v/0.9.0

````
npm i html2pdf.js
````

## Tutorial

https://www.youtube.com/watch?v=7LoWmGMB7e4

## Usage

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
