---
date: '2017-10-25T14:46:38+09:00'
layout: post
published: true
qiita_article_id: 88f8a461826ec2e6c6c6
tags:
- JavaScript
- Node.js
- webpack
title: '[JavaScript] Webpackとはなんぞや。そして、Slick CarouselをWebpackで使う'
updated: '2017-10-25T14:46:38+09:00'

---
# ストーリー  
  
Webpack便利ですね。  
複数のNodeモジュールをさくっとひとまとめにしてくれます。  
各種ローダーを入れれば、ECMAScriptを変換したり、CSSも読み込んだりできます。  
minifiedもできちゃいます。  
  
どんなものなのか理解しないまま、日々使っていたのですが、ここに来てjQueryプラグインを使う必要が出てきました。  
**slick-carousel**  
です。  
  
いつものように読み込んだところ、  
  
```
Uncaught TypeError: t(...).slick is not a function
```  
  
と、何やら不穏な動き。  
  
重い腰を上げて、Webpackが何をしてくれるものなのかを理解することにしたのでした。  
  
# 導入  
  
シンプルな環境で試してみます。  
  
```shell-session
mkdir webpack-exp
cd webpack-exp
npm init
npm install --save-dev webpack
```  
  
これで、`node_modules/.bin/webpack`コマンドが使えるようになりました。  
  
## 設定ファイル  
  
webpackの設定ファイルを作成します。  
  
**webpack.config.js**  
```js:webpack.config.js
const path = require('path');

module.exports = {
    entry: './src/app.js',                                                                
    output: {
        filename: 'bundle.js',
        path: path.resolve(__dirname, 'dist')
    }
};
```  
  
# 空っぽのソースはどうなる？  
  
まずは空っぽのソースを作ってwebpackしてみました。  
  
**src/app.js**  
```js:src/app.js
/* I am app.js */
```  
  
**dist/bundle.js**  
```js:dist/bundle.js
(function(modules) {
     /*
      *
      * （省略） Webpack初期化コード
      * 
      */
})([
    (function(module, exports) {
        /* I am app.js */
    })
]);
```  
  
`app.js`の内容が、`module`と`exports`を引数とする関数でラッピングされています。  
読み込んだソース中で`module`と`exports`が使えるのはこういうわけでしたか。  
  
ラッピングされた関数は、webpack初期化コードに渡されて順番に呼び出されています。  
  
  
# jQueryを読み込んでみる  
  
試してみたかったjQueryを読み込んでみます。  
  
```shell-session
npm install --save-dev jquery
```  
  
ソースは次のようにしてみました。  
  
**src/app.js**  
```js:src/app.js
import jQuery from 'jquery';

/* I am app.js */
```  
  
**dist/bundle.js**  
```js:dist/bundle.js
(function(modules) {
     /*
      *
      * （省略） Webpack初期化コード
      * 
      */
})([
    (function(module, __webpack_exports__, __webpack_require__) {
        "use strict";

        Object.defineProperty(__webpack_exports__, "__esModule", { value: true });

        var __WEBPACK_IMPORTED_MODULE_0_jquery__ =
          __webpack_require__(1);

        var __WEBPACK_IMPORTED_MODULE_0_jquery___default =
          __webpack_require__.n(__WEBPACK_IMPORTED_MODULE_0_jquery__);

        /* I am app.js */

    }),
    (function(module, exports, __webpack_require__) {
        var __WEBPACK_AMD_DEFINE_ARRAY__, __WEBPACK_AMD_DEFINE_RESULT__;

        /*
         * (省略) jQuery本体のコード
         */
    })
]);

```  
  
`import`に書いた`jQuery`はどこにいっちゃいましたかね？  
  
あと、ラッピング関数の引数が変わってますね。`exports`がなくなっちゃいました。  
  
  
# jQueryを使ってみる  
  
中身のあるソースにしてみました。  
  
**src/app.js**  
```js:src/app.js
import jQuery from 'jquery';                                                                                                                                                        

/* I am app.js */
;(function($) {
    $(function() {
        console.log('Hello I am app.js');                                                                                                                                           
    });
})(jQuery);
```  
  
**dist/bundle.js**  
```js:dist/bundle.js
(function(modules) {
     /* (snip) */
})([
    (function(module, __webpack_exports__, __webpack_require__) {
        (snip)
        var __WEBPACK_IMPORTED_MODULE_0_jquery___default =
          __webpack_require__.n(__WEBPACK_IMPORTED_MODULE_0_jquery__);

        /* I am app.js */
        ;(function($) {
            $(function() {
                console.log('Hello I am app.js');
            });
        })(__WEBPACK_IMPORTED_MODULE_0_jquery___default.a);
    }),
    (function(module, exports, __webpack_require__) {
        (snip)
    })
]);
```  
  
