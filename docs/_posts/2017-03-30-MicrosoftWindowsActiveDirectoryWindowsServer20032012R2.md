---
'created_at: ': '2017-03-30T10:46:56+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Windows
      versions: []
    - name: Microsoft
      versions: []
    - name: ActiveDirectory
      versions: []
  state: 0
title: "[Microsoft][Windows] Active Directory\u30C9\u30E1\u30A4\u30F3\u30B3\u30F3\u30C8\
  \u30ED\u30FC\u30E9\u79FB\u884C\u4F5C\u696D\u8A18\u9332(Windows Server 2003\u304B\
  \u30892012 R2\u3078\uFF09"
'updated_at: ': '2017-03-30T10:46:56+09:00'

---
Windows Server 2003で動いていたActive DirectoryドメインコントローラをWindows Server 2012R2へ移行した作業の記録です。  
  
ネームサーバとDHCPサーバも移行します。  
  
# 環境  
  
## 既存マシン  
  
ホスト名: adm00n  
IPアドレス: 192.168.1.17/24  
OS: Windows Server 2003  
機能: ネームサーバ、DHCPサーバ  
  
## 新マシン  
  
ホスト名: adm00c  
IPアドレス: 192.168.1.4/24  
OS: Windows Server 2012 R2  
  
# はじめに  
  
富士通が詳しい手順書を公開してくれています。ありがたいことです。  
  
[Windows Server 2012 /2012 R2 Active Directory 移行の手引き](http://jp.fujitsu.com/platform/server/primergy/technical/construct/pdf/win2012-active-directory01.pdf)  
  
こちらに従って作業しました。  
  
  
# 手順記録  
  
## (1) ドメインの機能レベルを確認  
  
ドメインとフォレストの機能レベルを確認します。Windows Server 2003レベルになっていればOKです。  
  
## (2) 既存マシンがFSMOになっていないか確認  
  
FSMOになっていないか確認します。  
なっていたら、移動しないといけないです。  
  
## (3) 新マシンをドメインへ参加  
  
いつものやり方で新マシンをドメインへ参加させます。  
  
## (4) 役割のインストール  
  
新マシンに「Active Directory ドメインサービス」の役割をインストールします。   
  
## (5) 新マシンをドメインコントローラへ昇格  
  
新マシンをドメインコントローラへ昇格させます。  
  
サーバーマネージャの左上に通知が出ていますので、それをクリックします。  
  
既存のドメインにドメインコントローラを追加します。  
  
DNSサーバ、グローバルカタログをインストールします。  
サイト名は既存のものにします。  
  
「権限のある親ゾーンが見つからないか、Windows DNSサーバーが実行されていないため、このDNSサーバーの委任を作成できません。云々」  
と言われてしまいましたが、気にせず先へ進みます。   
  
インストールが終わると、自動的に再起動します。   
  
## (6) 既存マシンを降格  
  
既存マシンを降格させます。  
  
 と、dcpromoを起動したら、「グローバルカタログ」をやっていると出てきました。  
  
いったんやめます。  
  
## (7) グローバルカタログを削除  
  
既存マシンからグローバルカタログを削除します。  
  
やり方はこのあたり ===> https://support2.microsoft.com/kb/295419/ja  
  
## (8) 既存マシンを降格（再チャレンジ）  
  
既存マシンでdcpromoコマンドを起動します。  
  
ウィザードにしたがっていけば大丈夫でした。  
  
うっかりドメイン削除とかしたりしないように〜！  
  
ローカルAdministratorのパスワードも設定します。  
  
## (9) DHCPデータベースの移行  
  
既存マシンで  
  
```
netsh dhcp server export dhcp.txt all 
```  
  
としてファイルにデータベースをエクスポートします。  
  
ファイルを新マシンへコピーします。  
  
新マシンで  
  
```
netsh dhcp server import dhcp.txt all
```  
  
としてファイルを読み込みます。  
  
  
## (10) IPアドレスの付け替え  
  
既存マシン 192.168.1.17 => DHCP  
新マシン 192.168.1.4 => 192.168.1.17   
  
こうしないと、DNSを静的に設定しているクライアントが面倒ですしね。  
  
最後に全部再起動して動作確認したら終了です。  
  
お疲れ様でした。  