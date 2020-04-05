---
date: '2018-02-10T11:12:43+09:00'
layout: post
published: true
qiita_article_id: ec9097c17820241915e4
tags:
- Windows
- Microsoft
- WMI
title: '[Windows] Windows Management Instrumentation (WMI)にある__EventConsumerクラス達'
updated: '2018-02-10T11:21:15+09:00'

---
# これはなに？  
  
Windowsイベント(イベントビューアで見られるあれ)を受信できるヤツら(クラス)の紹介です。  
  
Windows Management Instrumentation使ってますか？  
Windowsのマジ痒いところに手が届くできるヤツです。  
  
、、、けど、今どきはあんまし使わないんですかね？Qiitaでも情報少ないですし。  
  
  
  
## Windows Management Instrumentation (WMI)に親しむ  
  
WMIに親しむにはwbemtestアプリを使います。多分どなたのWindowsにも入っているはずです。  
  
起動するとこんな感じ  
  
![image.png](/assets/images/7f971074-f8b9-dfb9-af2f-51b4c526561a.png)  
  
おもむろに[接続...]ボタンを押して`root\subscription`名前空間に接続します。  
  
![image.png](/assets/images/5bee01d7-b9d4-6fde-5dd2-835e4841b412.png)  
  
他にどんな名前空間があるかって？  
すみません。知らないです。  
  
[クラスの列挙...]ボタンを押して、スーパークラス名に`__EventConsumer`と入力します。  
  
![image.png](/assets/images/2cd1d121-e3ec-ac27-05db-cf4bbdd8d5b9.png)  
  
ずらずらとクラスが表示されます。  
このクラス達を紹介したいと思います。  
  
  
# ActiveScriptEventConsumerクラス  
  
ActiveScript(VBScriptやJScript等)を実行してくれます。  
ActiveScriptでできることなら、なんでもできるはず。  
  
MSDN → https://msdn.microsoft.com/ja-jp/library/aa384749(v=vs.85).aspx  
  
## 主なプロパティ  
  
| プロパティ | 説明 | 例 |  
|:--|:--|:--|  
| Name | 一意な名前です。自由に設定できます。  | "SendMail"  |  
| ScriptEngine  | 使用する言語です。  | "VBScript"  |  
| ScriptText  | プログラムです。指定した言語で記述します。   | (略）  |  
  
# CommandLineEventConsumerクラス  
  
指定したコマンドを実行してくれます。  
  
MSDN → https://msdn.microsoft.com/ja-jp/library/aa389231(v=vs.85).aspx  
  
## 主なプロパティ  
  
| プロパティ | 説明 | 例 |  
|:--|:--|:--|  
| Name | 一意な名前です。自由に設定できます。  | "SaveDB"  |  
| CommandLineTemplate  | 実行するコマンドラインです。引数も書けます。   | "powershell.exe -ExecutionPolicy RemoteSigned InsertSql.ps1" |  
  
  
# LogFileEventConsumerクラス  
  
Windowsイベントをファイルに記録してくれます。  
  
MSDN → https://msdn.microsoft.com/en-us/library/aa392277(v=vs.85).aspx  
  
  
## 主なプロパティ  
  
| プロパティ | 説明 | 例 |  
|:--|:--|:--|  
| Name | 一意な名前です。自由に設定できます。  | "WriteLog"  |  
| Text  | 記録する文字列です。  | "%TargetInstance.Message%" |  
| Filename  | 記録先のファイル名です。   | "C:\var\log\error.log" |  
  
  
# NTEventLogEventConsumerクラス  
  
Windowsイベントをさらに別のイベントとして記録してくれます。  
あんまり使う機会ないかも。  
  
MSDN https://msdn.microsoft.com/ja-jp/library/aa392715(v=vs.85).aspx  
  
# SMTPEventConsumerクラス  
  
Windowsイベントをメール送信してくれるのですが、SMTP認証に対応していなかったりで使えないので割愛です。  
  
# リンク  
  
* [Monitoring and Responding to Events with Standard Consumers](https://msdn.microsoft.com/en-us/library/aa392395(v=vs.85).aspx)  
  
