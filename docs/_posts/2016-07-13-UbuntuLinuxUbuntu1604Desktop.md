---
date: '2016-07-13T13:16:20+09:00'
layout: post
published: true
qiita_article_id: 856da873736d8103a8b6
tags:
- Ubuntu
title: '[Ubuntu][Linux]Ubuntu 16.04 Desktop私的初期設定まとめ'
updated: '2016-10-03T20:34:49+09:00'

---
Ubuntu 16.04インストール後に行った設定をまとめました。  
  
# アップデート  
  
```
$ sudo apt-get update
$ sudo apt-get upgrade
```  
  
コマンドを実行しました。  
  
# ホームディレクトリをオートマウント  
  
NFSサーバにあるホームディレクトリをオートマウントします。  
  
## autofsをインストール  
  
```
$ sudo apt-get install autofs
```  
  
## rootユーザを有効にする  
  
```
$ sudo passwd root
$ sudo passwd -u root
```  
  
## Xを抜けてコンソールへログイン  
  
Ctrl+Alt+F1キーを押して、コンソールへ切り替え、rootユーザでログインします。  
  
## autofsを設定  
  
`/etc/auto.master`ファイルを編集します。  
  
**/etc/auto.master**  
```text:/etc/auto.master
/home  /etc/auto.home
```  
  
`/etc/auto.home`ファイルを作成します。  
  
**/etc/auto.home**  
```text:/etc/auto.home
*       -fstype=nfs,soft,intr,nodev,nosuid,noatime,async NFSSERVER:/EXPORTS/&
```  
  
NFSv4の場合は、fstypeのところをnfs4にします。  
  
autofsの設定を再読み込みします。  
  
```
$ service autofs reload
```  
  
存在するユーザのホームディレクトリへアクセスして確認します。  
  
```
$ ls /home/USER
```  
  
# 設定ツールをインストール  
  
続く作業を楽にするためにインストールしました。  
  
```
$ sudo apt-get install unity-tweak-tool
$ sudo apt-get install gnome-tweak-tool
$ sudo apt-get install compizconfig-settings-manager
```  
  
  
# 日本語入力  
  
## インストール  
  
英語状態でインストールしたので、IMEが有効になっていませんでした。  
  
All Settings > Language Supportを起動します。  
足りないものをインストールするかと聞かれるのでインストールします。  
  
**Keyboard input method system**をfcitxにします。  
  
All Settings > Text Entryを起動します。  
[+]ボタンをクリックして、「Japanese（Mozc)」を追加します。  
  
ついでに、現在の入力ソースをメニューバーへ表示するようにしました。  
  
有効にするために、一度ログアウトしてログインし直します。  
  
## 入力切替キーをAlt+チルダへ変更  
  
デフォルトだと、Windowsキー+チルダなので、Alt+チルダに変更します。  
  
### Alt+チルダに割り当てられているウィンドウ切り替えを無効にする  
  
CompizConfig Settings Manager ＞ Ubuntu Unity Plugin ＞ Switherタブを開きます。  
  
**Key to flip through windows in the Switcher**を変更します。なぜかDisabledのままだと有効になっているので、適当な使わなそうなキーに割り当てます。  
  
### Altキーを押した時に表示されるHUDを無効にする  
  
メニューを名前で操作できるHUDは、英語圏だと便利そうですね。  
  
Generalタブにある**Key to show HUD when tapped**を適当な使わなそうなキーに割り当てます。  
  
### Alt+チルダにIME起動を割り当てる  
  
All Settings > Text Entryを起動します。  
Mozc(Fcintx)を選び、設定ボタンをクリックします。  
  
Global Configタブを選び、Ｔｒｉｇｇｅｒ Input Methodを ``Alt+``` にします。  
  
  
  
# Caps Lockキーをコントロールキーにする  
  
## Ubuntu Unityの場合  
  
```
$ gsettiongs set org.gnome.desktop.input-sources xkb-options ['ctrl:nocaps']"
```  
  
## Xなしコンソールの場合  
  
`/etc/kbd/remap`ファイルに設定が用意されているので、有効にします。  
  
**/etc/kbd/remap**  
```text:/etc/kbd/remap
# This turns caps lock into control
s/keycode  58 = Caps_Lock/keycode  58 = Control/;
```  
  
# シェルをzshにする  
  
```
$ sudo apt-get install zsh
$ chsh -s /bin/zsh
```  
  
# 時計をローカルタイムゾーンにする  
  
Windowsも動かすので、RTCのタイムゾーンをローカルにしました。  
  
```
$ sudo timedatectl set-local-rtc 1
```  
  
こんな感じになりました。時計をローカルタイムゾーンにするのは、おすすめされないみたいです。  
  
```
$ timedatectl 
      Local time: 火 2016-07-05 10:28:20 JST
  Universal time: 火 2016-07-05 01:28:20 UTC
        RTC time: 火 2016-07-05 10:28:20
       Time zone: Asia/Tokyo (JST, +0900)
 Network time on: yes
NTP synchronized: no
 RTC in local TZ: yes

Warning: The system is configured to read the RTC time in the local time zone.
         This mode can not be fully supported. It will create various problems
         with time zone changes and daylight saving time adjustments. The RTC
         time is never updated, it relies on external facilities to maintain it.
         If at all possible, use RTC in UTC by calling
         'timedatectl set-local-rtc 0'.

