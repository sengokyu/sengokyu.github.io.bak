---
date: '2019-10-17T23:25:34+09:00'
layout: post
published: true
qiita_article_id: 8bbb648783814e8956ba
tags:
- Azure
- CI
- pipelines
- AzureDevOps
title: '[Microsoft] Azure Pipelinesビルド環境を自前で用意（セルフホスト）する'
updated: '2020-02-08T14:03:46+09:00'

---
# はじめに  
  
[Azure Pipelines](https://azure.microsoft.com/ja-jp/services/devops/pipelines/) は、自前で環境を用意しなくても、Microsoftが用意している環境を使ってビルド＆デプロイできます。  
ただ、プライベートなプロジェクトの場合は利用時間に上限（1800分/月）があり、長いテストを走らせたりしていると月半ばで上限に達してしまうことがあります。  
  
自前で環境を用意（セルフホスト）すれば、この上限がなくなります。  
  
# 手順  
  
[公式ドキュメント](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/v2-linux?view=azure-devops) にはわかりづらいところがあるので、あらためてまとめてみます。  
  
## 事前準備  
  
作業を始める前に、準備しておくものがあります。  
  
* PAT(Personal Access Token)  →→→  DevOpsの画面から作成して取得します。  
* エージェント →→→ [github](https://github.com/microsoft/azure-pipelines-agent)からダウンロードします。  
* エージェントインストール先の仮想マシンか物理マシン  
  
OSは、ビルドしたいものに合わせます。  
Linuxであれば、Ubuntu 18.04 がオススメです。19.04 は未サポートです。  
  
  
## エージェントのインストール  
  
事前準備ができたら、対象マシンにエージェントをインストールします。  
  
全体の概略は以下の通りです。  
  
1. エージェントを動かすユーザを作成  
2. エージェントをインストール  
3. エージェントを設定  
3. エージェントをサービスとして動かす  
  
ひとつずつ見ていきます。  
  
### エージェントを動かすユーザを作成  
  
以下の要件でユーザを作成しました。  
  
| 設定項目 | 値 |  
|:--|:--|  
| GID  | 660  |  
| グループ名  | vstsagent  |  
| UID | 660  |  
| ユーザー名  | vstsagent  |  
| シェル | /usr/sbin/nologin |  
| ホームディレクトリ | /opt/vstsagent |  
  
```console
sudo useradd --uid 660 --system --shell /usr/sbin/nologin --home-dir /opt/vstsagent --create-home vstsagent
```  
  
### エージェントをインストール  
  
ファイルをホームディレクトリに展開します。  
**ホームディレクトリ必須です。**  
  
```console
sudo -u vstsagent tar zxf vsts-agent-linux-x64-2.159.0.tar.gz -C /opt/vstsagent
```  
  
### エージェントを設定  
  
展開したファイル中にある`config.sh`を実行します。  
  
```console
cd /opt/vstsagent
sudo -u vstsagent ./config.sh
```  
  
いくつか質問が表示されるので答えていきます。  
  
| 質問項目 | 値 |  
|:--|:--|  
| Server URL  | https://dev.azure.com/****** <== 実際はDevOps組織のURL  |  
| Authentication Type  | 既定 (PAT)  |  
| Personal Access Token  | 事前準備で取得したもの  |  
| Agent Pool  | DevOps組織の中にあるAgent Pool名(defaultでOK)  |  
| Agent name  | 表示されるエージェント名（既定でOK）  |  
| Work folder | /mnt/vstswork[^1] |  
  
[^1]: 自分はAzure VMを用意したので、アクセスが速いリソースディスク（/mntにマウントされている）を使うようにしました。リソースディスクは再起動で消えるので、ディレクトリは別途起動時に作成する必要があります。  
  
### エージェントをサービスとして動かす  
  
ユーザー vstsagent で動かすようにします。  
  
```console
sudo ./svc.sh install vstsagent
```  
  
  
# 開発ツールのインストール  
  
使用する開発ツールもインストールします。  
  
* build-essential (コンパイラ等)  
* Node  
* DotNet SDK  
* Azure CLI  
* ...等  
  
  
# 落ち穂拾い  
  
## Azure VMにおける消えるリソースディスクへの対応  
  
シャットダウン時に消えてしまうリソースディスク内に作業ディレクトリを置くことにしたので、起動時に作成するようにしました。  
  
systemd用のファイルを作成します。  
  
```/lib/systemd/system/create-vsts-work-dir.service
[Unit]
Description=Create a work folder for the VSTS agent
After=cloud-config.service
ConditionVirtualization=microsoft
ConditionPathIsMountPoint=/mnt
ConditionPathExists=/dev/disk/azure/resource-part1

[Service]
Type=oneshot
ExecStart=/usr/local/sbin/create-vsts-work-dir
RemainAfterExit=yes
StandardOutput=journal+console

[Install]
WantedBy=multi-user.target
```  
  
起動時に動くようにします。  
  
```console
systemctl enable create-vsts-work-dir
```  
  
実行するシェルスクリプトも作成します。  
  
ファイル全体は https://github.com/sengokyu/create-vsts-work-dir へ置きました。  
  
  
  
# トラブルシューティング  
  
## node.jsがインストールされていないと言われる  
  
問題: ビルドの途中で、node.jsがインストールされていないというエラーが出ます。  
解決策?:thinking:: エージェントをホームディレクトリに展開したら大丈夫でした。   
  
## エラー "No agent found in pool Default which satisfies the specified demands: npm Agent.Version " が出力される  
  
問題: Pipelineの中でnpmを使えません。  
解決策?:thinking:: エージェントのバージョンを2.158から2.159にしたら動きました。  
  
## エラー "null: zip" と言われる  
  
問題: パッケージやZIPタスクでエラーが起きます。  
解決策: zipをインストールします。`apt-get install zip`  
  
# リンク  
  
* https://github.com/microsoft/azure-pipelines-agent エージェントはここから最新版をダウンロードした方が良いです。  
* https://github.com/microsoft/azure-pipelines-image-generation MicrosoftがホストしているVMのイメージを作成するpackerがあります。  
    * https://github.com/microsoft/azure-pipelines-image-generation/blob/master/images/linux/ubuntu1804.json Ubuntu 18.04のpackerファイルです。  
