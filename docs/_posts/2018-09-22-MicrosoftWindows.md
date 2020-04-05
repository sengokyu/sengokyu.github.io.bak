---
date: '2018-09-22T20:36:07+09:00'
layout: post
published: true
qiita_article_id: fb4948e0d2746e3cc26f
tags:
- .NET
- WPF
- WinForms
- Electron
- UWP
title: '[Microsoft] Windowsデスクトップ向け業務アプリ開発には何を採用すべきか？'
updated: '2020-03-03T18:21:58+09:00'

---
Windowsデスクトップ向けの業務アプリケーション開発において、どのようなテクノロジを採用すべきか考えたいと思います。  
  
# Win32 API  
  
そういえばこの手もありましたね。  
これじゃないといけない要件があるのなら仕方ないです。  
  
<dt>開発言語  
<dd>C++等   
</dd>  
<dt>配置方法  
<dd>Windowsインストーラ、あるいは実行ファイルをコピー  
</dd>  
<dt>更新方法  
<dd>Windowsインストーラを再配布、あるいは実行ファイルをコピー  
</dd>  
  
# Windows Forms (WinForms)  
  
.Net Framework 1.0とともに登場した最も歴史ある（もうあまり新規開発がなさそうな）テクノロジです。  
高DPI環境で難がありましたが、.Net Framework 4.7 + Windows 10で解決するようです。[^1]  
  
[^1]: Windows フォームでの高 DPI サポート https://docs.microsoft.com/ja-jp/dotnet/framework/winforms/high-dpi-support-in-windows-forms  
  
  
<dt>開発言語  
<dd>C#、VB.NET等  
</dd>  
<dt>配置方法  
<dd>ClickOnceがおすすめ  
</dd>  
<dt>更新方法  
<dd>ClickOnceにお任せ  
</dd>  
<dt>よいところ  
<dd>  
* 古くからあるので、開発者を確保しやすい（あるいはこれしかできない）かもしれない。  
* 所有しているUIコントロールが、WinFormsにしか対応していなかったりするかもしれない（そんなん捨てたほうがいい）  
</dd>  
<dt>いけてないところ  
<dd>  
* コントロール配置が固定なので、大画面環境になっても使いやすさが向上しない。  
* 高DPI(125%とか200%とかの)環境だと、画面が崩れて使えない。  
* いかんせん古い。  
* テストを書きづらい。あるいはできない。:zap:  
* Windowsの設定を変更しないとIMEを制御できない :zap: :zap: :zap: :zap: :zap:  
</dd>  
  
# Windows Presentation Framework (WPF)  
  
.Net Framework 3.0とともに登場しました。  
XAMLで作る画面はHTMLのフローレイアウトっぽく扱えるので、画面サイズに応じたアプリを作りやすいです。美麗な画面も作りやすいです。  
  
<dt>開発言語  
<dd>C#、VB.NET等  
</dd>  
<dt>配置方法  
<dd>ClickOnceがおすすめ  
</dd>  
<dt>更新方法  
<dd>ClickOnceにお任せ  
</dd>  
<dt>よいところ  
<dd>  
* 大画面、高DPI環境に対応している。  
* 美麗なUIを作りやすい。  
* MVVMアーキテクチャやPrismを利用してテストしやすいコードを書ける。 :zap:   
</dd>  
<dt>いけてないところ  
<dd>  
* 未だWinFormsやっているところは開発者がいないかもしれない。  
* 最近のMicrosoftはUWPにばかり注力してるよね？  
</dd>  
  
  
# Universal Windows Platform (UWP)  
  
Windows ~~10~~ 8とともに登場しました。  
セキュリティのためか、プラットフォーム非依存にするためか、アプリケーション外との接続が面倒だったりできなかったりしました。Windows 10 ver.1709以降なら、そのような制限はありません。  
  
作成したアプリケーションを配置するには、Microsoftストアを使うかサイドローディングを設定する必要があります。  
  
