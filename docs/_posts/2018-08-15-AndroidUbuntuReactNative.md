---
date: '2018-08-15T12:37:00+09:00'
layout: post
published: true
qiita_article_id: 5555fcc10980b7780714
tags:
- Android
- Ubuntu
- React
- reactnative
title: '[Android] Ubuntuで始めるReact Native'
updated: '2018-08-22T16:14:06+09:00'

---
# これはなに？  
  
React Nativeを使ってAndroidアプリ開発環境を構築したメモです。  
お目汚し失礼します。  
  
React Nativeのバージョンは 0.56 です。  
  
  
# あらかじめ必要なもの  
  
* Node 8.3以降とnpm  
* JDK 8以降  
* Android Studio  
* ~~Android SDK~~  
  
順番にインストールしていきます。  
  
## Node 8.3以降とnpm  
  
nodesourceからインストールします。  
  
```shell-session
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get update
sudo apt-get install nodejs
```  
  
参考: https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions  
  
## JDK 8以降  
  
```shell-session
sudo apt-get install openjdk-8-jdk
sudo update-java-alternatives --set java-1.8.0-openjdk-amd64
```  
  
## Android Studio  
  
`snap`を使用してインストールします。  
  
****  
```shell-session:
snap install --classic android-studio
```  
  
snapの外へアクセスできるようにするため`--classic`オプションを付けます。  
  
  
## Android SDK  
  
