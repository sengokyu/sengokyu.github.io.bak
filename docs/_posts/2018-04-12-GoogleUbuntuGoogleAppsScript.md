---
date: '2018-04-12T16:02:43+09:00'
layout: post
published: true
qiita_article_id: e6013655b1e5f5dfb44c
tags:
- Node.js
- Ubuntu
- GoogleAppsScript
title: '[Google] Ubuntuで始めるGoogle Apps Script開発'
updated: '2018-04-12T16:02:43+09:00'

---
# はじめに  
  
Google Apps Scriptの開発環境をUbuntu上に整えます。  
  
Ubuntuで作業していますが、とくにOSには依存していません。  
nodejsさえ動けばよいはずです。  
  
# 環境  
  
- Ubuntu 17.10 artful  
- nodejs 9.11.1  
- npm 5.6.0  
  
nodejs/npmがないときは、nodesource.comからインストールします。  
具体的なインストール方法は https://github.com/nodesource/distributions#debinstall を参照してください。  
  
# プロジェクトの作成  
  
適当なディレクトリを作成します。  
  
****  
```shell:
mkdir gas-experience-1
cd gas-experience-1
```  
  
git初期化します。  
  
****  
```shell:
git init
```  
  
npm初期化します。質問には適当に答えます。  
  
****  
```shell:
npm init
```  
  
こんなんができました。  
  
****  
```shell:
% ls -AF
.git/  package.json
```  
  
# [clasp](https://github.com/google/clasp)をインストール  
  
