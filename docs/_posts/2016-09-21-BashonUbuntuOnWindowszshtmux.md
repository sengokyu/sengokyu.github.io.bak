---
date: '2016-09-21T17:24:59+09:00'
layout: post
published: true
qiita_article_id: dd3f0f6a13a6e0b925e8
tags:
- Bash
- Zsh
- Windows
- BashOnUbuntuOnWindows
title: Bash on Ubuntu On Windowsのデフォルトシェルをzsh、あるいはtmuxにする（別解）
updated: '2016-09-21T17:43:19+09:00'

---
  
zshを使うようになって久しいので、Windowsでもbashではなくzshを使いたいものです。  
  
やりかたはすでにいろいろ挙がっています  
  
- [Ubuntu on Windows で zshをデフォルトシェルにする](http://qiita.com/nsmr0604@github/items/7dd38faed1abd9189a83)  
- [Windows10のbashをzshで起動するように変更する](http://qiita.com/yutaszk/items/ca0425456b5027d2ee0e)  
  
ここでは、Windows側からアプローチしてみます。  
  
# ステップ1 ショートカットを開く  
  
[Start] > [Bash on Ubuntu on Windows]を右クリック > [More] > [Open File Location]を選びます。  
  
![bash1.png](/assets/images/2578a9c2-204f-352c-d211-de4ddb87a311.png)  
  
# ステップ2 ショートカットのプロパティを開く  
  
Bash on Ubuntu on Windowsファイルを右クリックして[properties]を選びます。  
  
![bash2.png](/assets/images/2a753554-f634-1526-5c45-345487c22b6e.png)  
  
  
# ステップ3 ターゲットを変更  
  
Targetを次のように変更します。  
  
**変更前**  
```text:変更前
C:\Windows\System32\bash.exe ~
```  
  
**変更後**  
```text:変更後
C:\Windows\System32\bash.exe ~ -c zsh
```  
  
![bash3.PNG](/assets/images/885d19cd-c93a-fa9d-fcd5-ef24bd175636.png)  
  
ここで`zsh`と書かずに`tmux`と書けば、いきなり`tmux`が起動します。  
  
  
# おしまい  
  
スタートメニューからBash on Ubuntu on Windowsを選んでみてください。  
もしすでにタスクバーへピンどめしているときは、もう一度ピンどめしなおしてください。  
お疲れ様でした。  
  