```  
  
# 使い勝手いろいろ  
  
## メニューバーの時計に月日、曜日を表示  
  
すべての設定 ＞ 時刻と日付 ＞ 時計タブ  
  
時計の表示設定で「曜日」と「月日」をチェックします。  
  
## ランチャーのアイコンを小さくする  
  
すべての設定 ＞ 外観 ＞ 外観タブ  
  
Launcherのアイコンサイズを変更  
  
## 常にメニューを表示  
  
すべての設定 ＞ 外観 ＞ 挙動  
  
メニューの表示を「常に表示」へ変更  
  
## ホットコーナーを有効化  
  
Unity Tweak Tool ＞ ウィンドウマネージャー ＞ Hotcorners  
  
画面左下を「ウィンドウスプレッド」にしました。  
  
## 画面端にマウスが引っかかるのを無効にする  
  
マルチディスプレイにしてディスプレイ間側にランチャーがあると、ランチャーにマウスが引っかかります。  
  
CompizConfig Settings Manager ＞ デスクトップ ＞ Ubuntu Unity Plugin ＞ Launcherタブ  
  
「Launcher Capture Mouse」をオフにしました。  
  
  
# Multicast DNSを削除  
  
.localをDNSドメイン名として使っているため、Multicast DNSと喧嘩していました。  
削除してしまいます。  
  
```
$ sudo apt remove libnss-mdns
$ sudo apt autoremove
$ sudo apt autoclean
```  
  
# KVMで仮想化  
  
## インストール  
  
```
$ sudo apt-get install qemu-kvm libvirt-bin ubuntu-vm-builder bridge-utils
```  
  
GUI管理ツールも入れます。  
  
```
$ sudo apt-get install virt-manager
```  
  
自身が管理者であればlibvirtdグループに追加されているはずなので、いったんログアウトして再度ログインします。  
  
### Windowsゲスト用ドライバ  
  
fedoraのサイト https://fedoraproject.org/wiki/Windows_Virtio_Drivers からISOファイルをダウンロードし、`/var/lib/libvirt/images`に置きます。  
  
  
# いろいろインストール  
  
`apt-get`でインストールしました。  
  
- emacs  
- npm  
- lv  
- default-jre  
- openssh-client-ssh1 <br> ssh version 1で繋がないといけないところがあるので。。。  
  
## Oracle JDK  
  
http://www.oracle.com/technetwork/java/javase/downloads/index.html から.tar.gzをダウンロードし、/optの下に展開しました。  
  
```
$ sudo tar zxf Downloads/jdk-8u102-linux-x64.tar.gz -C /opt 
```  
  
便利のために、シンボリックリンクをはります。  
  
```
$ sudo ln -s jdk1.8.0_102 /opt/jdk
```  
  
  
## NetBeans  
  
aptにもありますが、 https://netbeans.org/downloads/ からダウンロードしてインストールしました。  
  
```
$ sudo sh Downloads/netbeans-8.1-javaee-linux.sh
```  
  
### 見た目関係  
  
デフォルトの見た目はよろしくないので、Tools > Options > Appearance > Look and Feelで、GTK+からMetalに変更しました。  
  
`/usr/local/netbeans-8.1/etc/netbeans.conf`ファイルを編集し、  
`netbeans_default_options`に次のオプションを追記しました。  
  
```
-J-Dawt.useSystemAAFontSettings=lcd
```  
  
### プラグイン  
  
- findbugs  
- moreemacs  
  
## remmina  
  
デフォルトで入っているバージョンは、フォントのアンチエイリアスが効かなかったので、最新のもの（バージョン2.0）を入れました。  
  
```
sudo apt-get remove remmina
sudo apt-get autoremove
sudo apt-add-repository ppa:remmina-ppa-team/remmina-next
sudo apt-get install remmina
```  
  
キーボードグラブ関係のストレスもなくなっていい感じ。  
  
## Firefox developer edition  
  
デフォルトのFirefoxもdeveloper editionも両方使いたいので、開発者チャンネルは使わずに別途インストールしました。  
  
https://www.mozilla.org/en-US/firefox/developer/ からtarボールをダウンロードします。  
`~/opt/firefox`へ展開します。  
  
ランチャー用ファイルを作成します。  
  
**~/.local/share/applications/firefox_dev.desktop**  
```text:~/.local/share/applications/firefox_dev.desktop
[Desktop Entry]
Name=Firefox Developer 
GenericName=Firefox Developer Edition
Comment=Firefox Developer Edition Web Browser.
Exec=/home/myname/opt/firefox/firefox %u
Terminal=false
X-MultipleArgs=false
Type=Application
Icon=/home/myname/opt/firefox/browser/icons/mozicon128.png
Categories=GNOME;GTK;Network;WebBrowser;
MimeType=text/html;text/xml;application/xhtml+xml;application/xml;application/rss+xml;application/rdf+xml;image/gif;image/jpeg;image/png;x-scheme-handler/http;x-scheme-handler/https;x-scheme-handler/ftp;x-scheme-handler/chrome;video/webm;application/x-xpinstall;
StartupNotify=true
```  
  
実行パーミッションをつけます。  
  
```
$ chmod +x ~/.local/share/applications/firefox_dev.desktop
```  
  
ファイルをダブルクリックして、Firefoxが起動するか確かめます。  
  
ログアウト/ログインすれば、dashに表示されるはずです。  
  
  
# デスクトップ壁紙  
  
よさげなのを探して、いろいろ入れて試しています。  
  
- wallpaper  
- ubuntustudio-wallpapers  
  
  
  
  
