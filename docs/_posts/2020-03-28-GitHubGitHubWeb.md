---
date: '2020-03-28T14:43:34+09:00'
layout: post
published: true
qiita_article_id: 39660d25e67debd7d131
tags:
- GitHub
- Jekyll
title: '[GitHub] GitHubを使って独自ドメインWebサイトを公開する方法'
updated: '2020-03-28T15:16:06+09:00'

---
先行記事多数ですが、自分のために書いています。お目汚し失礼します。  
  
# 公開Webサイトの作り方は3種類  
  
GitHubを使って[公開Webサイトを作る方法は3つ](https://help.github.com/en/github/working-with-github-pages/about-github-pages#types-of-github-pages-sites)あるそうです。  
  
* プロジェクト毎  
* ユーザー  
* 組織  
  
後者2つはGitHubのアカウントにひも付き、アカウントひとつにつき一サイトしか作れないそうです。  
  
プロジェクト毎のWebサイトを試してみます。  
  
# プロジェクト毎のWebサイトを作る手順  
  
## レポジトリの設定を開く  
  
レポジトリを開きます。README.mdしかない寂しいレポジトリですね。 :sweat_smile:   
  
https://github.com/sengokyu/eki-naka-nomi  
  
**Settings**をクリックします。  
  
![image.png](/assets/images/24412983-18c8-9bba-cc67-f295f1eae81b.png)  
  
  
  
## 公開サイトのソースを選ぶ  
  
3種類のソースから、公開元を選ぶことができます。  
  
* `gh-pages`ブランチ  
* `master`ブランチのルートディレクトリ  
* `master`ブランチの`docs`ディレクトリ  
  
`master`ブランチのルートディレクトリ を選んでみます。  
  
![image.png](/assets/images/6555e57e-2495-3ee0-1970-33a7e19dad39.png)  
  
画面が切り替わってURLが表示されました。  
  
![image.png](/assets/images/d10ccef3-4513-5dfc-78d5-4b6e5176b34e.png)  
  
## こんなんできました  
  
こんなんできました。  
http://sengokyu.github.io/eki-naka-nomi/  
![image.png](/assets/images/2c23734b-8a49-fc54-1950-7fa494732b93.png)  
  
  
## テーマを選択する  
  
画面が寂しいのでテーマを選択します。  
  
設定画面から、**Choose a theme** をクリックします。  
  
![image.png](/assets/images/0678b671-9db7-418b-f907-2301c6a5ae19.png)  
  
好きなテーマを選んで**Select theme**をクリックします。  
  
![image.png](/assets/images/3ee57659-1b71-5e0c-9208-427691cecc2b.png)  
  
  
## テーマが適用されました  
  
  
こんなんできました。  
http://sengokyu.github.io/eki-naka-nomi/  
  
![image.png](/assets/images/a3dbc6bf-2492-a459-52a5-a3c450ff4214.png)  
  
レポジトリに設定ファイルが増えています。  
https://github.com/sengokyu/eki-naka-nomi  
![image.png](/assets/images/bea1ee3c-c50e-680f-baa7-75e0a59721bf.png)  
  
公開Webサイトは[Jekyll](https://jekyllrb.com/)というものを使ってビルドされるそうです。あとで調べてみます。  
  
# 独自ドメインにする  
  
せっかくなので独自ドメインにします。  
  
ここでも3種類のやりかたがあるそうです。  
  
* `www`をつけた一般的なサブドメイン (例: www.example.com)  
* お好みのサブドメイン （例: blog.example.com)  
* サブドメインなし (例: example.com)  
  
お好みのサブドメインを試してみます。  
  
## ドメイン名を設定する  
  
お好みのドメイン名を入力して**Save**をクリックします。  
`ekinaka.uart.dev`にしてみました。  
  
![image.png](/assets/images/8628094d-1f36-cb80-830a-f8132b50094e.png)  
  
レポジトリに`CNAME`というファイルが増えていました。  
  
![image.png](/assets/images/cd7b5fdf-69a7-8cf1-da97-01cea506b438.png)  
  
入力したドメイン名が入っています。  
  
![image.png](/assets/images/38272508-4850-363c-28da-8b3dd0693718.png)  
  
## DNSレコードを追加する  
  
自分はGoogle Domainsを使っているので、そこからDNSレコードを追加しました。  
  
* 名前: お好みのサブドメイン  
* 種別: CNAME  
* TTL: デフォルト(1時間)  
* 値: ***ユーザー名***.github.io.  
  
![image.png](/assets/images/dd725598-60fa-65c8-2df9-2ae299a0ce00.png)  
  
## 確認  
  
できたかどうか確認してみます。  
  
なんでかAレコードが返ってきました。きっと誰かがよきにしてくれているのでしょう。。。  
  
```console
% dig ekinaka.uart.dev                                                                                                      (~/Documents/eki-naka-nomi)

; <<>> DiG 9.10.6 <<>> ekinaka.uart.dev
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 37316
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;ekinaka.uart.dev.              IN      A

;; ANSWER SECTION:
ekinaka.uart.dev.       3578    IN      A       185.199.111.153
ekinaka.uart.dev.       3578    IN      A       185.199.110.153
ekinaka.uart.dev.       3578    IN      A       185.199.109.153
ekinaka.uart.dev.       3578    IN      A       185.199.108.153
```  
  
## 開いて、、、開けない!? --- .dev ドメインの制限   
  
.dev ドメイン独自の話です。・  
.dev ドメインは https 接続必須で、httpでは開けません。  
  
* http://ekinaka.uart.dev/  :arrow_right: NG  
* https://ekinaka.uart.dev/ :arrow_right: OK  
  
  
  
## https必須にする  
  
設定を開いて**Enforce HTTPS**をチェックします。  
  
![image.png](/assets/images/dec5b924-e232-d89a-9790-f7fe8785e05c.png)  
  
こんな表示の時は待ちましょう。  
  
![image.png](/assets/images/090c5529-280a-9253-211b-aae8a5f2855a.png)  
  
## できた！  
  
繋がるようになりました。  
  
https://ekinaka.uart.dev/  
  
  
## 独自ドメイン設定時の注意 :warning:  
  
https://help.github.com/en/github/working-with-github-pages/about-custom-domains-and-github-pages#updating-custom-domains-when-your-github-pages-site-is-disabled  
  
GitHub Pages機能が無効な時に、カスタムドメインを設定してはいけません。  
他人があなたのドメインを使用してWebサイトを公開できます。  
  
  
# ページをカスタマイズする  
  
[静的ページはJekyllを使って生成される](https://help.github.com/en/github/working-with-github-pages/about-github-pages-and-jekyll)そうです。  
  
追加された`_config.yml`ファイルを変更することで色々できるみたいです。  
プラグインを使ったり作ったり(?)できるようです。作ったりは無理かな？  
  
## タイトルを設定する  
  
レポジトリをpullしてきて、`_config.yml`ファイルを編集します。  
  
![image.png](/assets/images/b3468ae4-ef11-cdc1-b31c-62017448c716.png)  
  
pushすると反映されました。  
  
https://ekinaka.uart.dev/  
![image.png](/assets/images/30f3695e-f01d-1575-0505-5c68898f8a7f.png)  
  
  
  
# 感想  
  
Markdown書いてpushするだけでWebサイトを更新できるの楽過ぎ。バージョン管理もできていい感じ。  
  
あとはハッシュタグ機能と、コメント機能（これはGitHubのIssueと繋げられたらよいな）を実現できれば移行できそうです。  
  
