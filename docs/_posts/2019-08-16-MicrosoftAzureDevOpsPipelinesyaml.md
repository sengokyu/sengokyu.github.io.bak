---
'created_at: ': '2019-08-16T11:32:44+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Azure
      versions: []
    - name: AzureDevOps
      versions: []
    - name: AzurePipelines
      versions: []
  state: 0
title: "[Microsoft] Azure DevOps Pipelines\u3067\u3001\u8907\u6570\u306Eyaml\u30D5\
  \u30A1\u30A4\u30EB\u3092\u4F7F\u3044\u5206\u3051\u308B\u65B9\u6CD5"
'updated_at: ': '2019-08-16T11:32:44+09:00'

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
