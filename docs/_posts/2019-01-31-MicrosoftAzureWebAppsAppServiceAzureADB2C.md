---
date: '2019-01-31T11:08:58+09:00'
layout: post
published: true
qiita_article_id: 3a4b2edbcfa00344ff55
tags:
- Azure
- AzureWebApps
- AzureADB2C
title: '[Microsoft] Azure WebApps (App Service)の簡易認証でAzure AD B2Cを使う方法'
updated: '2019-02-25T12:57:30+09:00'

---
Azure App Serviceの簡易認証でAzure AD B2Cを使うと、フリープランのAzure ADではできないログイン画面のカスタマイズができる(?)かもしれません。  
  
![image.png](/assets/images/a7633b9b-da3f-abea-ed1c-2ddb389a72bf.png)  
これはあらかじめ用意されているGrayテンプレートを使用したログイン画面です。  
  
# 簡易認証の設定に必要な情報  
  
簡易認証を設定するときには、以下の情報が必要です。  
  
- Client ID  
- Issuer Url  
- Client Secret  
  
![image.png](/assets/images/3b0d35ce-a209-c242-3344-65b1c410825d.png)  
  
それぞれの取得方法を説明します。  
  
# あらかじめ必要なもの  
  
Azure AD B2Cテナントは作成済みであるとします。  
  
まだの場合は[Tutorial: Create an Azure Active Directory B2C tenant](https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-create-tenant)の手順に従って作成します。  
  
# 簡易認証に必要な情報の取得方法  
  
## Client ID  
  
Azure AD B2Cに登録したアプリケーションのApplication IDです。  
  
### アプリケーションの登録方法  
  
アプリケーション名はお好みでつけます。  
  
Reply URLをWebAppsのURL+`/.auth/login/aad/callback`にします。  
  
![image.png](/assets/images/d5754c30-6fac-dfdc-572f-53bf2b32155e.png)  
  
登録すると、Application IDが発行されます。  
  
![image.png](/assets/images/13fcc820-b4d0-4493-b668-06b1c3a48ef8.png)  
  
  
## Issuer Url  
  
Azure AD B2Cに登録したUser flows(policies)から作成します。  
  
User flows(policies)を作成[^1]したら、Run user flowをクリックします。  
  
[^1]: 画面をカスタマイズするには、プレビュー版のSign in v2を使用する必要があります。  
  
![image.png](/assets/images/d9c58cdd-7f51-bcb0-92e0-5ae6e1ab56a4.png)  
  
URLが表示されます。  
  
![image.png](/assets/images/006b39f9-3105-10bb-0f7c-a46b0b5f6a8c.png)  
  
  
## Client Secret  
  
Azure AD B2Cに登録したアプリケーションで生成したKeyです。  
  
アプリケーションの画面から、Keyを生成します。  
  
![image.png](/assets/images/509cb2ac-5b32-d122-cf8a-ad621c7ac76b.png)  
  
  
# 参考リンク  
  
- [Cross-Post: App Service Auth and Azure AD B2C](https://blogs.msdn.microsoft.com/appserviceteam/2016/06/22/app-service-auth-and-azure-ad-b2c/)  
- [About user interface customization in Azure Active Directory B2C](https://docs.microsoft.com/en-us/azure/active-directory-b2c/customize-ui-overview)  
  
