---
title: 간단한 메모장 만들기 6
date: 2017-10-03 22:49:05
ttags: [javascript, angular, ionic]
thumbnail: https://cdn-images-1.medium.com/max/1000/1*dlN-vF1YJ7ZpOrx8rOGBEQ.jpeg
banner: https://cdn-images-1.medium.com/max/1000/1*dlN-vF1YJ7ZpOrx8rOGBEQ.jpeg
categories:
- projects
- simpleMemo
---

이번시간에는 아이오닉에서 제공해주는 무한 스크롤(infinite scroll)을 적용해볼거에요!!

## 테스트 환경 작성

테스트케이스 작성을 위해서 아래 코드를 **메모리스트** 페이지의 **ngOnInit** 생명주기 메서드의 아래의 코드를 작성한 후 새로고침해주세요.

```typescript
  for(let i = 0; i < 5000; i++) {
    this.memoManager.createMemo(`제목${i.toString()}`, `내용${i.toString}`);
  }
```

5000개가 넘어가면 메모리스트 페이지가 많이 느려지는걸 볼 수 있습니다.
텍스트만 있어도 느린데 이미지가 있는 페이지라면 어느정도일지 짐작이 되시겠죠?

<!-- more -->

## 코드 작성

### HTML
**src/pages/memo-list/memo-list/html**
```html
<ion-header>
  <ion-navbar color="primary">
    <ion-searchbar [(ngModel)]="searchKeyword"></ion-searchbar>
    <ion-buttons end>
      <button ion-button icon-only (click)="onClickMoreOption()">
        <ion-icon name="more">
        </ion-icon>
      </button>
    </ion-buttons>
  </ion-navbar>
</ion-header>
<ion-content>
  <ng-template ngFor let-memo [ngForOf]="memoList | async | slice : 0 : viewCount" let-i="index">
    <ion-card (click)="onClickViewMemoDetail(memo)" *ngIf="filterMemo(memo)">
      <ion-card-header>
        {{ memo.title }}
      </ion-card-header>
      <ion-card-content>
        {{ memo.contents }}
      </ion-card-content>
    </ion-card>
  </ng-template>
  <ion-infinite-scroll (ionInfinite)="doInfinite($event)">
    <ion-infinite-scroll-content
      loadingSpinner="bubbles"
      loadingText="please waite..."
    ></ion-infinite-scroll-content>
  </ion-infinite-scroll>
  <ion-fab right bottom>
    <button ion-fab icon-only (click)="onClickCreateMemo()">
      <ion-icon name="add" big></ion-icon>
    </button>
  </ion-fab>
</ion-content>
```

전체 코드입니다.

```html

<ng-template ngFor let-memo [ngForOf]="memoList | async | slice : 0 : viewCount" let-i="index">
  ...
</ng-template>

<ion-infinite-scroll (ionInfinite)="doInfinite($event)">
  ...
<ion-infinite-scroll>
```

**ionInfinite**에 주목하세요. ion-infinite-scroll 태그가 화면상에 보일경우 등록해놓은 **doInfinite** 함수를 호출합니다.

### Code
**src/pages/memo-list/memo-list/ts**
```typescript
import { AuthManagerProvider } from './../../providers/auth-manager/auth-manager';
import { FirebaseListObservable } from 'angularfire2/database';
import { Memo } from './../../models/memo/memo.interface';
import { Component, OnInit } from '@angular/core';
import { IonicPage, NavController, NavParams, LoadingController, ActionSheetController } from 'ionic-angular';

import { MemoCreatePage } from './../memo-create/memo-create';
import { MemoDetailPage } from './../memo-detail/memo-detail';

import { MemoManagerProvider } from './../../providers/memo-manager/memo-manager';
@IonicPage()

@Component({
  selector: 'page-memo-list',
  templateUrl: 'memo-list.html',
})

export class MemoListPage {

  searchKeyword: string = '';
  viewCount: number = 10;
  memoList: FirebaseListObservable<Memo>;

  constructor(
    public navCtrl: NavController,
    public navParams: NavParams,
    public loadingCtrl: LoadingController,
    public actionSheetCtrl: ActionSheetController,
    public authManager: AuthManagerProvider,
    public memoManager: MemoManagerProvider) {
  }
 
  ngOnInit() {
    this.memoList = this.memoManager.getMemoList();
  }

  doInfinite(infiniteScroll: any) {
    window.setTimeout(() => {
      this.viewCount += this.viewCount;
      infiniteScroll.complete();
    }, 500);
  }

  filterMemo(memo: Memo): boolean {
    return memo.title.includes(this.searchKeyword) || memo.title.includes(this.searchKeyword);
  }

  onClickViewMemoDetail(memo: Memo) {
    this.navCtrl.push(MemoDetailPage, { memo: memo });
  }
  
  onClickCreateMemo() {
    this.navCtrl.push(MemoCreatePage);
  }

  onClickMoreOption() {
    const actionSheet = this.getMoreOptionActionSheet();
    actionSheet.present();
  }

  getMoreOptionActionSheet() {
    return this.actionSheetCtrl.create({
      buttons: [
        {
          text: 'Logout',
          role: 'destructive',
          handler: () => {
            this.authManager.logoutUser();
          }
        }, {
          text: 'Cancel',
          role: 'cancel'
        }
      ]
    });
  }
}
```

별거없죠? slice할 viewCount를 증가시켜주고, infiniteScroll 이벤트를 종료합니다.

이번 포스팅은 좀 짧은감이 있네요. 무한스크롤에 대한 다음 두 글이 있는데 재미있으니 한번 읽어보세요!

1. https://brunch.co.kr/@ebprux/81
2. https://brunch.co.kr/@blackindigo-red/13