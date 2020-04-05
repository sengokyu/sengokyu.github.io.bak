---
date: '2019-09-29T16:59:53+09:00'
layout: post
published: true
qiita_article_id: 6ed5b70f35d6d6008607
tags:
- AWS
- TypeScript
- Angular
- CloudFormation
- lambda
title: '[AWS] AngularプロジェクトとAWS Lambdaプロジェクトをいっぺんにビルド＆デプロイしたい'
updated: '2019-11-22T21:21:42+09:00'

---
# はじめに  
  
これは頭の中を整理するための試行錯誤的なメモです。  
ちゃんとしたオチまでたどり着くかは不明です。  
ブラウザバックするなら今のうちです。  
  
もし先に進まれる方は、固有名そのままのところは、薄目で見てやってください。  
  
# 達成したいこと  
  
2つのプロジェクトがあります。  
  
* Angularでできたフロントエンド → S3にアップロードしています。  
* AWS Lambdaで動かすバックエンド  
  
2つのプロジェクトをいっぺんにビルドしてデプロイしたいです。  
  
  
# 統合前の各プロジェクトの構成  
  
各プロジェクトのディレクトリ構成と、ビルド＆デプロイ方法はこのようになっています。  
  
  
## ディレクトリ構成  
  
### Angular  
  
```shell
.
├── angular.json
├── browserslist
├── dist/              # ビルド結果はここ
├── e2e/
├── karma.conf.js
├── node_modules/
├── package-lock.json
├── package.json
├── src/
│   ├── app/
│   ├── assets/
│   ├── environments/
│   ├── favicon.ico
│   ├── index.html
│   ├── main.ts
│   ├── polyfills.ts
│   ├── styles.scss
│   └── test.ts
├── tsconfig.app.json
├── tsconfig.json
├── tsconfig.spec.json
└── tslint.json
```  
  
### AWS Lambda  
  
TypeScriptを使っています。  
  
```shell
.
├── .aws-sam/
│   └── build/  # ビルド結果はここ。CloudFormationのテンプレートも作成される
├── coverage/
├── event.json
├── karma.conf.js
├── node_modules/
├── package-lock.json
├── package.json
├── src/
│   └── google-suggest-proxy/  # プログラムはここ 
├── template.yaml              # CloudFormationテンプレートのテンプレート
├── tsconfig.app.json
├── tsconfig.json
├── tsconfig.spec.json
└── webpack.config.js
```  
  
  
## ビルド＆デプロイ方法  
  
### Angular  
  
ビルドは`ng`コマンドを使います。  
  
```shell
ng build --prod
```  
  
デプロイは`aws`コマンドを使います。S3へアップロードしています。  
  
```console
aws s3 sync dist/hokanchan s3://hokanchan/
```  
  
  
### AWS Lambda  
  
ビルドはwebpackです。  
  
```shell
./node_modules/.bin/webpack-cli
```  
  
デプロイは`sam`コマンドを使います。パッケージングと実際のデプロイの2段階です。  
  
```shell
sam package --template-file ./aws-sam/build/template.yml --s3-bucket ラムダ置き場 --output-template-file /tmp/packaged.yml
sam deploy --template-file /tmp/packages.yml --stack-name hokanchan --capabilities CAPABILITY_IAM
```  
  
# まずビルドについて  
  
いっぺんにビルドしたいので、2つのプロジェクトが一緒のディレクトリに入っていると嬉しいような気がします。  
（難しいこと考えないで、シェルスクリプトかMakefileでまとめたほうが簡単かもしれないけど、あえてやります）。  
  
## 悩みどころ  
  
各プロジェクトのディレクトリ構成でコンフリクトしているものがあります。  
  
* `src`ディレクトリ  
* `tsconfig.json`  
* `tsconfig.app.json`  
* `tsconfig.spec.json`  
* `karma.conf.js`  
  
これらをどう統合、あるいは分けて使えばよいか。。。  
  
## 解決案  
  
### 方針  
  
こんなふうに考えました。  
  
設定ファイルを統合するのはまたにして、まずは分けた状態でまとめよう。  
`ng`コマンドの設定に手を出すと道のりが長そうなので、AWS LambdaをAngular側に寄せよう。  
Angularはgenerate applicationしてprojectsディレクトリへ移そう。  
  