元ソースにあった`jQuery`が、いい感じに置き換えられています。  
ブラウザで読み込めば、ちゃんと動きますよー。  
  
# `import`と`require`って違うのか？  
  
ところで、外部モジュール読み込みに使う`require`は、`import`と違うのかどうか？  
  
**src/app.js**  
```js:src/app.js
const jQuery = require('jquery');                                                                                                                                                   

/* I am app.js */
;(function($) {
    $(function() {
        console.log('Hello, I am app.js');
    });
})(jQuery);
```  
ラッピングする関数は、このようになりました。  
  
**dist/bundle.js**  
```js:dist/bundle.js
function(module, exports, __webpack_require__) {
    const jQuery = __webpack_require__(1);

    /* I am app.js */
    ;(function($) {
        $(function() {
            console.log('Hello, I am app.js');
        });
    })(jQuery);
}
```  
  
`import`よりシンプルな中身になりました。  
  
  
# jQueryプラグインを使ってみる  
  
npmで入るなにか簡単なプラグインということで[jquery.marquee](https://www.npmjs.com/package/jquery.marquee)を試してみます。  
  
```shell-session
npm install --save-dev jquery.marquee
```  
  
**src/app.js**  
```js:src/app.js
const jQuery = require('jquery');
require('jquery.marquee');

/* I am app.js */
;(function($) {
    $(function() {
        $('.marquee').marquee({                                                                                                                                                     
            duration: 5000                                                                                                                                                          
        });     
    });
})(jQuery);
```  
  
## 非情なエラー  
  
ブラウザで読み込んだところ、エラーになってしまいました。  
  
```
bundle.js:10828 Uncaught ReferenceError: jQuery is not defined
    at Object.<anonymous> (bundle.js:10828)
    at __webpack_require__ (bundle.js:20)
    at Object.<anonymous> (bundle.js:71)
    at __webpack_require__ (bundle.js:20)
    at bundle.js:63
    at bundle.js:66
```  
  
## 非情な原因  
  
jquery.marqueeのソースを眺めてみると、`window.jQuery`ではなく、`jQuery`を参照しているのが原因のようです。むむむ。。。  
  
## 温厚(?)な解決策  
  
jQueryをglobalオブジェクトに入れてあげればいいようです。  
  
**src/app.js**  
```js:src/app.js
global.jQuery = require('jquery');
```  
  
ラッピング関数部分が、このように変わりました。  
  
**dist/bundle.js**  
```js:dist/bundle.js
function(module, exports, __webpack_require__) {
    (function(global) {
        global.jQuery = __webpack_require__(2);
        __webpack_require__(3);

        /* I am app.js */
        ;(function($) {
            $(function() {
                $('.marquee').marquee({
                    duration: 5000
                });
            });
        })(jQuery);
    }.call(exports, __webpack_require__(1)))
}
```  
  
`__webpack_require__(1)`で返される関数の中でグローバルなオブジェクトを定義しています。  
  
**dist/bundle.js**  
```js:dist/bundle.js
function(module, exports) {
    var g;

    // This works in non-strict mode
    g = (function() {
        return this;
    })();

    try {
        // This works if eval is allowed (see CSP)
        g = g || Function("return this")() || (1,eval)("this");
    } catch(e) {
        // This works if the window reference is available
        if(typeof window === "object")
                g = window;
    }

    // g can still be undefined, but nothing to do about it...
    // We return undefined, instead of nothing here, so it's
    // easier to handle this case. if(!global) { ...}
    module.exports = g;
}
```  
  
なるほどねー。  
  
  
# さて、Slick Carousel  
  
カルーセル、、、（オヤジギャグ審議中）  
  
インストールします。  
  
```
npm install --save-dev slick-carousel
```  
  
**src/app.js**  
```js:src/app.js
const jQuery = require('jquery');
require('slick-carousel');

/* I am app.js */
;(function($) {
    $(function() {
        $('.myslick').slick();
    });
})(jQuery);
```  
  
## 再現しないエラー  
  
・・・なんか動いてしまいました。いや、動いていいんですけどね。  
  
## 元エラーの原因  
  
jQueryをscriptタグでもも読み込んでた。。。  
  
# まとめ  
  
Webpackは読み込んだモジュールをいい塩梅に関数内に分離してくれて、必要があればそれを各モジュールに渡してくれることがわかりました。  
  
さらに得られた知見としては、  
  
* globalオブジェクトを恐れず使え  
* jQueryを何度も読み込まない orz  
  
というところでしょうか。  
  
# 環境  
  
最後に試した環境です。  
  
* node 8.6.0  
* npm 5.3.0  
* webpack 3.8.1  
* jquery 3.2.1  
* slick-carousel 1.8.1  
  
  
