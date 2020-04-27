[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Charts

* [Documentation](#documentations)    
* [ChartJS](#chartjs-sample-code)    
* [Chart grid 50%](#chart-grid-50%)    
* [World map ng2-google-chart](#ng2-google-chart)    

## Documentation
[Back to top](#charts)  

[Chart.js](http://www.chartjs.org/)    
[Chart.js API](http://www.chartjs.org/docs/latest/charts/)    
[D3js](https://www.d3-graph-gallery.com/)    
[ChartistJS](http://gionkunz.github.io/chartist-js/)

[Josh morony chartJS](https://www.joshmorony.com/adding-responsive-charts-graphs-to-ionic-2-applications/)    
[link : ng2-chart](http://valor-software.com/ng2-charts/)    
[forum : related post](https://forum.ionicframework.com/t/solved-ionic-2-ng2-charts/42926)    

## ChartJS Sample code

### Installation

````
npm install chart.js --save
````

*View.html*

```
<ion-header>
  <ion-navbar color="dark">
    <ion-title>
      ChartJS
    </ion-title>
  </ion-navbar>
</ion-header>
<ion-content padding>
  <ion-item [style.backgroundColor]="'transparent'" lines="none">
    <ion-icon slot="start" name="md-tablet-landscape" color="dark"></ion-icon>
    <b slot="end">Basculez en mode paysage pour voir le graphe</b>
  </ion-item>
  <div *ngIf="orientationPortrait">  
    <ion-item *ngFor="let i of dataList">{{ i }}</ion-item>
  </div>
  <ion-card no-padding *ngIf="!orientationPortrait">
    <ion-card-header>
      Weight chart
    </ion-card-header>
    <ion-card-content>
      <canvas #lineCanvas></canvas>
    </ion-card-content>
  </ion-card>
</ion-content>
```

*Controller.ts*

```javascript
import { Component, ViewChild, OnInit } from '@angular/core';
import { NavController, NavParams } from 'ionic-angular';
import { Chart } from 'chart.js';
import { ScreenOrientation } from '@ionic-native/screen-orientation';
import { ChangeDetectorRef } from '@angular/core';

@Component({
  selector: 'page-chart',
  templateUrl: 'chart.html',
})
export class ChartPage implements OnInit{

  @ViewChild('lineCanvas', {static: true}) lineCanvas;
  lineChart: any;
  orientation;
  orientationPortrait: boolean = true;
  dataList = [13.2, 13.5, 14.2, 13.8, 13.5, 13.4, 13.7];

  constructor(public navCtrl: NavController, 
    public navParams: NavParams, 
    private screenOrientation: ScreenOrientation,
    private ref: ChangeDetectorRef) {
  }

  ngOnInit(){
    
    this.screenOrientation.onChange().subscribe(
      () => {
          this.orientation = this.screenOrientation.type
          switch(this.screenOrientation.type){
            case this.screenOrientation.ORIENTATIONS.LANDSCAPE :
            case this.screenOrientation.ORIENTATIONS.LANDSCAPE_PRIMARY :
            case this.screenOrientation.ORIENTATIONS.LANDSCAPE_SECONDARY :
              this.orientationPortrait = false;
              this.ref.detectChanges();// force refresh
              this.onGenerateChart();
              this.ref.detectChanges();// force refresh
              break;
            case this.screenOrientation.ORIENTATIONS.PORTRAIT :
            case this.screenOrientation.ORIENTATIONS.PORTRAIT_PRIMARY :
            case this.screenOrientation.ORIENTATIONS.PORTRAIT_SECONDARY :
              this.orientationPortrait = true;
              this.ref.detectChanges();// force refresh
              break;
            default:
              this.orientationPortrait = true;
              this.ref.detectChanges(); // force refresh
              break;
          }
      }
   );
  }

  ionViewDidLoad() {
    this.onGenerateChart();
  }

  onGenerateChart(){
    // fill the chart with gradient
    //https://developer.mozilla.org/fr/docs/Web/API/CanvasRenderingContext2D/createLinearGradient
    var ctx = this.lineCanvas.nativeElement.getContext("2d");
    var grd=ctx.createLinearGradient(0,0,0,200);
    grd.addColorStop(0,"#cc1e1e");
    grd.addColorStop(1,"white");

    this.lineChart = new Chart(this.lineCanvas.nativeElement, {
 
      type: 'line',
      data: {
          labels: ["January", "February", "March", "April", "May", "June", "July"],
          datasets: [
              {
                  label: "weight",
                  fill: true, // remplissage de la zone sous le graphe
                  lineTension: 0.5, // lissage de la courbe
                  backgroundColor: grd,//"rgba(0,0,0,0.4)",
                  borderColor: "rgba(0,0,0,1)",
                  borderCapStyle: 'butt',
                  borderDash: [],
                  borderDashOffset: 0.0,
                  borderJoinStyle: 'miter',
                  pointBorderColor: "rgba(0,0,0,1)",
                  pointBackgroundColor: "#fff",
                  pointBorderWidth: 1,
                  pointHoverRadius: 5,
                  pointHoverBackgroundColor: "rgba(0,0,0,1)",
                  pointHoverBorderColor: "rgba(220,220,220,1)",
                  pointHoverBorderWidth: 2,
                  pointRadius: 1,
                  pointHitRadius: 10,
                  data: this.dataList,
                  spanGaps: false
              }
          ]
      }
   });
  }
}
```
*Style.scss*

```
page-chart {
    .scroll-content {
        background-color: map-get($colors, light);
    }

    ion-card-header {
        font-weight: bold;
    }
}
```

### Pie chart
[Back to top](#charts)  

*view file* 
```
<canvas #myCanvas></canvas>
```

*controller file*
```
export class MyClass {
	@ViewChild('myCanvas', {static: true}) myCanvas: ElementRef;
	
	initChart() {
		const dataset = { 
			datasets: [{
				data: [5, 6, 18],
				backgroundColor: ['#ffcc55', '#a51231', '#44ff22']
			}],
			labels: ['Red', 'Blue', 'Green']
		};
		
		const ctx = this.myCanvas.nativeElement;
		
		const myChart = new Chart(ctx,{
			type: 'pie',
			data: dataset,
			options: options
		});
	}
}
```

**Define chart height**

```
const ctx = this.myCanvas.nativeElement;
ctx.height = 150;

const myChart = new Chart(ctx,{
	type: 'pie',
	data: dataset,
	options: {
		maintainAspectRatio: false,
	}
});
```

### Customization
[Back to top](#charts)  

https://blog.vanila.io/chart-js-tutorial-how-to-make-gradient-line-chart-af145e5c92f9
https://codepen.io/nzuki/pen/oVmOOd
https://medium.com/@ValeriaCortezVD/tutorial-this-is-why-you-should-use-gradient-charts-with-chart-js-f5be20e39c92

**Gradient**

````
gradientStroke.addColorStop(0, firstColour);
gradientStroke.addColorStop(0.3, secondColour);
gradientStroke.addColorStop(0.6, thirdColour);
gradientStroke.addColorStop(1, fourthColour);
````

The *addColorStop(stop, colour)* method specifies the colours and their position **(0–1)** in the gradient object.


### Full example
[Back to top](#charts)  

In this example we create 1 line chart and two bar chart.

*Controller file*

````
import { Component, OnInit, Input, ViewChild } from '@angular/core';
import { Chart } from 'chart.js';

@Component({
  selector: 'app-view-analytics',
  templateUrl: './view-analytics.component.html',
  styleUrls: ['./view-analytics.component.scss'],
})
export class ViewAnalyticsComponent implements OnInit {

  @Input() dataset: any[] = [];
  @ViewChild('lineCanvas', {static: true}) lineCanvas;
  @ViewChild('barCanvas1', {static: true}) barCanvas1;
  @ViewChild('barCanvas2', {static: true}) barCanvas2;
  lineChart: any;
  barChart1: any;
  barChart2: any;
  lineChartTitle = 'Results by year';
  barChart1Title = 'Top NPI';
  barChart2Title = 'Top Outcomes';
  dataList = [5400, 5000, 4790, 4100, 3750, 3200, 2530, 2240, 2050];

  constructor() { }

  ngOnInit() {
    this.refreshChart();
  }

  ngAfterContentInit() {
    console.table(this.dataset);
  }
  updateData(data) {
    this.dataset = data;
    console.table(this.dataset);
  }

  refreshChart() {
    this.generateLineChart();
    this.generateBarChart1();
    this.generateBarChart2();
  }

  generateLineChart() {
    const ctx = this.lineCanvas.nativeElement.getContext('2d');
    
    this.lineChart = new Chart(this.lineCanvas.nativeElement, {
      type: 'line',
      data: {
          labels: ['2012', '2013',
        '2014', '2015', '2016',
      '2017', '2018', '2019', '2020'],
          datasets: [
              {
                  data: [1200, 1600, 2020, 1990, 2390, 1850, 2100, 2000, 2600],
                  spanGaps: false,
                  borderCapStyle: 'round',
                  lineTension: 0.5,
                  fill: false,
                  backgroundColor: '#72B83B',
                  borderColor: '#72B83B',
                  pointRadius: 0,
                  borderJoinStyle: 'miter'
              }
          ]
      },
      options: {
        /*title: {
          display: true,
          text: this.lineChartTitle,
        },*/
        maintainAspectRatio: false,
        legend: {
          display: false
        },
        layout: {
          padding: {
              left: 20,
              right: 20,
              top: 20,
              bottom: 50
          }
        },
        scales: {
            xAxes: [{
              gridLines: {
                display: false
              },
              scaleLabel: {
                display: false // hide egend title on X
              },
              ticks: {
                autoSkip: false,
                maxRotation: 90,  // legend rotation
                minRotation: 90  // legend rotation
              }
            }]
        },
      },
    });
  }

  generateBarChart1() {
    var ctx = this.barCanvas1.nativeElement.getContext('2d');
    var grd = ctx.createLinearGradient(0, 0, 0, 400);
    grd.addColorStop(0, '#72B83B');
    grd.addColorStop(0.5, '#ADDB88');
    grd.addColorStop(1, '#E2FACE');
    this.barChart1 = new Chart(this.barCanvas1.nativeElement, {
      type: 'horizontalBar',
      data: {
          labels: ['Chinese herbal drugs', 'Cognitive behaviour therapy',
        'Health education', 'Physical activity', 'Screening',
      'Massage', 'Beta carotene', 'Psychotherapy', 'Behavior therapy'],
          datasets: [
              {
                  label: '',
                  fillColor: grd,
                  fill: true, 
                  backgroundColor: grd,
                  borderWidth: 0,
                  borderColor: 'rgba(255,99,132,1)',  // tooltip square border
                  borderCapStyle: 'round', //'butt',
                  borderDash: [],
                  barThickness: 'flex',
                  data: this.dataList,
                  spanGaps: false,
                  showTooltips: false
              }
          ]
      },
      options: {
        /*tooltips: {enabled: false},
        hover: {mode: null},*/
        legend: {
          display: false
        },
        layout: {
          padding: {
              left: 20,
              right: 50,
              top: 0,
              bottom: 10
          }
        },
        scales: {
            yAxes: [{
              gridLines: {
                display: false
              },
              scaleLabel: {
                display: false // hide legend on Y
              }
            }],
            xAxes: [{
              gridLines: {
                display: false
              },
              scaleLabel: {
                display: false // hide legend on X
              },
              ticks: {
                display: false // hide labels on X
              }
            }]
        },
        animation: {
          duration: 1,
          onComplete: () => {
            // Display values at the end of the bars
            const chartInstance = this.barChart1,
            ctx = chartInstance.ctx;
            ctx.font = Chart.helpers.fontString(Chart.defaults.global.defaultFontSize, Chart.defaults.global.defaultFontStyle, Chart.defaults.global.defaultFontFamily);
            ctx.textAlign = 'center';
            ctx.textBaseline = 'bottom';
            ctx.fillStyle = '#000';
            this.barChart1.data.datasets.forEach((dataset, i) => {
              const meta = chartInstance.controller.getDatasetMeta(i);
              meta.data.forEach((bar, index) => {
                const data = dataset.data[index];
                ctx.fillText(data, bar._model.x + 20, bar._model.y + 6);
              });
            });
          }
        },
      },
    });
  }

  generateBarChart2() {
    var ctx = this.barCanvas2.nativeElement.getContext('2d');
    var grd = ctx.createLinearGradient(0, 0, 0, 400);
    grd.addColorStop(0, '#EB5E62');
    grd.addColorStop(0.5, '#EE8E91');
    grd.addColorStop(1, '#F2CACB');
    this.barChart2 = new Chart(this.barCanvas2.nativeElement, {
      type: 'horizontalBar',
      data: {
          labels: ['Adaptation, psychological', 'Return to work',
                  'Quality of life', 'Self concept', 'Patient compliance',
                  'Nausea', 'Body composition',
                  'Anxiety', 'Gastrointestinal motility'],
          datasets: [
              {
                  label: '',
                  fillColor: grd,
                  fill: true, // fill under chart area
                  lineTension: 0.5, // curve smooth
                  backgroundColor: grd,
                  borderColor: 'rgba(255,99,132,1)',  // tooltip square border
                  borderCapStyle: 'round', //'butt',
                  borderDash: [],
                  borderWidth: 0,
                  barThickness: 'flex',
                  data: this.dataList,
                  spanGaps: false,
              }
          ]
      },
      options: {
        legend: {
          display: false
        },
        layout: {
          padding: {
              left: 20,
              right: 50,
              top: 0,
              bottom: 10
          }
        },
        scales: {
            yAxes: [{
              gridLines: {
                display: false
              },
              scaleLabel: {
                display: false // hide legend on Y
              }
            }],
            xAxes: [{
              gridLines: {
                display: false
              },
              scaleLabel: {
                display: false // hide legend on X
              },
              ticks: {
                display: false // hide labels on X
              }
            }]
        },
        animation: {
          duration: 1,
          onComplete: () => {
            // Display values at the end of the bars
            const chartInstance = this.barChart2,
            ctx = chartInstance.ctx;
            ctx.font = Chart.helpers.fontString(Chart.defaults.global.defaultFontSize, Chart.defaults.global.defaultFontStyle, Chart.defaults.global.defaultFontFamily);
            ctx.textAlign = 'center';
            ctx.textBaseline = 'bottom';
            ctx.fillStyle = '#000';
            this.barChart2.data.datasets.forEach((dataset, i) => {
              const meta = chartInstance.controller.getDatasetMeta(i);
              meta.data.forEach((bar, index) => {
                const data = dataset.data[index];
                ctx.fillText(data, bar._model.x + 20, bar._model.y + 6);
              });
            });
          }
        },
      },
    });
  }
}

````
[Back to top](#charts)  

*View file*

````
<div class="flex-container">
  <div class="flex-div">
    <div class="tile chart-div2">
      <div class="chart-title">
        <ion-label>{{ lineChartTitle }}</ion-label>
      </div>
      <canvas #lineCanvas></canvas>
    </div>
  </div>
  <div class="flex-div">
    <div class="tile chart-div2">
      <div class="chart-title">
        <ion-label>{{ barChart1Title }}</ion-label>
      </div>
      <canvas #barCanvas1></canvas>
    </div>
  </div>
  <div class="flex-div">
    <div class="tile chart-div2">
      <div class="chart-title">
        <ion-label>{{ barChart2Title }}</ion-label>
      </div>
      <canvas #barCanvas2></canvas>
    </div>
  </div>  
</div>
````
[Back to top](#charts)  


*Style file*

````
.tile {
    -webkit-box-shadow: 0px 0px 12px -4px rgba(0,0,0,0.75);
    -moz-box-shadow: 0px 0px 12px -4px rgba(0,0,0,0.75);
    box-shadow: 0px 0px 12px -4px rgba(0,0,0,0.75);
    border-radius: 10px;
}
.chart-title {
    padding-left: 24px;
    padding-top: 20px;
    font-size: 11pt;
    font-weight: bold;
    color: #555;

    float: left;
}
.chart-div2 {
    position: relative;
    margin: auto;
    height: 260px;
    width: 100%;
}
.flex-container {
    display: flex;
    flex-wrap: wrap;
    padding: none;
}
.flex-div {
    text-align: center;
    //line-height: 75px;
    //font-size: 30px;
    margin-right: 20px;
}
@media screen and (min-width: 1600px) {
    .flex-div {
        width: 31%;
    }
    .chart-div2 {
        height: 260px;
        margin-top: 0px;
    }
}
/* If the screen size is 600px wide or less, set the font-size of <div> to 30px */
@media screen and (max-width: 1600px) {
    .flex-div {
        width: 48%;
    }
    .chart-div2 {
        height: 320px;
        margin-top: 20px;
    }
}
/* If the screen size is 600px wide or less, set the font-size of <div> to 30px */
@media screen and (max-width: 1344px) {
    .flex-div {
        width: 47%;
    }
    .chart-div2 {
        height: 320px;
        margin-top: 20px;
    }
}
/* If the screen size is 600px wide or less, set the font-size of <div> to 30px */
@media screen and (max-width: 1024px) {
    .flex-div {
        width: 100%;
    }
    .chart-div2 {
        height: 400px;
        margin-top: 20px;
    }
}

````

## Chart grid 50%
[Back to top](#charts)  

Here is a sample code aimed to put 2 charts (line + bar) on the same grid line with 50% width each.

*View file*

````
<div id="block_container">
    <div class="left-chart-div">
      <canvas #lineCanvas></canvas>
    </div>
    <div class="right-chart-div">
      <canvas #barCanvas></canvas>
    </div>
</div>
````
[Back to top](#ui-components)  

*Style file*

````
.block_container {
    display: flex;
    justify-content: center;
}
.left-chart-div {
    width: 50%;
    padding: 10px;
    overflow: hidden;
    float: left;
}
.right-chart-div {
    float: right;
    width: 50%;
    padding: 10px;
    overflow: hidden;
}
````
[Back to top](#ui-components)  

*Controller file*

````
import { Component, OnInit, ViewChild, ElementRef } from '@angular/core';
import { Chart } from 'chart.js';
import 'chartjs-top-round-bar';

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
  
  dataList = [5400, 5000, 4790, 4100, 3750, 3200, 2530, 2240, 2050];


  horizontalBarChartData = {
    labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
    datasets: [{
      label: 'Dataset 1',
      backgroundColor: "#ffcc00",
      borderColor: 'red',
      borderWidth: 1,
      data: [
        1,2,3,4,5,6,7
      ]
    }, {
      label: 'Dataset 2',
      backgroundColor: "orange",
      borderColor: "orange",
      data: [
        8,9,10,11,12,13,14
      ]
    }]
  };
    
  constructor() { }

  ngOnInit() {
    this.generateLineChart();
    this.generateBarChart();
  }

  generateBarChart() {
    var ctx = this.barCanvas.nativeElement.getContext('2d');
    var grd = ctx.createLinearGradient(0, 0, 0, 400);
    grd.addColorStop(0, '#72B83B');
    grd.addColorStop(1, '#E2FACE');
    this.barChart = new Chart(this.barCanvas.nativeElement, {
      type: 'horizontalBar',
      data: {
          labels: ['Chinese herbal drugs', 'Cognitive behaviour therapy',
        'Health education', 'Physical activity', 'Screening',
      'Massage', 'Beta carotene', 'Psychotherapy', 'Behavior therapy'],
          datasets: [
              {
                  label: '',
                  fillColor: grd,
                  fill: true, // remplissage de la zone sous le graphe
                  lineTension: 0.5, // lissage de la courbe
                  backgroundColor: grd,
                  borderColor: 'rgba(255,99,132,1)',  // border du carré dans la tooltip
                  borderCapStyle: 'round', //'butt',
                  borderDash: [],
                  barThickness: 'flex', // largeur des barres. 'flex' = définition automatique de la largeur la plus appropriée
                  //barPercentage: 0.5,
                  borderDashOffset: 0.0,
                  borderJoinStyle: 'miter', // jointure de courbe (arrondie, plate ou pointue)
                  pointBorderColor: 'rgba(0,0,0,1)',
                  pointBackgroundColor: '#fff',
                  pointBorderWidth: 0,
                  pointHoverRadius: 5,
                  pointHoverBackgroundColor: 'rgba(0,0,0,1)',
                  pointHoverBorderColor: 'rgba(220,220,220,1)',
                  pointHoverBorderWidth: 2,
                  pointRadius: 1,
                  pointHitRadius: 10,
                  data: this.dataList,
                  spanGaps: false,
              }
          ]
      },
      options: {
        legend: {
          display: false
        },
        scales: {
            yAxes: [{
              gridLines: {
                color: 'white', // couleur des lignes de l'axe
                display: false
              },
              scaleLabel: {
                display: false // affichage de la légende des y
              }
            }],
            xAxes: [{
              gridLines: {
                color: 'white', // couleur des lignes de l'axe
              },
              scaleLabel: {
                display: false // affichage de la légende des x
              },
              ticks: {
                display: false // affichage des labels de l'axe des x
              }
            }]
        },
        animation: {
          duration: 1,
          onComplete: () => {
            const chartInstance = this.barChart,
            ctx = chartInstance.ctx;
            ctx.font = Chart.helpers.fontString(Chart.defaults.global.defaultFontSize, Chart.defaults.global.defaultFontStyle, Chart.defaults.global.defaultFontFamily);
            ctx.textAlign = 'center';
            ctx.textBaseline = 'bottom';
            ctx.fillStyle = '#000';
            this.barChart.data.datasets.forEach((dataset, i) => {
              const meta = chartInstance.controller.getDatasetMeta(i);
              meta.data.forEach((bar, index) => {
                const data = dataset.data[index];
                ctx.fillText(data, bar._model.x + 20, bar._model.y + 6);
              });
            });
          }
        },
      },
   });
  }


  generateLineChart() {
    const ctx = this.lineCanvas.nativeElement.getContext('2d');
    this.lineChart = new Chart(this.lineCanvas.nativeElement, {
      type: 'line',
      data: {
          labels: ['2012', '2013',
        '2014', '2015', '2016',
      '2017', '2018', '2019', '2020'],
          datasets: [
              {
                  data: [1200, 1600, 2020, 1990, 2390, 1850, 2100, 2000, 2600],
                  spanGaps: false,
                  borderCapStyle: 'round',
                  lineTension: 0.5,
                  fill: false,
                  backgroundColor: '#72B83B',
                  borderColor: '#72B83B',
                  pointRadius: 0,
                  borderJoinStyle: 'miter'
              }
          ]
      },
      options: {
        legend: {
          display: false
        },
        layout: {
          padding: {
              left: 20,
              right: 20,
              top: 20,
              bottom: 20
          }
        },
        scales: {
            xAxes: [{
              gridLines: {
                color: 'white', // couleur des lignes de l'axe
              },
              scaleLabel: {
                display: false // affichage de la légende des x
              },
              ticks: {
                autoSkip: false,
                maxRotation: 90,  // rotation de la légende
                minRotation: 90  // rotation de la légende
              }
            }]
        },
      },
   });
  }

}

````

## ng2-google-chart
[Back to top](#charts)  

[ng2-google-chart demo](https://www.devrandom.it/software/ng2-google-charts/demo/)    
[ng2-google-chart](https://www.devrandom.it/software/ng2-google-charts/)    
[Google GeoChart doc](https://developers.google.com/chart/interactive/docs/gallery/geochart#text-geocharts)    

*installation*

```
npm install ng2-google-charts --save
```

*import library in index.html*

````
  <script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
  <script type="text/javascript">
    google.charts.load('current', {
      'packages':['geochart'],
      // Note: you will need to get a mapsApiKey for your project.
      // See: https://developers.google.com/chart/interactive/docs/basic_load_libs#load-settings
      'mapsApiKey': <YOUR_API_KEY>
    });
  
  </script>
````


The way this library works, you’ll have to import Google Charts in your *page.module.ts* using

```import { Ng2GoogleChartsModule } from 'ng2-google-charts';```

and also include *Ng2GoogleChartsModule* in the *@NgModule* imports in *app.module.ts*.

### colored world map sample
[Back to top](#charts)  

*controller file*

````
import { Component, OnInit } from '@angular/core';
import { Ng2GoogleChartsModule } from 'ng2-google-charts';
import { GoogleChartInterface } from 'ng2-google-charts/google-charts-interfaces';

@Component({
  selector: 'app-worldmap',
  templateUrl: './worldmap.page.html',
  styleUrls: ['./worldmap.page.scss'],
})
export class WorldmapPage implements OnInit {

  public gChart: GoogleChartInterface;


  public geoChart: GoogleChartInterface = {
    chartType: 'GeoChart',
    dataTable: [
      ['Country', 'Population (2019)'],
      ['Austria',	8858775],
      ['Belgium',	11467923],
      ['Bulgaria', 7000039],
      ['Croatia',	4076246],
      ['Cyprus',	875898],
      ['Czech Republic', 10649800],
      ['Denmark',	5806081],
      ['Estonia',	1324820],
      ['Finland',	5517919],
      ['France',	67028048],
      ['Germany',	83019214],
      ['Greece',	10722287],
      ['Hungary',	9797561],
      ['Ireland',	4904226],
      ['Italy',	60359546],
      ['Latvia', 1919968],
      ['Lithuania',	2794184],
      ['Luxembourg', 613894],
      ['Malta',	493559],
      ['Netherlands',	17282163],
      ['Poland', 37972812],
      ['Portugal', 10276617],
      ['Romania',	19401658],
      ['Slovakia', 5450421],
      ['Slovenia', 2080908],
      ['Spain',	46934632],
      ['Sweden', 10230185],
    ],
    options: {
      //region: '150', // Europe
      colorAxis: {colors: ['#ffc107', '#fd7e14', '#dc3545']},
      /*backgroundColor: '#9cf',
      datalessRegionColor: '#f8f9fa',*/
      defaultColor: '#6c757d',
      //height: 600, // => if you want to set a specific height (width is not necessary)
    }
  };
  constructor() { }
}
````

*View file*

````
<ion-content>
  <google-chart [data]="geoChart"></google-chart>
</ion-content>
````

### Make ng2-google-chart responsive
[Back to top](#charts)  

> **Warning** : By default, ng2-google-chart are not responsive !

To avoid this, here is a self-working example based on the previous colorised worldmap. To make google chart responsive, you need to listen for *window.onresize* event

````
export class WorldmapPage implements OnInit {
  public geoChart: GoogleChartInterface;

  constructor(private ngZone: NgZone) { 
    window.onresize = (e) => {
        this.ngZone.run(() => {
            console.log("Width: " + window.innerWidth);
            console.log("Height: " + window.innerHeight);
            this.refreshMap(window.innerHeight);
        });
    };
  }

  ngOnInit() {
    this.refreshMap(0, 600);
  }
  
  ngOnDestroy() {
    // IMPORTANT ! You need to delete window onresize event listener
    // to avoid ViewDestroyError when you will close this view
    window.onresize = null;
  }

  refreshMap(height) {
    this.geoChart = {
      chartType: 'GeoChart',
      dataTable: [
        ['Country', 'Population (2019)'],
        ['Austria',	8858775],
        ['Belgium',	11467923],
        ['Bulgaria', 7000039],
        ['Croatia',	4076246],
        ['Cyprus',	875898],
        ['Czech Republic', 10649800],
        ['Denmark',	5806081],
        ['Estonia',	1324820],
        ['Finland',	5517919],
        ['France',	67028048],
        ['Germany',	83019214],
        ['Greece',	10722287],
        ['Hungary',	9797561],
        ['Ireland',	4904226],
        ['Italy',	60359546],
        ['Latvia', 1919968],
        ['Lithuania',	2794184],
        ['Luxembourg', 613894],
        ['Malta',	493559],
        ['Netherlands',	17282163],
        ['Poland', 37972812],
        ['Portugal', 10276617],
        ['Romania',	19401658],
        ['Slovakia', 5450421],
        ['Slovenia', 2080908],
        ['Spain',	46934632],
        ['Sweden', 10230185],
      ],
      options: {
        //region: '150', // Europe
        colorAxis: {colors: ['#ffc107', '#fd7e14', '#dc3545']},
        /*backgroundColor: '#9cf',
        datalessRegionColor: '#f8f9fa',*/
        defaultColor: '#6c757d',
        height: height,	// => remove if you want 100% page height chart
      }
    };
  }
}
````

> **To note** : If you remove the height property of geochart options, the chart will automatically fit the page
