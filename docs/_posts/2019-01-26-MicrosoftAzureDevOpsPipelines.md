---
date: '2019-01-26T19:07:47+09:00'
layout: post
published: true
qiita_article_id: e832c3f1c1c8fbb98415
tags:
- Azure
- CI
- AzureDevOps
- AzurePipelines
title: '[Microsoft] Azure DevOps Pipelines 設定ファイルの書き方'
updated: '2019-10-09T10:53:36+09:00'

---
Azure Pipelinesの動作を設定するYAMLファイルの書き方を完全に理解したので、忘れないうちにまとめておきます。  
  
リファレンスマニュアルはこちら → https://docs.microsoft.com/ja-jp/azure/devops/pipelines/yaml-schema?view=azdevops&tabs=schema  
  
  
# 全体の構成  
  
まず全体の構成です。  
ジョブ（後述）を書かない場合と、書く場合に分かれます。  
  
コピペしてテンプレにしてください。  
  
## ジョブを書かない場合  
  
**azure-pipelines.yml**  
```yaml:azure-pipelines.yml
pool:
  vmImage: ubuntu-16.04

steps:
- 手順
- ……
```  
  
## ジョブを書く場合  
  
**azure-pipelines.yml**  
```yaml:azure-pipelines.yml
jobs:
- job: myjobname
  pool:
    vmImage: ubuntu-16.04
  steps:
  - 手順
  - ……
```  
  
  
  
  
# 手順の書き方  
  
Pipelinesで実行する手順は、`steps`以下に書いていきます。  
  
**azure-pipelines.yml**  
```yaml:azure-pipelines.yml
steps:
- 手順1
- 手順2
- 手順3
- ……
```  
  
# 『手順』として書けるもの  
  
## `task`  
  
[あらかじめ用意されている処理](https://docs.microsoft.com/ja-jp/azure/devops/pipelines/tasks/index?view=azdevops)を実行します。  
  
タスクの一覧はこちら → https://docs.microsoft.com/ja-jp/azure/devops/pipelines/tasks/index?view=azdevops  
  
例えば`npm install`を実行する場合はこうなります。  
  
****  
```yaml:
steps:
- task: Npm@1
```  
  
  
  
## `template`  
  
外部ファイルを読み込みます。パラメータを渡すこともできます。  
  
例えば、`ci/build.steps.yml`という外部ファイルを読み込み、`target`というパラメータを渡す場合はこうなります。  
  
****  
```yaml:
steps:
- template: ci/build.steps.yml
  parameters:
    target: myproject
```  
  
読み込まれる側のファイルはこうなります。  
  
**ci/build.steps.yml**  
```yaml:ci/build.steps.yml
parameters:
  target: デフォルト値

steps:
- script: npm run build ${{"{{"}} parameters.target }}
```  
  
  
## タスクのショートカット  
  
いくつかのタスクにはショートカットというか、エイリアスが用意されています。  
  
- [script](https://docs.microsoft.com/ja-jp/azure/devops/pipelines/tasks/utility/command-line?view=azdevops&tabs=yaml) CmdLine@2のエイリアス  
- [bash](https://docs.microsoft.com/ja-jp/azure/devops/pipelines/tasks/utility/shell-script?view=azdevops) ShellScript@2のエイリアス  
- [pwsh](https://docs.microsoft.com/ja-jp/azure/devops/pipelines/tasks/utility/powershell?view=azdevops) PowerShell@2のエイリアス  
- [PowerShell](https://docs.microsoft.com/ja-jp/azure/devops/pipelines/tasks/utility/powershell?view=azdevops) PowerShell@2のエイリアス  
  
これらは、`task: タスク名`と書かずに、直接書けます。  
  
****  
```yaml:
steps:
- script: …
- bash: …
- pwsh: …
- PowerShell: …
```  
  
よく使うのは`script`です。コマンドラインスクリプトを書けます。  
  
vmImageとしてWindowsを選んだ場合はcmd.exe、Linux/macOSであればbashです。  
  
****  
```yaml:
steps:
- script: echo %foo% # Windowsの場合
- script: echo $foo # Linux/macOSの場合
```  
  
# ジョブの書き方  
  
『手順』をまとめると『ジョブ』になります。  
『ジョブ』は、並列に実行したり、前段階の『ジョブ』の状態に応じて実行可否を決めたりできます。  
  
  
****  
```yaml:
jobs:
- ジョブ1
- ジョブ2
- ジョブ3
- ……
```  
  
# 『ジョブ』として書けるもの  
  
## `job`  
  
まとめた手順`steps`を書けます。  
  
```yaml
jobs:
- job: ジョブ名
  steps:
  - 手順1
  - 手順2
  - ……
```  
  
## `template`  
  
外部ファイルを読み込みます。パラメータを渡すこともできます。  
  
例えば、`ci/env.jobs.yml`という外部ファイルを読み込み、`machine`というパラメータを渡す場合はこうなります。  
  
****  
```yaml:
jobs:
- template: ci/env.jobs.yml
  parameters:
    machine: ubuntu-16.04
```  
  
読み込まれる側のファイルはこうなります。  
  
**ci/env.jobs.yml**  
```yaml:ci/env.jobs.yml
parameters:
  machine: デフォルト値

jobs:
- job: EachEnvironment
  pool:
    vmImage: ${{"{{"}} parameters.machine }}
```  
  
