---
date: '2019-10-23T02:07:33+09:00'
layout: post
published: true
qiita_article_id: 8a0f7df9f1ed903ffd63
tags:
- Angular
title: '[Angular] 画面を引っ張ってリロード (pull to refresh)'
updated: '2019-10-23T11:21:22+09:00'

---
# これはなに？  
  
画面を下に引っ張って(pan down)してリロードする機能が欲しくて作りました。  
  
![pull-to-refresh-sample.gif](/assets/images/0174d7c9-75b2-e30a-883d-cc146ff94992.gif)  
  
このサンプルは、実際にはリロードせず、リロード処理が必要になった回数をカウントして表示しているだけです。  
  
同じようなものはすでにいくつかあると思います。  
もしかすると、すでにAngular Materialに入っているかもしれません。  
薄目で見てやってください。  
  
# 元ソース  
  
こちら https://stackblitz.com/edit/angular-rxjs-pull-to-refresh を元にしています。  
  
元ソースから以下の点を変更しています。  
  
* アイコン（丸→矢印、ドロップシャドウ付加）  
* リロード中にアイコン回転 → 引っ張っている最中にアイコン回転  
* リロードが終わるとアイコンが消える → ただちにアイコンが消える  
* RxJS 6対応  
  
# ソースコード  
  
こちらに置きました。  
  
https://stackblitz.com/github/sengokyu/angular8-pull-to-refresh-sample  
  
## 主要ファイル  
  
主なファイルです。  
  
```
src
└ app
  ├ pull-to-refresh
  │ └ pull-to-refresh.component.ts  タッチイベントを受け取りアイコンを表示するコンポーネント
  └ services 
    └ load-notify.service.ts 他コンポーネント等へリロードすることを通知するためのサービス
```  
  
  
  
## ちょっと解説  
  
自分でもよくわからなくなりそうなRxJS周りの処理を解説します。  
  
### 捕まえるイベント  
  
下記は、タッチイベントを拾う`Observable`です。  
  
**src/app/pull-to-refresh/pull-to-refresh.component.ts**  
```ts:src/app/pull-to-refresh/pull-to-refresh.component.ts
  private readonly touchstart$ = fromEvent<TouchEvent>(document, 'touchstart');
  private readonly touchend$ = fromEvent<TouchEvent>(document, 'touchend');
  private readonly touchmove$ = fromEvent<TouchEvent>(document, 'touchmove');
```  
  
### アイコンの場所と回転の制御  
  
下記は、アイコンの移動と回転をさせるためのパラメータを流す`Observable`です。  
  
`touchstart`イベントが流れたら、代わりに`touchmove`イベントを購読し、移動量を流すようにしています。  
`touchend`イベントが流れるまで購読し終わったら、現在位置から画面トップまでマイナス方向の値を流します。  
  
**src/app/pull-to-refresh/pull-to-refresh.component.ts**  
```ts:src/app/pull-to-refresh/pull-to-refresh.component.ts
  private drag$ = this.touchstart$.pipe(
    switchMap(start => {
      let pos = TOP_POSITION;

      return concat(
        this.touchmove$.pipe(
          map(move => move.touches[0].pageY - start.touches[0].pageY),
          tap(p => (pos = p)),
          filter(p => p < this.pullDistance),
          takeUntil(this.touchend$)
        ),
        defer(() => this.tweenObservable(pos, TOP_POSITION, 200)) // 位置を戻す
      );
    }),
    repeat()
  );
```  
  
### リロードするように通知  
  
下記は、コンポーネント初期化時のコールバックです。  
サービスを介して画面のリロードが必要になったことを通知します。  
  
`touchstart`イベントが流れたら、代わりに`touchend`イベントを購読し、移動量を流しています。  
移動量が規定値以上であれば画面リロードを通知します。  
  
**src/app/pull-to-refresh/pull-to-refresh.component.ts**  
```ts:src/app/pull-to-refresh/pull-to-refresh.component.ts
  ngOnInit(): void {
    // 指を離した時に、規定距離を移動していたらリフレッシュ
    this.touchstart$
      .pipe(
        switchMap(start => {
          return this.touchend$.pipe(
            map(x => x.changedTouches[0].pageY - start.touches[0].pageY)
          );
        }),
        filter(p => p >= this.pullDistance)
      )
      .subscribe(() => this.loadNotifyService.notify());
  }
```  
  
# 感想  
  
* HammerJSとAngular組み込みのアニメーション機能を使って実装していましたが、どうにも期待する動きになりませんでした。  
* イベントを絡めたコンポーネントのユニットテストは難しいです（すみません。今回書いてません）。  
* RxJSたのしぃ〜。  
  
最後までお読みいただきありがとうございました。  
