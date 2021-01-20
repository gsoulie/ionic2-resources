[< Back to main Menu](https://github.com/gsoulie/Mobile-App-Development/blob/master/ionic2-test.md)    

# Gesture

## Resources

https://www.youtube.com/watch?v=S57QWQGZf3I&ab_channel=SimonGrimm       
https://www.joshmorony.com/create-tinder-style-swipe-cards-with-ionic-gestures/        
https://www.youtube.com/watch?v=5Umk0KyD9rU&ab_channel=JoshuaMorony       


## Swipe on card list

This is a sample of list of cards with a swipe gesture with 2 actions available on left or right swipe.

How it works : we have a classic list of ion-item including a ion-card. Under each card, we have a div ("action-div<X>") which contains the 2 possible actions ("left-action<X>" and 
"right-action<X>").
By default, those actions div are hidden and the background of their parent is transparent. When we are going to swipe right or left, we want to show one or the other action switch swipe direction and
apply a different color to the background. 
To achieve this, when we are going to change the css "visible" property of the target action and change de css "background" property of the "action-div<X>"

In this example, on the right swipe, we remove the card from the list like in gmail mobile app (delete item and animate list to collapse the rest of the list). On left swipe, we just show an alert.
*View file*

````
<ion-list>
  <div *ngFor="let study of studies; let i = index" id="swipe-div">
    <div [id]="'action-div' + i " class="action-div">
      <div [id]="'action-div-inner' + i" class="action-div-inner">
        <div class="action-icon left" [id]="'left-action' +  i ">
          <ion-icon name="eye" slot="icon-only"></ion-icon>
        </div>
        <div class="action-icon right" [id]="'right-action' +  i ">
          <ion-icon name="trash" slot="icon-only"></ion-icon>
        </div>
      </div>
    </div>
    <ion-item #studyTile lines="none" class="swipe-item">
      <ion-card>
        <ion-card-header>
          {{ study.title }}
        </ion-card-header>
      </ion-card>
    </ion-item>
  </div>
</ion-list>
````

*Style file*

````

app-study-tile {
    width: 100% !important;
}
.bookmark {
    stroke: var(--ion-color-text-grey) !important;
    fill: white !important; 
}
ion-list {
    --background: transparent !important;
    background: transparent !important;
    background-color: transparent !important;
}
#swipe-div, .swipe-item {
    position: relative;
    background-color: transparent !important;
    .action-div {
        position: absolute;
        height: 100%;
        width: 100%;
        padding-top: 10px;   
        padding-bottom: 10px; 
        
        background-color: transparent !important;
    }
    .action-div-inner {
        display: flex;
        align-items: center;
        justify-content: space-between;
        height: 100%;
        background-color: transparent;
    }
    .action-icon {
        display: flex;
        align-items: center;
        justify-content: center;
        
        height: 100%;
        width: 100px;
        ion-icon {
            color: white;
            width: 30px;
            height: 30px;
        }
    }
    .left {
        visibility: hidden;
    }
    .right {
        visibility: hidden;
    }
    ion-item {
        --background: transparent !important;
        background-color: transparent !important;
        --padding-start: 0px !important;
        --padding-end: 0px !important;
        --inner-padding-start: 0px !important;
        --inner-padding-end: 0px !important;
    }
}
````

*Controller file*

````
import { DataService } from './../../shared/services/data.service';
import { Study } from './../../shared/models/study.model';
import { ServiceResult } from './../../shared/models/serviceResult.model';
import { UserQueryService } from './../../shared/services/user-query.service';
import { globalConfig } from './../../shared/enum/global.config';
import { Component, OnInit, ViewChild, ElementRef, ChangeDetectorRef, QueryList, ViewChildren } from '@angular/core';
import { ActivatedRoute, Params } from '@angular/router';
import { Gesture, LoadingController, createAnimation, GestureController } from '@ionic/angular';

@Component({
  selector: 'app-results',
  templateUrl: './results.page.html',
  styleUrls: ['./results.page.scss']
})
export class ResultsPage implements OnInit {

  dataset: ServiceResult;
  studies: Study[] = [];
  @ViewChildren('studyTile', {read: ElementRef}) cards: QueryList<ElementRef>;

  constructor(
              private userQueryService: UserQueryService,
              private dataService: DataService,
              private gestureCtrl: GestureController,
              private ref: ChangeDetectorRef) { }

 
  async ngOnInit(): Promise<void> {
    this.fetchData();
  }

  /**
   * Fetching data from server
   */
  async fetchData(): Promise<void> {
      await this.userQueryService.fetchQueryResults({queryId: this.selectedId, start: this.page})
      .subscribe((res: ServiceResult) => {
        this.studies = res;
        this.initializeGesture() // building gesture
      });
  }
 
  initializeGesture(): void {
    this.ref.detectChanges();
    // convert ion-card dom object in array
    const cardArray = this.cards.toArray();
    this.useSwipe(cardArray);
  }

  
  async useSwipe(cardArray:ElementRef<any>[]): Promise<void> {
    for(let i = 0; i < cardArray.length; i++) {
      const card = cardArray[i];
      const style = card.nativeElement.style;
      const windowWidth = window.innerWidth;

      // animation for card deletion 
      const hostDeleteAnimation = createAnimation()
      .addElement(card.nativeElement)
      .duration(300)
      .easing('ease-out')
      .fromTo('height', '48px', '0');

      const gesture: Gesture = await this.gestureCtrl.create({
        el: card.nativeElement,
        gestureName: 'swipe',
        gesturePriority: 100, // needed if using pull to refresh
        onStart: ev => {
          style.transition = "";
        },
        onMove: ev => {  
          // Right swipe
          if (ev.deltaX >= 20) {
            style.transform = `translate3d(${ev.deltaX}px, 0, 0)`;
            document.getElementById("right-action"+i).style.visibility = 'hidden';
            document.getElementById("left-action"+i).style.visibility = 'visible';
            document.getElementById("action-div-inner"+i).style.backgroundColor = '#707070';
          } else {
            // Left swipe
            if (ev.deltaX < -20) {
              style.transform = `translate3d(${ev.deltaX}px, 0, 0)`;
              document.getElementById("right-action"+i).style.visibility = 'visible';
              document.getElementById("left-action"+i).style.visibility = 'hidden';
              document.getElementById("action-div-inner"+i).style.backgroundColor = '#009DBE';
            } else {
              document.getElementById("right-action"+i).style.visibility = 'hidden';
              document.getElementById("left-action"+i).style.visibility = 'hidden'; 
              document.getElementById("action-div-inner"+i).style.backgroundColor = 'transparent';
            }
          }
          this.ref.detectChanges();
          
        },
        onEnd: ev => {
          style.transition = "0.2s ease-out";

          // Left swipe gauche
          if (ev.deltaX <= (windowWidth / 2) * -1) {
            alert('Action on left swipe');
            this.ref.detectChanges();
          }

          // Right swipe
          if(ev.deltaX > 180){  
            // Remove card from list
            style.transform = `translate3d(${windowWidth}px, 0, 0)`;
            hostDeleteAnimation.play();
            document.getElementById("action-div"+i).remove();
            hostDeleteAnimation.onFinish(() => {
            // this.deleted.emit(true);
            });
          
          } else {
            // Reset css when card get back on its initial position
            style.transform = ''
            document.getElementById("right-action"+i).style.visibility = 'hidden';
            document.getElementById("left-action"+i).style.visibility = 'hidden';
            document.getElementById("action-div-inner"+i).style.backgroundColor = 'transparent';
            this.ref.detectChanges();
          }
        }
      }, true);
      gesture.enable(true);
    }
  }
}
 
````