<dt>開発言語  
<dd>C#、VB.NET、JavaScript等  
</dd>  
<dt>配置方法  
<dd>  
* Microsoftストアから配信  
* プライベートストアから配信  
* Windows 10の設定を変更してサイドローディング  
</dd>  
<dt>更新方法  
<dd>  
* Windows 10にお任せ  
</dd>  
<dt>よいところ  
<dd>  
* インストールに管理者権限を必要としない。  
* 更新が簡単。  
* 大画面、高DPI環境に対応している。  
* 美麗なUIを作りやすい。  
</dd>  
<dt>いけてないところ  
<dd>  
* 実質Windows 10 ver.1709以降が必要。  
* 配置方法が面倒。  
* 1画面1スレッドなので、複数画面使うときはそれなりのコツが必要。  
* 欲しいUIコントロールがないかもしれない。  
</dd>  
  
  
# Electron  
  
ご存知Visual Studio Codeの開発に使われています。  
  
<dt>開発言語  
<dd>JavaScript、TypeScript  
</dd>  
<dt>配置方法  
<dd>  
* パッケージを作成して実行してもらう。  
</dd>  
<dt>更新方法  
<dd>  
* Electronの機能を使える。  
</dd>  
<dt>よいところ  
<dd>  
* Googe Chromeでできることが全部できる。  
</dd>  
<dt>いけてないところ  
<dd>  
* 頑張らないと重くなる。  
* 気をつけないとXSSする。  
</dd>  
  
# Xamarin :zap: :zap:   
  
iOS/AndroidアプリをC#で開発できるXamarinです。Windowsアプリも作れます。  
  
<dt>よいところ  
<dd>  
* iOS/Androidとビジネスロジックコードを共有できる。  
</dd>  
<dt>いけてないところ  
<dd>  
* ビルドしてデバッグ実行するまでが、それなりに一苦労（でした。今はどうでしょう？）。  
</dd>  
  
# Delphi :zap: :zap: :zap:  
  
