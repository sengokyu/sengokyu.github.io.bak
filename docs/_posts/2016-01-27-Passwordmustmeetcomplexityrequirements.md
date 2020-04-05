---
date: '2016-01-27T10:19:21+09:00'
layout: post
published: true
qiita_article_id: 5de27e00ec5ffa8b76a9
tags:
- Windows
- Hyper-V
title: コマンドラインから「パスワードは、複雑さの要件を満たす必要がある(Password must meet complexity requirements)」ポリシーを無効にする
updated: '2016-01-27T10:19:21+09:00'

---
# 背景  
  
近頃のWindows Serverは、既定で「パスワードは、複雑さの要件を満たす必要がある」ポリシーが有効になっていて、単純な英字だけのパスワードなどを設定できません。Hyper-Vも同様です。  
  
GUIがあればgpedit.mscから変更できますが、Hyper-VはGUIがないのでコマンドラインから作業する必要があります。  
  
# 作業手順  
  
seceditというコマンドを使います。ポリシー全体をファイルから読み込んであげるという使い方しかできないそうです。 :frowning:   
  
## 1. 現在の設定をエクスポートする  
  
****  
```cl:
secedit /export /areas SECURITYPOLICY /cfg export.inf
```  
  
export.infというファイルに現在の設定をエクスポートします。  
  
## 2. ファイルを変更する  
  
System AccessというセクションにPasswordComplexityという行があるので、値を1から0に変更します。  
  
変更前：  
  
****  
```ini:
[System Access]
PasswordComplexity = 1
```  
  
変更後：  
  
****  
```ini:
[System Access]
PasswordComplexity = 0
```  
  
## 3. 変更したファイルを読み込む  
  
****  
```cl:
secedit /configure /db secedit.sdb /areas SECURITYPOLICY /cfg export.inf
```  
  
以上です。 :smile:   
  
# 参考  
  
- [secedit コマンドによるローカルセキュリティポリシーの操作](http://orebibou.com/2013/09/secedit-%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E3%81%AB%E3%82%88%E3%82%8B%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E3%82%BB%E3%82%AD%E3%83%A5%E3%83%AA%E3%83%86%E3%82%A3%E3%83%9D%E3%83%AA%E3%82%B7%E3%83%BC/)  
- [Windowsコマンド集](http://itpro.nikkeibp.co.jp/article/Windows/20051013/222731/)  