### 考えたディレクトリ構成  
  
  
```shell
.
├── .aws-sam/          # AWS Lambdaビルド結果
├── dist/              # Angularビルド結果
├── prjects/           
│   ├── frontend-hokanchan/    # Angularのもの
│   │   ├── src/               # Angularのプログラムソース
│   │   ├── karma.conf.json
│   │   ├── tsconfig.app.json
│   │   └── tsconfig.spec.json
│   └── backend-google-suggest-proxy/  # AWS Lambdaのもの 
│   │   ├── src/                       # AWS Lambdaのプログラムソース
│   │   ├── karma.conf.json
│   │   ├── tsconfig.app.json
│   │   └── tsconfig.spec.json
├── template.yaml              # モトイキ
├── tsconfig.json              # Angular用
├── tsconfig.sam.json          # AWS Lambda用
└── webpack.config.js          # モトイキ
```  
  
この構成に合わせて、`webpack.config.js` `angular.js` `tsconfig.*.json` `karma.conf.json` を書き換えました。  
  
期待通りにビルドやテストができるようになりました。  
  
# さてデプロイについて  
  
ビルドができるようになったので、次はデプロイです。  
  
## 悩みどころ  
  
- Lambdaへのデプロイと、S3への静的コンテンツアップロードを同時にやりたい  
    - Lambdaへのデプロイは2段階  
        - 1段階目は、パッケージングとS3へのアップロード  
        - 2段階目は、実際にLambdaへのデプロイ  
    - 2段階目と同時に、静的コンテンツのアップロードはできる？  
        - できそう？ →  [How to use CloudFormation to deploy Frontend Apps to S3 and Serverless Application Repository](https://serverless.pub/deploy-frontend-to-s3-and-sar/)  
            - いったんLambdaにアップロードしたファイルを、あとからS3に入れるっぽい。なかなかトリッキー  
        - 他に、S3へアップロードするLambdaを作って使うという手もあるらしい。これもまた搦め手のような。  
- SAM(Serveless-applicatin-model)でLambdaを作ると、API Gatewayも作られる  
    - このAPI Gatewayに独自ドメインを割り当てたい  
    - このAPI GatewayからS3に入れた静的コンテンツも配信させたい  
  
## 解決（あるいは諦め）案  
  
- CloundFormationによる静的コンテンツのアップロードは諦める。  
    - 静的コンテンツをアップロードする素直なやり方はなさそう。  
    - 静的コンテンツとLambdaは別々にデプロイする。  
- API Gatewayのルートに、S3 WebサイトをGETするメソッドを追加する。  
  
## CloudFormationテンプレートに追加するリソース  
  
`sam`コマンドで生成したテンプレートには、あらかじめ [AWS::Serverless::Function](https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#awsserverlessfunction) が入っています。  
  
これ以外に追加するリソースです。  
  
### S3バケットとポリシー  
  
公開WebサイトとしてS3バケットを作ります。  
AWSのテンプレートスニペットからコピペして作りました。  
  
#### S3バケット  
  
```yaml
  StaticContentBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Ref bucketName
      AccessControl: PublicRead
      WebsiteConfiguration:
        IndexDocument: index.html
        ErrorDocument: error.html
```  
  
#### ポリシー  
  
```yaml
  BucketPolicy:
    Type: AWS::S3::BucketPolicy
    DependsOn: StaticContentBucket
    Properties:
      Bucket: !Ref StaticContentBucket
      PolicyDocument:
        Id: StaticContentBucketPolicy
        Version: '2012-10-17'
        Statement:
          - Sid: PublicReadForGetBucketObjects
            Effect: Allow
            Principal: '*'
            Action: 's3:GetObject'
            Resource: !Join
              - ''
              - - 'arn:aws:s3:::'
                - !Ref StaticContentBucket
                - /*
```  
  
  
### API Gatewayドメイン名とマッピング  
  
独自ドメイン名を使えるようにします。  
  
#### ドメイン名  
  
```yaml
    Type: AWS::ApiGateway::DomainName
    Properties:
      CertificateArn: !Ref certificateArn
      DomainName: !Ref domainName
      EndpointConfiguration:
        - REGIONAL
```  
  
#### マッピング  
  
[ServerlessRestSApi](https://github.com/awslabs/serverless-application-model/blob/master/docs/internals/generated_resources.rst#api)は、`AWS::Serverless::Function`の中で作成されるAPI Gatewayの論理名です。  
  
```yaml
  DomainNameMapping:
    DependsOn:
      - RestApi
      - CustomDomainName
    Type: AWS::ApiGateway::BasePathMapping
    Properties:
      DomainName: !Ref CustomDomainName
      RestApiId: !Ref ServerlessRestApi
      BasePath: ''
```  
  
  
### 静的コンテンツのリソースとメソッド  
  
API Gatewayのルート直下にHTTP Proxyを設定してS3バケットへ向けます。  
  
#### API Gatewayリソース  
  
```yaml
  StaticContentResource:
    Type: AWS::ApiGateway::Resource
    Properties:
      ParentId: !GetAtt RestApi.RootResourceId
      RestApiId: !Ref ServerlessRestApi
      PathPart: '{path}'
```  
  
#### API Gatewayメソッド  
  
```yaml
  StaticContentResourceMethod:
    DependsOn: StaticContentResource
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: GET
      ResourceId: !Ref StaticContentResource
      RestApiId: RestApi
      AuthorizationType: NONE
      RequestParameters:
        method.request.path.path: true
      Integration:
        IntegrationHttpMethod: GET
        Type: HTTP_PROXY
        Uri: !Join
          - ''
          - - !GetAtt StaticContentBucket.WebsiteURL
            - '/{path}'
        PassthroughBehavior: WHEN_NO_MATCH
        IntegrationResponses:
          - StatusCode: 200
        CacheKeyParameters:
          - 'method.request.path.path'
        RequestParameters:
          integration.request.path.path: 'method.request.path.path'
```  
  
# コマンドを`package.json`にまとめる  
  
よく使ったコマンドは`package.json`にまとめました。  
これで忘れても安心。  
  
AWS S3へビルドしたAngularをアップロード  
  
****  
```json:
    "s3:sync": "aws s3 sync dist/frontend-hokanchan  s3://hokanchan.uart.dev",
```  
  
AWS S3コンテナの中身を削除  
  
```json
    "s3:delete": "aws s3 rm s3://hokanchan.uart.dev --recursive",
```  
  
Lambdaをビルド  
  
```json
    "sam:build": "webpack-cli",
```  
  
Lambdaをテスト  
  
```json
    "sam:test": "karma start projects/backend-google-suggest-proxy/karma.conf.js",
```  
  
Lambdaをパッケージング  
  
```json
    "sam:package": "npm run sam:build;sam package --template-file .aws-sam/build/template.yaml --s3-bucket lambdastash-ap1 --output-template-file /tmp/packaged.yaml",
```  
  
Lambda等をデプロイ  
  
```json
    "sam:deploy": "npm run sam:package;sam deploy --template-file /tmp/packaged.yaml --stack-name hokanchan --capabilities CAPABILITY_IAM",
```  
  
Cloudformationの失敗内容を出力  
  
```json
    "sam:failed": "aws cloudformation describe-stack-events --stack-name hokanchan --query 'StackEvents[?ResourceStatus==`CREATE_FAILED`]'",
```  
  
できあがったAPI GatewayのURLを出力  
  
```json
    "sam:apiurl": "aws cloudformation describe-stacks --stack-name hokanchan --query 'Stacks[].Outputs[?OutputKey==`GoogleSuggestProxyApi`]' --output table",
```  
  
Cloudformationスタックを削除  
  
```json
    "sam:undeploy": "aws cloudformation delete-stack --stack-name hokanchan;aws cloudformation wait stack-delete-complete --stack-name hokanchan"
```  
  
  
  
# 参考リンク  
  
* [aws cloudformation package コマンドで自動的にS3にアップロードしてくれるリソース一覧](https://qiita.com/ikeisuke/items/e46641a3ec582baba3f5)  
* [How to use CloudFormation to deploy Frontend Apps to S3 and Serverless Application Repository](https://serverless.pub/deploy-frontend-to-s3-and-sar/)  
* AWS SAMドキュメント  
  * [リソース種別](https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#resource-types)   
  * [生成されるリソース](https://github.com/awslabs/serverless-application-model/blob/master/docs/internals/generated_resources.rst#api)  
* AWS Cloudformationリソースリファレンス  
  * [AWS::S3::Bucket](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html)  
  * [AWS::S3::BucketPolicy](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-properties-s3-policy.html)  
  * [AWS::ApiGateway::Resource](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-apigateway-resource.html)  
  * [AWS::ApiGateway::Resource Integration](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-properties-apitgateway-method-integration.html)  
  * [AWS::ApiGateway::Method](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-apigateway-method.html)  
* [Setting up an Api Gateway Proxy Resource using Cloudformation](https://cjohansen.no/aws-apigw-proxy-cloudformation/)  
* [テンプレートスニペット カスタムドメインを使用した静的ウェブサイトの作成](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/quickref-s3.html#scenario-s3-bucket-website-customdomain)  
  
  
# もしかしたらダメかも？  
  
長々と書いてきましたが、成功していません。  
  
  
`AWS::Gateway::RestApi`リソースを作成したら、`API::Gateway::Deployment`リソースも作成しないと、実際に使えるようにはなりません。  
`API::Gateway::Deployment`は、`AWS::Serverless::Function`の中で暗黙的に作成されます。  
したがって、`API::Serverless::Function`が作成された後に`AWS::Gateway::Resource`や`AWS::Gateway::Method`を追加しても使えません。  
  
  
`AWS::Serverless::*`をやめて、すべて手書きするしかないかなー？  
