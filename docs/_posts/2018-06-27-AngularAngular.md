---
date: '2018-06-27T15:01:38+09:00'
layout: post
published: true
qiita_article_id: 90d64e6ac71ee2de7d42
tags:
- TypeScript
- Angular
title: '[Angular] Angularテンプレートまとめ チートシート'
updated: '2018-06-27T15:04:25+09:00'

---
引用元: [Angular - Cheat Sheet](https://angular.io/guide/cheatsheet)  
  
# このチートシートの読み方  
  
| テンプレート | 出力 | 解説 |  
|:--|:--|:--|  
| テンプレートの書き方です。  | コンポーネント適用後の出力例です。  | 解説です。  |  
  
## 前提  
  
出力例は、以下のコンポーネントクラスを前提としています。  
  
```typescript
export class MyCmpComponent {
  firstName: string     = "Jane";
  myAriaRole: string    = "banner";
  isDelightful: boolean = true;
  mySize: int           = 100;
  ponyName: string      = "Seabiscuit";
  employer: Employer;

  readRainbow($event: any): void { /* (snip) */ }
}
```  
  
  
# Angular テンプレート文法  
  
  
| テンプレート | 出力 | 解説 |  
|:--|:--|:--|  
| `<input [value]="firstName">`  | `<input value="Jane">`  | `value`プロパティに、`firstName`式の評価結果をバインドします。  |  
| `<div [attr.role]="myAriaRole">`  | `<div role="banner">`  | `role`属性に、`myAriaRole`式の評価結果をバインドします。  |  
| `<div [class.extra-sparkle]="isDelightful">`  | `<div class="extra-sparkle">`  | `extra-sparcle`CSSクラスを付与するかどうかを、`isDelightful`式の評価結果が真値になるかどうかにバインドします。  |  
| `<div [style.width.px]="mySize">`  | `<div style="width:100px">`  | `width`プロパティに`mySize`式の評価結果をバインドします。単位`px`はオプションです。  |  
| `<button (click)="readRainbow($event)">`  | `<button onClick="readRainbow($event)">`  | クリック時に`readRainbow`メソッドを呼び出します。  |  
| `<div title="Hello {{"{{"}}ponyName}}">` | `<div title="Hello Seabuscuit">` | プロパティに補完文字列をバインドします。 `<div [title]="'Hello ' + ponyName">` と一緒です。 |  
| `<p>Hello {{"{{"}}ponyName}}</p>` | `<p>Hello Seabuscuit</p>` | コンテンツを補完文字列にバインドします。 |  
| `<my-cmp [(title)]="name">` || 双方向（ツーウェイ）データバインディングです。 `<my-cmp [title]="name" (titleChange)="name=$event">` と同じです。  |  
| `<video #movieplayer ...><button (click)="movieplayer.play()"></video>` | | `video`要素を格納した`movieplayer`というローカル変数を生成します。ローカル変数は、同じテンプレート中のデータバインディングやイベントバインディング式で使用できます。 |  
| `<p *myUnless="myExpression">...</p>` || `<ng-template [myUnless]="myExpression"><p>...</p></ng-template>`と同じです。 |  
| `<p>Card No.: {{"{{"}}cardNumber ｜ myCardNumberFormatter}}</p>`[^1] || `myCardNumberFormatter`フィルタを通して出力します。 |  
| `<p>Employer: {{"{{"}}employer?.companyName}}</p>` | | `employer`が`undefined`でもエラーにならないようにします。 |  
  
[^1]: Markdownのエスケープがわからなかったので、パイプを全角で記載しています。  