[Getting Started](https://facebook.github.io/react-native/docs/getting-started.html)に書いてある以下のものは不要みたいです。  
  
* Android 6.0 (Marshmallow  
    * Google APIs  
    * Android SDK Platform 23  
    * Intel x86 Atom_64 System Image  
    * Google APIs Intel x86 Atom_64 System Image  
  
* Android SDK Build-Tools  
    * 23.0.1  
  
環境変数も設定しなくてOKです。  
  
  
やっと準備が整いました。 :sweat_smile:   
  
# React Nativeを始める  
  
## CLIをインストール  
  
CLIをインストールします。  
  
```shell-session
sudo npm install -g create-react-native-app
```  
  
ドキュメントによっては[react-native-cli](https://www.npmjs.com/package/react-native-cli)を使っています。正解は？  
  
  
## プロジェクトを作成  
  
CLIを使ってさくっと。  
  
****  
```shell-session:
create-react-native-app AwesomeProject
```  
  
こんなファイル達ができました。  
  
```
App.js
app.json
App.test.js
.babelrc
.gitignore
node_modules/
package.json
package-lock.json
README.md
.watchmanconfig
```  
  
`App.js`の中身はこれだけです。  
  
**App.js**  
```jsx:App.js
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';

export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text>Open up App.js to start working on your app!</Text>
        <Text>Changes you make will automatically reload.</Text>
        <Text>Shake your phone to open the developer menu.</Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```  
  
`package.json`にある依存性はこれだけです。  
  
****  
```json:
"expo": "^27.0.1",
"react": "16.3.1",
"react-native": "~0.55.2"
```  
  
ここにある[Expo](https://docs.expo.io/versions/v29.0.0/introduction/)はこういうものらしいです。  
  
> Expo is a set of tools, libraries and services which let you build native iOS and Android apps by writing JavaScript.  
  
JavaScriptでネイティブアプリを作るためのツール集ですね。  
  
## 実行  
  
`npm`コマンドから実行します。  
  
****  
```shell-session:
npm start
```  
  
ターミナルにこんなメッセージが出ました。  
  
![Selection_144.png](/assets/images/311ab8d8-46c4-66c4-4e1f-d69e902d30d6.png)  
  
端末にExpoクライアントをインストールして、QRコードを読めばプログラムが動くようです。QRコードが出るって新しいかも。  
  
* [iOS用Expoクライアント](https://itunes.apple.com/app/apple-store/id982107779)  
* [Android用Expoクライアント](https://play.google.com/store/apps/details?id=host.exp.exponent)  
  
自分はエミュレータで動かします。Android Studioからエミュレータを起動します。  
  
エミュレータが起動したら、先ほどのターミナルで`a`キーを押します。  
  
こんなメッセージが出ました。  
  
![Android Emulator - Nexus_5X_API_28_x86:5554_146.png](/assets/images/d58f5b58-3521-d3eb-a919-6bd0734368d2.png)  
  
よくわからないですが設定を有効にして再度`a`キーを押したらアプリが動きました。  
  
![Android Emulator - Nexus_5X_API_28_x86:5554_148.png](/assets/images/2ee36e87-c180-0419-e65c-a725323cfab9.png)  
  
# プログラムを少し修正  
  
メッセージと色を変えてみました。  
  
**App.js**  
```jsx:App.js
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';

export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text>こんにちは！リアクトネイティブ</Text>
        <Text>Changes you make will automatically reload.</Text>
        <Text>Shake your phone to open the developer menu.</Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#ccf',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```  
  
即座に反映されました。  
  
![Android Emulator - Nexus_5X_API_28_x86:5554_149.png](/assets/images/dbd3cee5-a921-9e6f-2334-c9ab88c5ff48.png)  
  
  
# 落ち穂拾い  
  
## `create-react-native-app`と`react-native-cli`  
  
Create React Native App(CRNA)は、[Expo](https://expo.io/)サービスを使うようにできているそうです。  
  
[EJECTING.md](https://github.com/react-community/create-react-native-app/blob/master/EJECTING.md)によりますと、App StoreやPlay Storeへ配置するにはCRNAから脱却しないといけないそうです。  
  
### CRNAからの脱却  
  
脱却先は2つあります。  
  
* いつものReact Native  
* ExpoKit  
  
いつものReact Nativeのほうでは、`react-native`コマンドを使って作業するようになります。  
  
ExpoKitのほうでは、Expoのサービスを使い続けられます。  
  
### 脱却してみる  
  
いつものReact Nativeに脱却してみます。  
  
****  
```shell-session:
npm run eject
```  
  
React NativeにするかExpoKitにするか質問が表示されますので、React Nativeを選びます。  
  
![image.png](/assets/images/7f4b7b0c-7bbe-c04b-1152-4b4526f410c0.png)  
  
さらにいくつかの質問に答えると、いろいろファイルが書き換えられました。  
  
`android`と`ios`ディレクトリができて、それぞれにネイティブ用のプロジェクトが格納されています。  
  
依存性からはExpoが消えました。  
  
### CLIをインストール  
  
`react-native-cli`をインストールします。  
  
****  
```shell-session:
sudo npm install -g react-native-cli
```  
  
環境変数`ANDROID_HOME`を設定  
  
**~/.zshrc**  
```zsh:~/.zshrc
export ANDROID_HOME=${HOME}/opt/Android/Sdk
```  
  
  
### 実行  
  
エミュレータを起動します。  
  
サーバを起動します。  
  
****  
```shell-session:
npm run start
```  
  
エミュレータ内でアプリを起動します。  
  
****  
```shell-session:
npm run android
```  
  
  
  
  
## KVMを使う  
  
エミュレータを高速に動かすためにKVMをインストールします。  
  
****  
```shell-session:
sudo apt-get install qemu-kvm libvirt-bin
```  
  
`/dev/kvm`を開けるように、自分自身を`kvm`グループに追加します。  
  
```shell-session
sudo usermod --append kvm ログインID
```  
  
## gradleのヒープサイズを大きくする  
  
デフォルト1024MBだとコンパイルが遅いので、最低1536MBにします。  
  
**~/.zshrc**  
```zsh:~/.zshrc
export GRADLE_OPTS=-Dorg.gradle.jvmargs=-Xmx2048M
```  
  
## エミュレータが固まったら？  
  
Cold Bootするとよいです。  
  
![image.png](/assets/images/bef767e7-73ba-6a0e-102c-15a4210bf6ec.png)  
  
  
# 感想  
  
iOSとAndroidのアプリをソース一本で書けるのは、相当な効率アップです。  
  
何かハマリどころが出てきた際、ネイティブの知識に＋してReact Native + Reactの知識が必要になるので大変そうです。React Nativeを採用するかどうかは、作成するアプリケーションの規模と複雑さに応じてといったところでしょうか。  
  
すでにiOS/Androidの十分な知識があるひとには、React Nativeとてもいいと思います。  
  
  
  
  
  
  
  
  
