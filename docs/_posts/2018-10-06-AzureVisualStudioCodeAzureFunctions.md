---
date: '2018-10-06T09:35:47+09:00'
layout: post
published: true
qiita_article_id: 1cccb8b868d8d0ab3cf4
tags:
- Node.js
- macos
- VSCode
- AzureFunctions
title: '[Azure] Visual Studio CodeでAzure Functionsのデバッグができないとき'
updated: '2018-10-06T09:36:25+09:00'

---
下記のURLにある手順に従っても、うまくいかないんです。  
という時の対処方法です。  
  
Deploy to Azure using Azure Functions  
https://code.visualstudio.com/tutorials/functions-extension/getting-started  
  
# 現象  
  
Visual Studio Codeでデバッグ実行すると、下記メッセージが表示されます。  
  
![image.png](/assets/images/83d3f37c-d424-24a7-71c5-c4c3f24cd894.png)  
  
Cannot connect to runtime process, timeout after 10000ms (reason: Cannot connect to the target: connect ECONNREFUSED 127.0.0.1:5858).  
  
# 環境  
  
* macOS High Sierra  
* node 10.11.0  
* Visual Studio Code 1.27.2  
* Azure Functions拡張 0.11.0  
* Azure functions tools 2.0.3  
* .Net Core SDK 2.1.402  
  
  
# 原因  
  
node実行時に、`--inspect=5858`オプションが渡っていないから。  
  
# 解決策  
  
`launch.json`を開くと、`preLaunchTask`として`runFunctionsHost`が設定されています。  
  
`tasks.json`を開きます。  
  
**tasks.json（抜粋）**  
```json:tasks.json（抜粋）
{
  "version": "2.0.0",
  "tasks": [{
      "identifier": "runFunctionsHost",
      "type": "shell",
      "command": "func host start",
      "options": {
        "env": {
          "languageWorkers:node:arguments": "--inspect=5858",
        }
      },
  }]
}
```  
  
`--inspect=5858`と書いてありますが、理解してもらえてないご様子です。  
  
nodeの環境変数を直接指定することにします。  
  
****  
```json:
          "NODE_OPTIONS": "--inspect=5858"
```  
  
このように書き換えます。  
  
# そのうち・・・・・・  
  
Azure functions toolsが修正されて、この記事不要になるかな？  
  
