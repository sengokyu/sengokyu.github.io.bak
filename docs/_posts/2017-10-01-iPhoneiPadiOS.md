---
date: '2017-10-01T13:15:38+09:00'
layout: post
published: true
qiita_article_id: 071daed92f2e4898e825
tags:
- iPhone
- iOS
title: '[iPhone][iPad][iOS] 家族で音楽ファイルを共有する方法を考える'
updated: '2017-10-01T13:15:38+09:00'

---
# ストーリー  
  
君が300枚のCDを持っていて、僕も300枚のCDを持っている  
--- そうね  
二人暮らしを始める部屋は手狭だから、荷物を減らしたいんだ  
--- そうね  
CDはリッピングしてオンラインに置かないか？  
--- いいわよ。私のiPhoneとiPadで聞けるようにしてね  
  
・・・せっせとリッピング  
  
ふぅー、終わった。  
全部で150GBか。  
適当に無料ストレージに入れようと思っていたけれど、とても収まらないな。。。  
NASとかサーバとか家に置きたくないし。。。  
どうしよう。。。 ＜ **イマココ！**  
  
（一部フィクションです）  
  
# 何はともあれ、iOSで音楽を再生する方法  
  
どこにファイルを置くにせよ、再生するアプリが必要ですよね。きっと。  
  
## Apple純正アプリ  
  
### ミュージック  
  
普通というか標準ですね。  
  
これが使えればよいのですけれど、音楽ファイルを入れるには、、、  
  
* iTunesを使って同期  
* iTunesを使ってホームシェアリング  
* iTunes Music Storeで購入してダウンロード  
* Apple Musicへ加入  
* サードパーティのアプリを使って同期 ← リーガルかイリーガルか微妙なところ。突然使えなくなるかもしれない  
  
、、、という方法しかないので使えないかな。  
  
### Podcast  
  
プライベートなPodcastは登録できるのかな・・・？  
・・・できなさそう。  
  
### Safari  
  
音楽ファイルを開けば再生できますね。  
毎回ダウンロードしますし、アルバムとして再生もできないですけれど。。。  
  
## サードパーティアプリ  
  
オンラインストレージに置いたファイルを再生してくれるアプリはいくつもありました。  
  
ぐぐる → https://www.google.co.jp/search?q=site%3Aitunes.apple.com+cloud+music+player  
  
  
どれがいいんだろう？？？  
ちょいちょいピックアップしてみました。  
  
| アプリ | 値段（円） | 対応オンラインストレージ | 備考  
|:--|:-:|:--|:-:|  
| [Jukebox](https://itunes.apple.com/jp/app/id1072583255?mt=8) | 240 | Dropbox  |  
| [Evermusic Pro](https://itunes.apple.com/jp/app/id905746421?mt=8) | 360 | Dropbox, Google Drive, OneDrive, Box, Mega, Yandex.Disk | SMB, WebDAVもサポート  
| [MusiNext Pro オフライン](https://itunes.apple.com/jp/app/id1146755549?mt=8)  | 480  | Dropbox, Google Drive, OneDrive, Box、ownCloud, Yandex.Disk |   |  
| [CloudBeats](https://itunes.apple.com/us/app/cloudbeats-offline-cloud-music-player/id573192227?mt=8) | 無料 | Google Drive, DropBox, Box, OneDrive, Amazon Cloud Drive, Mediafire, ownCloud | WebDAVもサポート  
| [My Cloud Music Offline](https://itunes.apple.com/jp/app/my-cloud-music-offline/id1030741417?mt=8) | 無料 | Dropbox, Google Drive, iCloud Drive  
| [Music Now 音楽プレーヤー](https://itunes.apple.com/jp/app/music-now-%E9%9F%B3%E6%A5%BD%E3%83%97%E3%83%AC%E3%83%BC%E3%83%A4%E3%83%BC/id1063762873?mt=8) | 無料 | Dropbox, Google Drive, iCloud Drive  
| [Musicloud](https://itunes.apple.com/jp/app/musicloud-%E3%82%AF%E3%83%A9%E3%82%A6%E3%83%89%E7%94%A8mp3%E3%81%A8flac%E9%9F%B3%E6%A5%BD%E3%83%97%E3%83%AC%E3%83%BC%E3%83%A4%E3%83%BC/id1040451985?mt=8) | 無料 | Dropbox, Google Drive, OneDrive   
  
[Box](https://www.box.com/)や[Mega](https://mega.nz/)というものを知りませんでした。  
  
# 使えるオンラインストレージ  
  
## 料金一覧  
  
| サービス名/プラン | 月額（円） | 容量 | 備考 |  
|:-:|:-:|:--|:--|  
| Dropbox/Plus  | 1,000  | 1TB |  
| OneDrive/Office365 Solo[^1]  | 1,274 | 1TB | Microsoft Officeも付いてくる |  
| Google Drive/1TB  | 1,300  | 1TB  
| iCloud/200GB  | 400 | 200GB | ファミリー共有を使えばOK？ |  
| Amazon Drive/Unlimited | 1,150（ひと月あたり） | 無制限 |  
| Box/Business | 1,800（一ユーザあたり） | 無制限  
| Mega/プロ・ライト | 約650（4.99ユーロ） | 200GB  
  
Office使うならMicrosoftよさげだけれど、iPhone使いだしApple iCloudかな。。。  
  
[^1]: 昔は、もっといろいろプランありませんでしたっけ？  
  
  
ほんとはAWS使ってぶいぶい言わせる記事書こうと思っていたのですが、だんだんただのアプリ比較記事になってきました。。。  
  
  
# いや、でもやっぱりAWSでぶいぶい。。。  
  
自分で作ろうかなぁ  
--- おとこのひとって、そういう無駄なこだわりあるわよね  
  
## 費用どれくらいかかりそう？  
  
### 見積もり前提  
  
- ストレージ容量は150GB  
- 音楽は1曲あたり5分、ファイルサイズは22MB  
- 週末は、音楽かけっぱなし（8時間 × 8日間 ÷ 5分 ＝ 768曲）  
- 通勤時ちょいちょい聞く（1時間 × 20日間 ÷ 5分 = 240曲）  
- 1ドル120円  
  
### 毎月これくらいかかりそう  
  
- S3料金<br>= 0.025USD/GB × 150GB × 120JPY/USD<br> = 450円  
- リクエスト料金<br>= 0.0037USD/10000リクエスト × 1008リクエスト × 120JPY/USD<br> = 5円弱  
- データ転送料金<br> = (22MB × 1008リクエスト ー 1GB) × 0.014USD/GB × 120JPY/USD <br>= 35円弱  
  
合計490円弱  
  
Standard IAなら安くなるかな・・・iCloudでいい気がしてきました。  
  
## 再生アプリはどうする？  
  
DIYですから、作らないといけないですね。  
心の声「おんなじやったら、こうたらええ」  
  
つづく。。。かもしれない  
