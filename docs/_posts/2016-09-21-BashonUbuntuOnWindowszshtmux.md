---
'created_at: ': '2016-09-21T17:24:59+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Bash
      versions: []
    - name: Zsh
      versions: []
    - name: Windows
      versions: []
    - name: BashOnUbuntuOnWindows
      versions: []
  state: 0
title: "Bash on Ubuntu On Windows\u306E\u30C7\u30D5\u30A9\u30EB\u30C8\u30B7\u30A7\u30EB\
  \u3092zsh\u3001\u3042\u308B\u3044\u306Ftmux\u306B\u3059\u308B\uFF08\u5225\u89E3\uFF09"
'updated_at: ': '2016-09-21T17:43:19+09:00'

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
  
