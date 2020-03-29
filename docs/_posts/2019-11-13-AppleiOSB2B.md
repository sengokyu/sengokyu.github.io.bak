---
'created_at: ': '2019-11-13T17:46:34+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: iPhone
      versions: []
    - name: iOS
      versions: []
    - name: apple
      versions: []
    - name: AppleBusinessManager
      versions: []
  state: 0
title: "[Apple] iOS\u5411\u3051\u306BB2B\u696D\u52D9\u30A2\u30D7\u30EA\u3092\u914D\
  \u4FE1\u3059\u308B\u624B\u7D9A\u304D\u307E\u3068\u3081"
'updated_at: ': '2019-12-07T12:37:35+09:00'

---
# これはなに？  
  
App Storeへ公開しない、B2B業務向けカスタムAppを配信する方法が分かりづらかったのでまとめました。  
  
# 必要な要件  
  
* アプリを使いたい組織への要件  
  * Apple Business Managerを契約していること  
* アプリを開発する組織への要件  
  * Apple Developer Programへ登録していること  
  * App Store Connectへ登録するアプリを特定組織向けに配信するように設定すること  
  
ちなみにApple Business Managerは無料です。  
  
# 手続き全体  
  
手続き全体は下記の図のようになります。  
  
![image.png](/assets/images/0e8d42d7-636d-dac6-741b-3ff9b6401658.png)  
  
図の作成には、とても便利で使いやすい https://draw.io/ を使いました。  
感謝！  
  
# 気をつけること  
  
Apple Business Managerで購入したアプリは、その組織内でしか利用できません。  
パートナー企業等に提供してはいけません。  
  
# 参考リンク  
  
* [Apple Business Managerユーザーガイド](https://support.apple.com/ja-jp/guide/apple-business-manager/welcome/web)  
* [VPPでカスタムB2B Appを配信する際について](https://qiita.com/miyae/items/426b8819a4f16f740d02)  
* [カスタムB2BアプリをVPPで配信する方法について　ざっくりなメモ](https://qiita.com/hosso/items/6333a2e840fb52af037d)  
* [ややこしいiOSアプリ配布方法の俯瞰とケースバイケースの配布方法選定](https://qiita.com/cross-xross/items/a5a9fceff706e3e740c3)  
  
