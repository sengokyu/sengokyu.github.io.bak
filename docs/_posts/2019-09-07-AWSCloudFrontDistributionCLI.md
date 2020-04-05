---
date: '2019-09-07T17:34:55+09:00'
layout: post
published: true
qiita_article_id: 4f93a2b2209ab72578bf
tags:
- AWS
- CloudFront
title: '[AWS] CloudFront Distributionをコマンドライン(CLI)から作成する'
updated: '2019-10-06T15:28:04+09:00'

---
CLIいいですよね。  
GUIと違ってコピペですみます。  
これはAWS CloudFrontのDistributionをコマンドラインから作成する物語です。  
  
ちなみに、DistributionというのはCloudFrontの設定単位です。  
  
# あらかじめ必要なもの｜こと  
  
* [AWS CLI](https://aws.amazon.com/jp/cli/)  
* AWSのアカウント  
* AWS CLIにログインしておく  
  
  
# 雛形JSONを生成  
  
awsコマンドに渡す雛形を生成できます。便利。  
  
```shell-session
aws cloudfront create-distribution --generate-cli-skeleton > aws-cloudfront-distribution.json
```  
  
`aws-cloudfront-distribution.json`というファイルができました。  
  
このJSONファイルを使ってDistributionを作成します。  
  
# Distributionの中身  
  
Distributionには、大まかにいって2つの設定があります。配信元とキャッシュの振る舞いです。  
  
## 配信元  
  
配信元のことをOriginといいます。  
OriginはS3やAPI Gateway等のhttp/httpsでアクセスできるものです。  
Originは複数個使うことができます。  
  
Originを束ねてフェールオーバーさせることもできます。OriginGroupといいます。  
  
図にするとこんな感じです。  
  
![image.png](/assets/images/6a215c3d-a94e-58d8-2774-0070828d924b.png)  
  
（作図には [draw.io](https://www.draw.io/) を使用しました。これすご過ぎないですか？）  
  
## キャッシュの振る舞い  
  
キャッシュの振る舞いはパスごとに設定できます。  
例えば、「`/static`は最低3日キャッシュ」みたいなことができます。  
どのパスにもマッチしないコンテンツに対しては既定の振る舞いを設定できます。  
  
図にするとこんな感じです。  
  
![image.png](/assets/images/50db49cb-72b8-f679-54c7-88cec36b10a6.png)  
  
  
# 今回作成するもの  
  
なんとなくわかった感じになったところで、今回作成するものです。  
  
* 配信元  
  * Angularでできたフロントエンド。S3に格納しています。  
  * API。LambdaでできたAPIです。クエリストリングを受け取ってJSONを返します。  
* キャッシュの振る舞い  
  * 既定値は3日キャッシュ  
  * APIへのアクセスは1日キャッシュ（あまり変わらないものなので）  
  
# JSONを編集  
  
## Originを作成  
  
まずOriginを作成します。  
Originは、S3とS3以外で少し違います。  
  
### S3の場合  
  
| パラメータ |説明 |   
|:--|:--|:--|  
| DomainName  | S3のホスト名です。 |   
| Id  | Originを区別する一意な文字列です。|  
| S3OriginConfig | S3へアクセスするのに必要なIdentityです。 |  
  
`S3OriginConfig`は、プライベートなS3の時に使う認証情報です。  
今回はパブリックなS3なので使いません。  
  
### S3以外の場合  
  
| パラメータ |説明 |   
|:--|:--|:--|  
| DomainName  | S3の場合と一緒です。  |   
| Id  | Originを区別する一意な文字列です。|  
| CustomOriginConfig  | このOriginの設定です。  |   
  
`CustomOriginConfig`はこんな内容です。  
  
| パラメータ |説明 |   
|:--|:--|  
|HttpPort|HTTPポート番号です。必須です。|  
|HttpsPort|HTTPSポート番号です。必須です。|  
|OriginProtocolPolicy|http-only、match-viewer、https-onlyのいずれかです。|  
|OriginSslProtocols|TLSv1等SSL接続に使うプロトコルです。｜  
  
### できあがったJSON  
  
#### Angularでできたフロントエンド用  
  
****  
```json:
        {
          "Id": "origin-s3",
          "DomainName": "hokanchan.uart.dev.s3-website-ap-northeast-1.amazonaws.com",
          "S3OriginConfig": {
            "OriginAccessIdentity": ""
          }
        }
```  
  
`S3OriginConfig`は中身なくても記述しないとダメです。  
  
#### API用  
  
****  
```json:
        {
          "Id": "origin-api",
          "DomainName": "4719bbobr6.execute-api.us-west-1.amazonaws.com",
          "OriginPath": "/Prod/suggest",
          "CustomOriginConfig": {
            "HTTPPort": 80,
            "HTTPSPort": 443,
            "OriginProtocolPolicy": "https-only",
            "OriginSslProtocols": {
              "Quantity": 1,
              "Items": ["TLSv1.2"]
            },
            "OriginReadTimeout": 60,
            "OriginKeepaliveTimeout": 1
          }
        }
```  
  
[TLSv1.2のみがベストプラクティス](https://aws.amazon.com/jp/premiumsupport/knowledge-center/api-gateway-cloudfront-distribution/)だそうです。  
  
  
## キャッシュの振る舞いを作成  
  
キャッシュを制御するパラメータはたくさんあります。  
  
* 既定用 [DefaultCacheBehavior](https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_DefaultCacheBehavior.html)。  
* 特定パス用 [CacheBehavior](https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_CacheBehavior.html)  
  
中身は大体一緒です。とりあえず必須パラメータだけあげます。  
  
  
| パラメータ | 説明 |  
|:--|:--|  
| ForwardedValues  | クエリ文字列やCookieの振る舞いを設定します。  |  
| MinTTL  | キャッシュに残す最小TTLです。  |  
| TargetOriginId  | 先に設定したOriginのIdです。ルーティング先です。  |  
| TrustedSigners  | Originがプライベートなもののときに使う認証情報です（で、いいのかな）。  |  
| ViewerProtocolPolicy  | Originへのアクセスに使えるものです allow-all、https-only、redirect-to-httpsのいずれかです。  |  
  
特定パス用には、パスを特定するパラメータ [PathPattern](https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/distribution-web-values-specify.html#DownloadDistValuesPathPattern) も必須です。  
  
### できあがったJSON  
  
#### 既定用  
  
```json
    "DefaultCacheBehavior": {
      "TargetOriginId": "origin-s3",
      "ForwardedValues": {
        "QueryString": true,
        "Cookies": {
          "Forward": "all"
        }
      },
      "TrustedSigners": {
        "Enabled": false,
        "Quantity": 0
      },
      "ViewerProtocolPolicy": "redirect-to-https",
      "MinTTL": 0,
      "DefaultTTL": 259200,
      "Compress": true,
    }
```  
  
#### API用  
  
APIに渡すクエリストリングに応じてキャッシュするようにしました。  
[HTTPヘッダに`Host`以外の何かを指定する必要](https://aws.amazon.com/jp/premiumsupport/knowledge-center/api-gateway-cloudfront-distribution/)があります。  
  
```json
    "CacheBehaviors": {
      "Quantity": 1,
      "Items": [
        {
          "PathPattern": "/Prod/suggest/*",
          "TargetOriginId": "origin-api",
          "ForwardedValues": {
            "QueryString": true,
            "Cookies": {
              "Forward": "all"
            },
            "Headers": {
              "Quantity": 6,
              "Items": [
                "Accept",
                "Accept-Encoding",
                "Accept-Language",
                "Cache-Control",
                "User-Agent",
                "DNT"
              ]
            },
            "QueryStringCacheKeys": {
              "Quantity": 1,
              "Items": ["q"]
            }
          },
          "TrustedSigners": {
            "Enabled": false,
            "Quantity": 0
          },
          "ViewerProtocolPolicy": "allow-all",
          "MinTTL": 0,
          "DefaultTTL": 86400,
          "Compress": true
        }
      ]
    }
```  
  
## その他記述するもの  
  
これらのパラメータが必須です。  
  
| パラメータ | 説明 |  
|:--|:--|  
| CallerReference  | CloundFront作成呼び出しを一意に識別する文字列。シリアルみたいなものですね  |  
| Comment  | 作成しようとしているディストリビューションにつける任意のコメント  |  
| Enabled  | 有効か無効か。もちろん有効にするので Yes 一択です。  |  
  
### 独自ドメイン用サーバ証明書  
  
*.cloudfront.comではなく、独自ドメインで運用したいので設定します。  
Amazon Certificate ManagerのUS-EAST-1リージョンに証明書がないといけないみたいです。  
  
```json
    "ViewerCertificate": {
      "CloudFrontDefaultCertificate": false,
      "ACMCertificateArn": "arn:aws:acm:us-east-1:069866334413:certificate/6bbabedf-c976-49b5-9633-f676aa7f1f2c",
      "SSLSupportMethod": "sni-only"
    }
```  
  
ARNは次のコマンドで取得できます。  
  
```cl
aws --region us-east-1  acm list-certificates
```  
  
### / でアクセスされた時に返すもの  
  
https://www.example.com/ でアクセスされた時にindex.htmlを返すようにします。  
  
****  
```json:
    "DefaultRootObject": "index.html",
```  
  
### 独自ドメイン名  
  
使用する独自ドメイン名を記述します。  
  
```json
    "Aliases": {
      "Quantity": 1,
      "Items": ["hokanchan.uart.dev"]
    },
```  
  
# awsコマンドを実行  
  
JSONファイルができました。  
awsコマンドを実行します。  
  
```cl
aws cloudfront create-distribution --cli-input-json file://aws-cloudfront-distribution.json
```  
  
## エラーが出たら……  
  
必須と書いてなくても、存在しないといけない項目があるようです。  
最初に生成した雛形から消し過ぎないほうがよさそうです。  
  
# DNSを設定  
  
独自ドメインにしたので、ネームサーバにCNAMEレコードを登録します。  
登録する値は次のコマンドでわかります。  
  
```cl
aws cloudfront list-distributions --query 'DistributionList.Items[*].DomainName'
```  
  
レコードが存在するとエラーになりますので、存在する場合は消しておきます。  
