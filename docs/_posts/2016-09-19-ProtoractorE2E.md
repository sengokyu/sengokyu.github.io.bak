---
'created_at: ': '2016-09-19T15:50:26+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: JavaScript
      versions: []
    - name: test
      versions: []
    - name: Selenium
      versions: []
    - name: Protractor
      versions: []
  state: 0
title: "[Protoractor][E2E\u30C6\u30B9\u30C8] \u30D6\u30E9\u30A6\u30B6\u3092\u4F7F\u7528\
  \u3057\u305F\u30C6\u30B9\u30C8\u3092\u884C\u3046"
'updated_at: ': '2016-09-19T16:02:30+09:00'

---
# 環境  
  
- Ubuntu 16.04  
  
## 必要なもの  
  
- node.js  
- npm  
- Chromeブラウザ  
- Yeomanとprotractorジェネレータ  
  
  
Yeomanが入っていなければインストールします。  
  
```
% sudo npm install -g yeoman
% sudo npm install -g generator-protractor
```  
  
  
  
# 準備  
  
Yeomanでさくっと準備します。  
  
```
% yo protractor
Welcome to the protractor code generator.
? Choose a name for the protractor configuration file protractor.conf.js
? Choose a base URL http://localhost:8000
? Which browsers do you want to run? Chrome
   create package.json
   create protractor.conf.js
   create spec/example_spec.js
   create README.txt


I'm all done. Running npm install for you to install the required dependencies. 
If this fails, try running the command yourself.

(snip)
Done! Now follow these steps:
How to run the tests:

1. Start the Selenium server:
./node_modules/.bin/webdriver-manager start

2. Open a new terminal and run Protractor:
./node_modules/.bin/protractor protractor.conf.js

You can read these instructions in README.txt

```  
  
質問にはとりあえず全部Enterで答えました。  
  
# 動かしてみる  
  
Yeoman最後のコメントに従って、動かしています。  
  
```
% ./node_modules/.bin/webdriver-manager start
```  
  
何かがが起動しました。  
  
別のコンソールでprotractorを動かします。  
  
```
% ./node_modules/.bin/protractor protractor.conf.js 
Using the selenium server at http://localhost:4444/wd/hub
[launcher] Running 1 instances of WebDriver
Started
...


3 specs, 0 failures
Finished in 8.837 seconds
[launcher] 0 instance(s) of WebDriver still running
[launcher] chrome #1 passed
```  
  
ブラウザが起動し、angularjs.orgのサイトが表示されて、なんだかいろいろうまく動いたみたいです。  
  
# npmコマンドで動かす  
  
package.jsonのscripts配列に、以下の２行を追加して`npm test`で動くようにしました。  
  
****  
```js:
    "test": "node ./node_modules/.bin/protractor protractor.conf.js",
    "webdriver": "node ./node_modules/.bin/webdriver-manager start"
```  
  
# AngularJSなしで動かす  
  
デフォルトではAngularJSの準備ができるまで待つようになっているので、AngularJSでないアプリの場合は待たないようにします。  
  
`protractor.conf.js`ファイルに追記します。  
  
**protractor.conf.js**  
```js:protractor.conf.js
  onPrepare: function() {
    browser.ignoreSynchronization = true;
  },

```  
  
# テストを書く  
  
テストはspecディレクトリの下に`*_spec.js`というファイル名で作成していきます。  
  
ユースケース名にするとわかりやすいと思います。  
  
  
  
  
  
