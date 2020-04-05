---
date: '2016-07-08T10:07:18+09:00'
layout: post
published: true
qiita_article_id: 536b16ab5c6fc52aa823
tags:
- jQuery
- Bootstrap
- jquery.ui
title: bootstrapモーダルダイアログ上にinput-groupを配置すると、jQuery UI datepickerがz-indexの計算を間違える
updated: '2016-07-08T10:07:18+09:00'

---
jQuery UI便利ですか？便利ですね！！  
bootstrap便利ですか？便利ですね！！  
（導入）  
  
# 前提  
  
## bootstrapのinput-group  
  
bootstrapにinput-groupという機能があります。  
http://getbootstrap.com/components/#input-groups  
フォーム部品の前後に、アイコンや文字をきれいに付ける機能です。  
  
## bootstrapのmodal  
  
bootstrapにmodalという機能があります。  
http://getbootstrap.com/javascript/#modals  
ページ上にきれいなウィンドウを表示する機能です。  
  
## jquery uiのdatepicker  
  
jQuery UIにdatepickerという機能があります。  
http://jqueryui.com/datepicker/  
テキストボックスにカレンダーを付ける機能です。  
  
  
# 問題  
  
modal上に配置したフォーム部品にinput-groupを付けると、datepickerがz-indexの計算を間違えてしまい、datepikerがmodalの後ろに隠れてしまいます。  
  
![datepicker1.PNG](/assets/images/eb22ce80-fb4b-1799-4fbb-583144ab0727.png)  
  
ソースはこんなんです。  
  
**zindex-issue.html**  
```html:zindex-issue.html
<div id="mydialog" class="modal fade" role="dialog">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
      </div>
      <div class="modal-body">
        <form>
          <div class="form-group">
            <label>This is a datepicker with input-group on modal</label>
            <div class="input-group">
              <span class="input-group-addon"><i class="glyphicon glyphicon-calendar"></i></span>
              <input type="text" class="datepicker form-control">
           </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</div>
```  
  
  
  
ほんとはこのようになるはずなのです。  
  
![datepicker2.PNG](/assets/images/f15d7dce-2a6c-9d82-91e9-cd5b3e1d1bb9.png)  
  
こちらで問題を再現できます。  
https://jsfiddle.net/5da9pjw1/16/  
  
  
  
# 解決策！？  
  
モーダル上の.input-group .form-controlのz-indexをmodalより大きい数字にしてあげれば大丈夫？  
  
**fix-bootstrap-zindex-issue.css**  
```css:fix-bootstrap-zindex-issue.css
.modal .input-group .form-control { z-index:1060; }
```  
  
解決策追加後はこちら。  
https://jsfiddle.net/5da9pjw1/17/  
