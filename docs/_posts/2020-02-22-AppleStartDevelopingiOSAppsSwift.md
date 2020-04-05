---
date: '2020-02-22T18:52:47+09:00'
layout: post
published: true
qiita_article_id: ba44da217e48d8b58fc5
tags:
- iOS
- Swift
title: '[Apple] 写経 Start Developing iOS Apps (Swift)'
updated: '2020-02-23T12:34:07+09:00'

---
Apple公式ドキュメント [Start Developing iOS Apps (Swift)](https://developer.apple.com/library/archive/referencelibrary/GettingStarted/DevelopiOSAppsSwift/BuildABasicUI.html#//apple_ref/doc/uid/TP40015214-CH5-SW1) の一部を写経します。  
  
  
# appデリゲートソースファイル  
  
`AppDelegate.swift`には、2つの主要な関数があります。  
  
* `AppDelegate`クラスを定義します。**appデリゲート**は、コンテンツが描画されるウィンドウを作成し、アプリケーション内の状態遷移を受け付けるための場所を提供します。  
* アプリケーションの**エントリポイント**と、入力イベントをアプリケーションへ渡す**実行ループ**を作成します。これはファイル先頭にある`UIApplicationMain`属性(`@UIApplicationMain`)によって行われます。  
  `UIApplicationMain`属性は、デリゲートクラスとして`AppDelegate`クラスを`UIApplicationMain`へ渡すことと同義です。戻り値の中で、システムは**アプリケーションオブジェクト**を作成します。アプリケーションオブジェクトはアプリケーションのライフサイクルを管理する責任を負います。システムは`AppDelegate`クラスのインスタンスもまた作成しアプリケーションオブジェクトへ割り当てます。最終的にシステムはアプリケーションを起動します。  
  
> わかったこと:  
> アプリケーションの実行時に、アプリケーションオブジェクトとappデリゲートが作成される。  
> `UIApplicationMain`属性をつけることで、アプリケーションのエントリポイントとappデリゲートであることをシステムに認識させる。  
  
`AppDelegate`クラスはプロパティを一つ含みます。  
  
```swift
var window: UIWindow?
```  
  
このプロパティはアプリケーションのウィンドウへの参照を格納しています。ウィンドウはアプリケーションのビュー階層の根元です。コンテンツはこの中に描画されます。このwindowプロパティはオプショナルであることに注意が必要です。ある時点では値が入っていません(nilです)。  
  
`AppDelegate`クラスは以下のメソッドのスタブ実装も含んでいます。  
  
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
func applicationWillResignActive(_ application: UIApplication)
func applicationDidEnterBackground(_ application: UIApplication)
func applicationWillEnterForeground(_ application: UIApplication)
func applicationDidBecomeActive(_ application: UIApplication)
func applicationWillTerminate(_ application: UIApplication)
```  
  
これらのメソッドは、アプリケーションオブジェクトにappデリゲートとやりとりさせるためのものです。  
  
いずれのデリゲートメソッドも既定の振る舞いを持っています。メソッドが空か存在しないと、既定の振る舞いをします。  
  
> わかったこと:  
> `AppDelegate`プロトコルを実装することでアプリケーションオブジェクトとやりとりする。  
> `AppDelegate`プロトコルの中身を実装しないと、既定の振る舞いをする。  
