---
date: '2016-10-03T21:09:41+09:00'
layout: post
published: true
qiita_article_id: 51e3e8837d3ce6a04ea0
tags:
- Lubuntu
title: '[Lubuntu][Linux] Lubuntu 16.04で、Caps LockをCtrlにする方法いろいろ'
updated: '2016-10-03T21:09:41+09:00'

---
Ubuntu 16.04を入れたあと、`apt-get install lubuntu-desktop`してLubuntuにした環境でのお話です。  
  
とりあえずはできるようになったのですが、正しいお作法教えてください。  
  
  
# :x: 方法1 /etc/default/keyboard を編集  
  
情報源 → http://micchan.com/tips/2016/01/17/replace-ctrl-key-with-caps-lock-key-of-lubuntu/  
  
/etc/default/keyboardに次のような行を付け加え、再起動します。  
  
**/etc/default/keyboard**  
```text:/etc/default/keyboard
XKBOPTIONS="ctrl:nocaps"
```  
  
結果、だめでした。  
  
# :x: 方法2 ~/.config/lxsession/Lubuntu/autostart を編集  
  
情報源 → http://qiita.com/tnarihi/items/a46bcf96558951505d98  
  
~/.config/lxsession/Lubuntu/autostart に次のような行を付け加えてログインし直します。  
  
**~/.config/lxsession/Lubuntu/autostart**  
```text:~/.config/lxsession/Lubuntu/autostart
@setxkbmap -option "ctrl:nocaps"
```  
  
結果、だめでした。  
  
LXDEのWikiによると、autostart内の各行は、スペースで区切られた先頭要素しか読み込まないみたいです。引数があるならシェルスクリプトを用意しろとか。 → https://wiki.lxde.org/en/LXSession  
  
# :x: 方法3 ~/.config/lxsession/Lubuntu/desktop.confを編集  
  
desktop.confファイルのKeyboadセクションにある行を書き換えて、ログインし直します。  
  
**~/.config/lxsession/Lubuntu/desktop.conf**  
```text:~/.config/lxsession/Lubuntu/desktop.conf
[Keymap]
options=ctrl:nocaps
```  
  
結果、だめでした。  
  
# :o: 方法4 シェルスクリプトでsetxkbmapを実行  
  
簡単なシェルスクリプトを用意します。  
  
**~/bin/lx-caps2ctrl**  
```bash:~/bin/lx-caps2ctrl
# !/bin/sh
exec setxkbmap -option caps:nocaps
```  
  
作成したシェルスクリプトをautostartから実行するようにします。  
  
**~/.config/lxsession/Lubuntu/autostart**  
```text:~/.config/lxsession/Lubuntu/autostart
@/home/ofyou/bin/lx-caps2ctrl
```  
  
結果、うまくいきました。  
  
# :interrobang: 方法5 ~/.config/autostart/setxkbmap.desktopを作成  
  
  
試していないですが、こんなファイルを作ればよいみたいです。  
  
**~/.config/autostat/setxkbmap.desktop**  
```text:~/.config/autostat/setxkbmap.desktop
[Desktop Entry]
Type=Application
Name=Caps2Ctrl
Exec=setxkbmap -options caps:nocaps
OnlyShowIn=LXDE
```  
  
  
  