まだまだ元気(?)な[Delphi](https://www.embarcadero.com/products/delphi)です。  
  
<dt>よいところ  
<dd>  
* Visual BasicのようなRAD環境がある。  
* iOS/Androidとビジネスロジックコードを共有できる。  
</dd>  
<dt>いけてないところ  
<dd>  
* ベンダーロックインが心配。  
* 新たに言語を覚えなくてはいけない。  
</dd>  
  
  
# React Native :zap: :zap:   
  
[React Native Windows](https://microsoft.github.io/react-native-windows/)がMicrosoftから公式にスポンサードされて再実装中です。  
~~ドキュメント→[Getting Started Guide](https://github.com/microsoft/react-native-windows/blob/master/vnext/docs/GettingStarted.md)~~  
アナウンス→[ブログ](https://microsoft.github.io/react-native-windows/blog/)  
  
React Native本体のバージョンに追随するように着々と進んでいます。2020年2月現在 Version 0.61 :zap: :zap: :zap: :zap:   
  
<dt>よいところ  
<dd>  
* iOS/Androidと同じ知識で対応できる。  
* テストが書きやすい（はず）。  
</dd>  
<dt>いけてないところ  
<dd>  
* まだ(2019年7月現在)リリースされていない。  
</dd>  
  
  
  
  
  
# .NET Core 3 + WinForms/WPF [^2][^4]  
  
[^2]: .NET Core 3はWindowsデスクトップアプリをサポートする https://www.infoq.com/jp/news/2018/05/net-core3-announced  
[^4]: Windows Forms/WPFはOSSになりました https://github.com/dotnet/winforms https://github.com/dotnet/wpf :zap:  
  
WinForms/WPF/UWPと並行する概念ではないけれど、とりあえず書いておきます。.Net Core上で開発すれば、OSと.Net Frameworkの組み合わせから自由になれる（はず）。  
~~2019年にリリースされる予定です。~~  
  
2020年2月現在 3.1.2 がリリースされています。 :zap: :zap: :zap: :zap:   
  
<dt>よいところ  
<dd>  
* OS/.Net Frameworkのバージョンに縛られない。  
* .Net Frameworkよりテストが書きやすい。 :zap:  
* WinForms/WPFがOSSになり、ソースを追いかけて納得できない挙動を把握できる。 :zap: :zap: :zap: :zap:   
* VB.NETはサポートされていない！（今のところ） :zap: :zap: :zap: :zap:   
</dd>  
<dt>いけてないところ  
<dd>  
* ~~まだ(2018年9月現在)リリースされていない。~~ ~~2019年9月にリリース予定です。[^3] :zap:~~  
* 期待するほど .Net Frameworkと変わらない。 :zap: :zap: :zap: :zap:   
* 色々憶測が飛び交う中、WinForms/WPFがOSSになりました。もしかするとMicrosoftはやめたがっているのかもしれない（憶測）。 :zap: :zap: :zap: :zap:   
* まだ（2020年2月現在）ビジュアルデザイナーが動かない。 :zap: :zap: :zap: :zap:   
</dd>  
  
[^3]: Introducing .NET 5 https://devblogs.microsoft.com/dotnet/introducing-net-5/ :zap:  
  
  
  
# WinForms + XAML Islands / WPF + XAML Islands  
  
WinFormsあるいはWPFからUWPのUIコントロールを使えるようにするためのものです。ガワネティブならぬガワWinFormsやガワWPFができます。  
https://docs.microsoft.com/ja-jp/windows/uwp/xaml-platform/xaml-host-controls  
  
<dt>よいところ  
<dd>  
* Windows 7でUWPを動かせる(?)  
</dd>  
<dt>いけてないところ  
<dd>  
* まだ出たばかり。  
</dd>  
  
  
  
# UWPの周辺テクノロジ  
  
WinForms/WPFに関する情報は多いので、未だ発展し続けるUWPについてまとめてみます。  
  
## Windows Template Studio  
  
UWPアプリの雛形を作ってくれるVisual Studio 2017の追加機能です。  
https://marketplace.visualstudio.com/items?itemName=WASTeamAccount.WindowsTemplateStudio  
  
## Windows UI Library  
  
JavaScriptで言うところのshimみたいなもの＋UIコントロール集です。  
https://github.com/Microsoft/microsoft-ui-xaml  
  
## Windows Community Toolkit  
  
Windows SDKに不足しているUIコントロール集です。  
積極的に開発されています。  
https://github.com/Microsoft/WindowsCommunityToolkit  
  
## サイドローディング  
  
UWPアプリをMicrosoftストアを介さずにインストールする方法です。  
Windows 10の設定を変更する必要があります。  
  
## プライベートストア  
  
私的出品が可能なMicrosoftストアです。  
ビジネス向けと教育機関向けがあります。  
  
- プライベート ストアを使ってアプリを配布する https://docs.microsoft.com/ja-jp/microsoft-store/distribute-apps-from-your-private-store  
- Working with 基幹業務アプリ https://docs.microsoft.com/ja-jp/microsoft-store/working-with-line-of-business-apps  
  
# 結論  
  
UWPで要件を満たせるのであればUWP。  
Webに慣れた開発者が多ければElectronかReact Native。  
iOS/Android向けアプリも一緒に開発するならXamarinかReact Native。  
そうでなければWPF一択です。  
  
**2019年7月 追記** :zap: :zap: :zap:  
  
WPFを選択した場合、.Net Framework 4.7/4.8にするか、.Net Core 3にするのかも選ばなければいけません。  
  
将来的なサポートやテストしやすさを考えれば、.Net Core 3から.NET 5へと続く道へ。  
リリースが間近か、現在ある資産をどうしても手放せないのであれば、.Net Framework 4.7/4.8でしょうか。  
  
  
# 参考リンク :zap: :zap: :zap:  
  
- [Visual Studio 2019で始める「WPF on .NET Core 3.0」開発 というタイトルで登壇してきました](http://www.nuits.jp/entry/wpf-on-dot-net-core-3_0)  
