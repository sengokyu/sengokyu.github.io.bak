---
date: '2017-11-08T09:14:53+09:00'
layout: post
published: true
qiita_article_id: d86b57311a8e54ef7cb9
tags:
- Mac
- macos
title: '[Apple] macOS High Sierra私的環境設定記録'
updated: '2017-11-08T09:15:18+09:00'

---
久しぶりにMacを買いました。  
MavericksからHigh Sierraへ、かなーりバージョン飛んでも浦島感が薄いのはさすがです。<ディス>Windowsならこうはいかない</ディス>  
  
これは、新しいMacを使い始める上での私的な環境設定の記録です。  
お目汚し失礼します。  
  
  
# JapaneseIMのデフォルトスペース入力を半角にする  
  
ことえり時代は`defaults`コマンドでできたのですが、今はそういうのないそうですね。  
  
参照 → [macOS Sierraで日本語入力時に常にスペースを半角に](https://qiita.com/on0z/items/a9b4033cc088627b1728)  
  
参照先の情報に従って、plistファイルを変更します。  
  
```xml
--- KeySetting_Default.plist.bak	2017-10-22 12:54:34.000000000 +0900
+++ KeySetting_Default.plist	2017-10-22 13:00:39.000000000 +0900
@@ -11,12 +11,16 @@
 			<key>&apos; &apos;</key>
 			<dict>
 				<key>command</key>
-				<string>contextual_space</string>
+				<string>direct_input</string>
+				<key>character</key>
+				<string> </string>
 			</dict>
 			<key>shift+&apos; &apos;</key>
 			<dict>
 				<key>command</key>
-				<string>contextual_space_reverse</string>
+				<string>direct_input</string>
+				<key>character</key>
+				<string>　</string>
 			</dict>
 			<key>delete:</key>
 			<dict>
```  
  
Xcodeがあれば、Xcodeを使って編集するのが簡単かもしれません。  
  
変更前:  
<img width="517" alt="Screen Shot 2017-10-22 at 12.58.45.png" src="/assets/images/d49a94a8-b42a-7857-b56a-e770a73e79bf.png">  
  
変更後:  
<img width="500" alt="Screen Shot 2017-10-22 at 13.06.56.png" src="/assets/images/0d440964-7534-9d48-73dc-d71b5f65f2fc.png">  
  
# システム設定いろいろ  
  
* Display  
    * ResolutionをScaledにして画面を広く  
* Keyboard  
    * キーリピート間隔とリピート待ちを最小に  
* Sharing  
    * コンピュータ名を変更  
  
他にもやることあったような気がしたのですがなかったです。  
  
   
# Terminalの設定関係  
  
## シェルをzshにする  
  
```shell-session
chsh -s /bin/zsh
```  
  
## 日本語周り  
  
半角全角両方ある文字幅を全角にする -> [Unicode East Asian Ambiguous characters to wide]をオンにする。  
  
## 見た目周り  
  
[Antialias Text]をオンにする  
  
  
# ソフトウェアのインストール  
  
インストールしたものたちです。  
  
* XCode  
* Firefox  
* Firefox Developer Edition  
* Google Chrome  
* Google Chrome Canary  
* Dash for macOS  
  
## XCode Developer Tools  
  
コマンドラインツールを入れます。  
  
```shell-session
xcode-select --install
```  
  
  
# Homebrewのインストール  
  
  
`~/opt/brew`にインストールしました。  
  
```shell-session
mkdir opt
cd opt
git clone https://github.com/Homebrew/brew.git
```  
  
~/.zprofileにパスを追加します。  
  
**~/.zprofile**  
```zsh:~/.zprofile
path=(
    ${HOME}/opt/brew/bin(N-/)
    ${path}
)

typeset -U path
```  
  
  
# Homebrewからインストール  
  
Homebrewからインストールしたものたちです。  
  
* tmux  
* cask/java (Oracle Java 9)  
* cask/netbeans-java-se (NetBeans Java SE用)  
* cask/visual-studio-code (Visual Studio Code)  
* mysql  
* cask/sequel-pro (Sequel Pro)  
* cask/alfred  
* php/php70  
  
## Nodeのインストール  
  
```shell-session
brew install nodenv
```  
  
```shell-session
nodenv install 8.8.0
```  
  
nodenvの初期化を~/.zprofileで行います。  
  
**~/.zprofile**  
```zsh:~/.zprofile
eval $(nodenv init -)
```  
  
  
## Emacsのインストール  
  
```shell-session
brew install emacs
```  
  
Emacs 25.3.1が入りました。  
  