[node-google-apps-script](https://www.npmjs.com/package/node-google-apps-script)はもう古いそうです。[clasp](https://github.com/google/clasp)をインストールします。  
  
****  
```shell:
npm install --save-dev @google/clasp
```  
  
こんなんが入りました。  
  
****  
```shell:
% ls -AF node_modules/.bin 
clasp@  gp12-pem@  mime@  mkdirp@  rimraf@  uuid@  which@
```  
  
# script.google.comへログイン  
  
****  
```shell:
./node_modules/.bin/clasp login
```  
  
ブラウザが起動するので、Googleへログインします。  
  
![image.png](/assets/images/2fc614e7-33c6-635e-7b0d-ee081d00bcea.png)  
  
アクセス可否を尋ねられるので、許可します。  
  
![image.png](/assets/images/3f9fbdd1-4dd8-6bcf-3dbe-4020c65474b6.png)  
  
ログインできたようです。ブラウザを閉じます。  
  
![image.png](/assets/images/a872d4f0-1381-8cb5-4b6d-90239060bf31.png)  
  
ホームディレクトリにファイルができました。  
  
**~/.clasprc.json**  
```json:~/.clasprc.json
{"access_token":"〜〜〜内緒〜〜〜","token_type":"Bearer","refresh_token":"〜〜〜","expiry_date":1523505527448}
```  
  
  
# GASプロジェクトを作成……の前に、APIを有効化  
  
GASプロジェクトを新規作成しようとしたら、エラーになってしまいました。  
  
****  
```shell:
% ./node_modules/.bin/clasp create gas-experience-1
Error: Permission denied. Enable the Apps Script API:
https://script.google.com/home/usersettings
```  
  
メッセージに従って https://script.google.com/home/usersettings へアクセスします。  
  
オフになっていました。  
  
![image.png](/assets/images/2d7044fd-3213-7ca6-366c-8b6147f055fe.png)  
  
オンにします。  
  
![image.png](/assets/images/51783593-6a41-2605-fd35-c0ffcc98e456.png)  
  
  
# GASプロジェクトを作成  
  
あらためてプロジェクトを作成します。タイトルを「gas-experience-1」にしました。付けなくてもよいです。  
  
****  
```shell:
./node_modules/.bin/clasp create gas-experience-1
```  
  
ファイルが2つできました。  
  
****  
```shell:
% ls -AF
appsscript.json  .clasp.json  .git/  node_modules/  package.json  package-lock.json
```  
  
中身はこんなんです。  
  
**appscript.json**  
```json:appscript.json
{
  "timeZone": "America/New_York",
  "dependencies": {
  },
  "exceptionLogging": "STACKDRIVER"
}
```  
  
**.clasp.json**  
```json:.clasp.json
{"scriptId":"〜〜〜"}
```  
  
ブラウザから見るとこうなっていました。  
  
![image.png](/assets/images/4cdff160-7f16-d2b8-ec5f-528d63567dc1.png)  
  
  
# スクリプトを作成  
  
お好みのエディタでスクリプトを作成します。  
  
とりあえずこんな内容にしてみました。[^1]  
  
[^1]: これはユニットテストできないお試し用コードです。真似しないでくださいね。  
  
**Code.js**  
```js:Code.js
function doGet(e) {                          
  var template = HtmlService.createTemplate('<p>Hello, <?= name ?>!</p>');                
  template.name = e.parameter.name;          

  var output = HtmlService.createHtmlOutput();                                            
  output.append(template.evaluate().getContent());                                        

  return output;                             
}
```  
  
# アップロード対象のファイルを設定  
  
既定では、すべてのnode_modulesを含むすべてのファイルをアップロードしようとしてしまいます。そしてこんなエラーが起きます。  
  
```
Push failed. Errors:                         
(node:15160) UnhandledPromiseRejectionWarning: TypeError: Cannot read property 'map' of undefined         
```  
  
アップロード対象のファイルを明示的に`.claspignore`ファイルへ記載します。  
  
**.claspignore**  
```text:.claspignore
**/**
!appsscript.json
!Code.js
```  
  
書き方は.gitignoreと一緒かな？  
[anymatch](https://github.com/micromatch/anymatch)モジュールに従います。  
  
  
  
# スクリプトをアップロード  
  
****  
```shell:
./node_modules/.bin/clasp push
```  
  
ブラウザから見ると、コードがアップロードされているはずです。  
  
![image.png](/assets/images/7ffe6d0e-6435-7d41-591c-9b413ab66d4d.png)  
  
# manifestファイルを更新してアップロード  
  
Webアプリとして動かすため、manifestファイルを更新してアップロードします。  
  
**appsscript.json**  
```json:appsscript.json
{
  "timeZone": "America/New_York",
  "dependencies": {
  },
  "exceptionLogging": "STACKDRIVER",
  "webapp": {
     "access": "ANYONE_ANONYMOUS",
     "executeAs": "USER_DEPLOYING"
  }
}
```  
  
`"webapp"`を追加して、アクセス権と実行ユーザを指定します。  
  
| 設定 | 意味 |  
|:--|:--|  
| ANYONE_ANONYMOUS  | Googleにログインしていない匿名ユーザでもアクセスできるようにします。  |  
| USER_DEPLOYING  | デプロイしたユーザとしてスクリプトを実行します。  |  
  
  
manifestファイル全体の説明はこちら → https://developers.google.com/apps-script/concepts/manifests  
  
  
アップロードします。  
  
****  
```shell:
./node_modules/.bin/clasp push
```  
  
# WebアプリのURLを確認  
  
コマンドラインからURLを確認できればよいのですが、残念ながらその機能はないようです。  
  
ブラウザから確認します。  
  
![image.png](/assets/images/7216d2bd-b734-fbb7-2c58-e8bd88bff5ae.png)  
  
# お試し実行  
  
引数を付け加えてアクセスしてみます。  
  
https://script.google.com/macros/s/AKfycbx-wruSZhrRrMWy2oADdhmUeYeTAEaMCySF2wTXRw/exec?name=Sengokyu  
  
こんなんできました。  
  
![image.png](/assets/images/9c79e2b8-9fe0-6228-4950-e61c2a772b24.png)  
  
  
# おしまい  
  
お疲れ様でした。  
  
次は開発言語をTypeScriptに変更し、テストも導入したいと思います。  
  
  
