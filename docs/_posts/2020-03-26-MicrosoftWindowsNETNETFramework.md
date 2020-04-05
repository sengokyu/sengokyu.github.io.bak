---
date: '2020-03-26T22:59:50+09:00'
layout: post
published: true
qiita_article_id: a93d0be65483479f1253
tags:
- Windows
- .NET
- .NETFramework
- .NETCore
title: '[Microsoft] Windowsデスクトップ向け業務アプリ開発は.NETと.NET Frameworkどっち？'
updated: '2020-03-31T09:26:32+09:00'

---
[Windowsデスクトップ向け業務アプリ開発には何を採用すべきか？](2018-09-22-MicrosoftWindows.md) を書いていた時点ではわからなかったことがだいぶはっきりしましたので、あらためてまとめてみます。  
  
以下、Windows 10のバージョン記載は慣例に習って西暦下2桁 + 03 または 09 としています（2004のようにイレギュラーもあるかも）。  
  
# .NET(.NET Core)の現況  
  
## いつからいつまで使える？  
  
[.NET Core Support Policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)から引用します。  
  
| 名前 | バージョン | LTS | リリース日 | サポート終了日 |  
|:-:|:-:|:-:|:-:|:-:|  
| .NET Core   | 3.1  | LTS |2019/12/3  | 2022/12/3 |  
| .NET  | 5.0 | | 2020/11  | 2022/2 |  
| .NET  | 6.0 | LTS | 2021/11  | 2024/11 |  
| .NET  | 7.0 | | 2022/11 | 2024/2 |  
| .NET  | 8.0  | LTS | 2023/11 | 2026/11 |   
  
LTS(Long Term Support)版はリリースから3年間サポートされます。  
  
## Visual Studioにおけるサポートは？  
  
