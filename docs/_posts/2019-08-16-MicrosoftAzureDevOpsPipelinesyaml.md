---
date: '2019-08-16T11:32:44+09:00'
layout: post
published: true
qiita_article_id: d0adc19e38aee878fe8d
tags:
- Azure
- AzureDevOps
- AzurePipelines
title: '[Microsoft] Azure DevOps Pipelinesで、複数のyamlファイルを使い分ける方法'
updated: '2019-08-16T11:32:44+09:00'

---
# これはなに？  
  
Azure DevOps Pipelinesを作成すると、既定では`azure-pipelines.yml`というファイルが自動的に出来上がります。  
このファイルを、例えばビルド先ターゲット（開発用と本番用など）に応じて用意して使い分けるお話です。  
  
# 手順  
  
## ステップ 1  
  
パイプラインの画面から、[New build pipeline]をクリックします。  
  
<img width="226" alt="image.png" src="/assets/images/8906d331-361c-b196-9c80-58c4af2463ed.png">  
  
## ステップ 2  
  
自分が使っているレポジトリを選びます。  
  
<img width="665" alt="image.png" src="/assets/images/c7a4923e-7de3-3e72-1242-d6c614facdc1.png">  
  
<img width="673" alt="image.png" src="/assets/images/144891da-226c-9f75-4d5d-f52a829576fd.png">  
  
  
## ステップ 3  
  
ここが要です。  
というか、やることはここだけです。  
  
__Existing Azure Pipelines YAML file__ を選びます。  
  
<img width="822" alt="image.png" src="/assets/images/16a04440-37f3-f906-ef39-5026b4e8a465.png">  
  
  
## ステップ 4  
  
YAMLファイルを選びます。  
ここでは`azure-pipelines-staging.yml`というファイルを選んでいます。  
  
<img width="475" alt="image.png" src="/assets/images/33795cd8-38d1-2a12-a1d4-4a610a3d956f.png">  
  
  
## ステップ 5  
  
以上です。  
お疲れ様でした。  
  
  
# ところで  
  
Azure DevOpsには、Build PipelineとRelase Pipelineというものがあります。  
Release Pipelineはディスコンなんでしょうか。  
↓↓↓ドキュメントには **(Classic)** なんて書かれていますし。  
https://docs.microsoft.com/en-us/azure/devops/pipelines/release/?view=azure-devops  
