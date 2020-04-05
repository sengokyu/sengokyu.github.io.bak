---
date: '2018-05-28T01:57:19+09:00'
layout: post
published: true
qiita_article_id: 05f4f7eb2281bb4cd647
tags:
- Python
- Mac
- SensorTag
- BLE
- IoT
title: Texus Instruments SensorTag CC2650のRoll/Pitch/Yawを計算する
updated: '2018-05-28T23:54:31+09:00'

---
# はじめに  
  
このプログラムは作りかけです。  
RollとPitchはいい感じに取得できるのですが、Yawが時々暴走して明後日の値になってしまいます。きっと何か計算が間違っていると思うのですが、未だ原因特定できません。  
  
# 概要  
  
[TI SensorTag](http://www.tij.co.jp/ww/wireless_connectivity/sensortag/)は、いくつもセンサーを搭載した小さなデバイスです。BLEで接続し、それなりに簡単(?)にセンサーから値を取得できます。  
  
そのセンサーのうち、加速度、角速度、磁気（いわゆる9軸ジャイロ）から、デバイスがどちらに傾いているか、どちらに向いているかを計算します。  
  
# 環境  
  
- TI SensorTag CC2650 (Firmware 1.40 Mar 30 2017)  
- macOS High Sierra  
- Python 2.7.10  
- AdaFruit_BluefruitLE  
  
macOSで使えるPythonライブラリが無くて探しました。  
macOSで開発するなら、PythonよりNodeの方がいいかもしれません。  
  
# BLE接続について  
  
BLEって低電力なBluetoothだと思っていました。  
いえいえ、全然違いました。  
  
BLEデバイスは、以下のような手順で統一的に操作できる便利なやつでした。  
  
1. ご近所のデバイスをスキャン  
2. デバイスにあるサービスをリストアップ  
3. サービスにあるキャラクタリスティクスをリストアップ  
4. キャラクタリスティクスを指定して値を読み書き  
  
[Lightblue](https://itunes.apple.com/jp/app/lightblue/id639944780?mt=12)というアプリを使うと、上記の操作が簡単にできます。  
  
## Lightblueを使ったお試し  
  
Lightblueを起動すると、デバイスを検索してくれます。  
デバイスが見つかったら、F000AA80-0451-4000-B000-000000000000というサービスを辿ります。  
F000AA82-0451-4000-B000-000000000000というキャラクタリスティクスが設定とセンサーのOn/Off切り替えです。  
接続した直後はOffになっています。  
ここに`0x7f00`を書き込むと、F000AA81-0451-4000-B000-000000000000というキャラクタリスティクスからセンサーの値を読み込めます。  
  
<img width="1154" alt="スクリーンショット 2018-05-28 1.52.39.png" src="/assets/images/63d3a585-424d-c61d-da56-a9c3185d414d.png">  
  
<img width="771" alt="スクリーンショット 2018-05-28 1.53.09.png" src="/assets/images/0728bf26-e6de-98c0-f41c-728e9c207fda.png">  
  
どんな値を読み書きすればよいのかは、下記をご覧ください。  
http://processors.wiki.ti.com/index.php/CC2650_SensorTag_User%27s_Guide#When_connected  
  
# Roll/Pitch/Yawの計算について  
  
センサーから取得した値から単純に計算すると、何もしてなくてもどんどんずれていってしまいます。  
Qiitaを検索すると、Kalmanフィルタを使えという記事が見つかりました。  
でも、欲しいのはRoll/Pitch/Yawなのです、さらに探すとMadgwickというものが見つかりました。  
  
ありがたいことにC#のソースが公開されていたので、Pythonへ必要分を移植しました。  
http://x-io.co.uk/open-source-imu-and-ahrs-algorithms/  
  
論文はこちら ＞ [Madgwickアルゴリズム](http://x-io.co.uk/res/doc/madgwick_internal_report.pdf)  
  
# 作成したプログラム  
  
githubに置きました。  
  
https://github.com/sengokyu/ti-sensortag-movement-sample  
  
## プログラムの実行  
  
`sample/sample.py`ファイルを実行すると、60秒間Roll/Pitch/Yaw（というかオイラー角）を読み込んで画面に出力します。  
  
![sensortag-sample.mov.gif](/assets/images/ce909762-0c0d-26b3-59c1-8b381f12ef74.gif)  
  
# Help Me!  
  
地磁気を基準値として使うYawがおかしいです。  
センサーを動かしていなくても、どんどん値が変わっていくことがあります（変わらないこともあります）。  
どこをチェックすべきでしょうか。  
  
# 感想  
  
初Python、初BLE、初センサーと初ものづくしで、右も左もわからない。  
さらに4元数という未知の数字。  
なんにもわからないけれど、初めてできることは楽しいですね。  
  