[Visual Studio 2019 16.3から.NET Core 3.0がサポート](https://devblogs.microsoft.com/visualstudio/dot-net-core-support-in-visual-studio-2019-version-16-3/)されました。  
  
  
### WPFの場合  
  
WPF XAMLデザイナーは、Visual Studio 2019 16.3から使用できます。[プレビューではありません](https://devblogs.microsoft.com/dotnet/introducing-net-core-windows-forms-designer-preview-1/)。  
  
### Windows Formsの場合  
  
[Updates on .NET Core Windows Forms designer](https://devblogs.microsoft.com/dotnet/updates-on-net-core-windows-forms-designer/)によりますと、ビジュアルデザイナーはVisual Studio 2019 16.6においてプレビュー段階です。まだいくつかのコントロールしかサポートしておらず、またサードパーティのコントロールもサポートしていません。  
  
全機能のサポートは.NET 5.0の頃になるようです。  
  
.NET Frameworkで作って、コピーすればOKという話もありました。  
  
  
  
# .NET Frameworkでいつまで頑張れるか  
  
.NET Frameworkの系譜は終わり、.NET Coreベースの.NET 5になりますが、.NET FrameworkはバンドルされているOSに準じてサポートされ続けます。  
  
Windows 10のサポート期間とバンドルされている.NET Frameworkのバージョンを[Windows ライフサイクルのファクト シート](https://support.microsoft.com/ja-jp/help/13853/windows-lifecycle-fact-sheet)と[.NET Framework versions and dependencies](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/versions-and-dependencies)から引用します。  
  
2004以降は未リリースです。  
  
| Windows 10 バージョン | Pro.エディションサポート終了日 | Enterpriseエディションサポート終了日 | バンドル.NET Framework バージョン  
|:-:|:-:|:-:|:-:|  
| 2203   | 2023/12 (?) | 2023/12 (?) | 4.8 (?) |  
| 2109   | 2023/5 (?) | 2024/5 (?) | 4.8 (?) |  
| 2103   | 2022/12 (?) | 2022/12 (?) | 4.8 (?) |  
| 2009   | 2022/5 (?) | 2023/5 (?) | 4.8 (?) |  
| 2004  | 2021/12 (?) | 2021/12 (?) | 4.8 |  
| 1909  | 2021/5/11  | 2022/5/10  | 4.8  |  
| 1903  | 2020/12/8  | 2020/12/8 | 4.8  |  
| 1809  | 2020/5/12  | 2021/5/11  | 4.7.2 |  
| 1803  | （終了）  | 2020/11/10  | 4.7.2 / 3.5 SP1 |  
  
[3.5 SP1は、バージョン1809以降からOSコンポーネントとはみなされなくなりました](https://support.microsoft.com/ja-jp/help/17455/lifecycle-faq-net-framework)。  
  
4.8も、いつかWindows 10から切り離されるのか？  
もし切り離されるとすればいつ頃か？  
というのが気になるところです。:innocent:  
  
[.NET Framework Support Policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-framework) には、下記のように書いてあるので、切り離されることはなさそうな気もします。  
> .NET Framework 4.8 is the latest version of .NET Framework and will continue to be distributed with future releases of Windows.   
  
個人的には、.NET 6.0 がGAになってから5〜8年後の2026年か2029年あたりで切り離されるんじゃないかと妄想しています。  
  
## 3.5 SP1使えばよくね？  
  
3.5 SP1のサポート期間はとても長いので、これで作っておけば安心な気もします。しかしながら[メインストリームサポートは2023/10/10まで](https://support.microsoft.com/en-us/lifecycle/search?alpha=.net%20framework%203.5)なので、最後にインストールできるWindows 10はバージョン2303になると思われます。したがって、3.5 SP1は実質2024年12月までではないでしょうか？  
  
| Windows 10バージョン | リリース時期 | サポート終了時期 |   
|:-:|:-:|:-:|  
| 2309  | 2023/11 (?) | 2025/5 (?) |  
| 2303  | 2023/5 (?)  | 2024/12 (?)  |  
  
  
  
# .NETと.NET Frameworkの比較  
  
.NET Frameworkにあって.NETにない機能（WCFとか）は置いておいて、開発・運用にフォーカスして比較表めいたものを作ってみます。  
  
| 比較項目 | .NET 3.1 | .NET 5.0〜 |  .NET Framework 4.8 | 備考 |  
|:-:|:-:|:-:|:-:|:--|  
| Visual StudioにおけるWPFのサポート  | ◯ | ◯ | ◯ |   |  
| Visual StudioにおけるWinFormsのサポート  | ×寄りの△  | ◯ (?) | ◯ | WinFormsはVSと一体のため、なかなかに難しいらしいです。[^2]  |  
| C# バージョン  | 8.0〜 | ← | 7.3  |   |  
| Visual Basicのサポート | × (?)[^3] | ◯ | ◯ | .NETでもVB使えますが、もうやめたほうがよいです。[^1] |  
| ビルドツール | dotnetコマンド / msbuild| ← | msbuild | |  
| テストの書きやすさ | ぼちぼち◯ | ← | △ | .NETはSealedが外されて、モックを作りやすくなりました。 |   
| ソースコード  | オープン | ← | プロプライエタリ  | ソースを見れば万事解決（つらみ  |  
| インストーラ | MSIX | ← | ClickOnce | |  
| ランタイム | インストーラとセット | ← | 別途インストール |  
| 乗り換え頻度  | 24〜36ヶ月毎に、次のLTS版.NETへ乗り換える  | ← | 18 or 30ヶ月毎に、新しいWindows 10へ乗り換える  | |  
  
  
[^2]: https://devblogs.microsoft.com/dotnet/introducing-net-core-windows-forms-designer-preview-1/#comment-3396  
[^3]: https://devblogs.microsoft.com/dotnet/introducing-net-core-windows-forms-designer-preview-1/#comment-3345  
[^1]: Visual Basicは、言語としては進化させないことが発表されました。言語に対する変更が必要な.NETの機能が登場したとしても、Visual Basicではサポートされません。  
  
  
# 結論  
  
前提条件によって変わってくると思われます。  
  
* Visual Basicで作成したい  
    * :point_right: .NET Framework 4.8 が無難です。  
* Windows Formsで作成したい  
    * :point_right: .NET Framework 4.8 を選ぶしかないです。  
    * :point_right: ガワだけ.NET Framework 4.8で作り、ビジネスロジックは.NET StandardなDLLというのもありかもしれません。  
* 上記以外の場合  
    * [UWPコントロール](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/controls-by-function)を使いたい  
        * :point_right: .NET  
    * 運用を予定している環境に入っている.NET Frameworkが古い（そんなことありえるかな？）  
        * :point_right: .NET  
    * 最新のC#を使いたい  
        * :point_right: .NET  
    * 使いたいサードパーティライブラリが.NETに対応していない  
        * :point_right: .NET Framework 4.8  
        * 対応をうたっていなくても、内容によっては.NETから使える場合があります。  
            * :point_right: .NET  
    * NuGetの使用を禁止されている  
        * :point_right: .NET Framework 4.8  
    * Microsoft製品は3世代目からが本番だよね？  
        * :point_right: .NET 8.0待ち  
  
主観混じりの結論です。  
  
# リンク  
  
* WPFのgitレポジトリ https://github.com/dotnet/wpf  
* WinFormsのgitレポジトリ https://github.com/dotnet/winforms  
  
  
# Windows 10と.NET/.NET Frameworkサポート期間一覧  
  
あらためてサポート期間をひとつの図にまとめてみました。  
アプリケーションのアップデートや動作検証費用をいつごろ予算化すればよいか、あらかじめ先々のことを話しておくと後々スムーズです。  
  
![image.png](/assets/images/9d3782f1-7c8d-4a64-d912-37ad07a55578.png)  
