---
date: '2019-10-04T13:34:51+09:00'
layout: post
published: true
qiita_article_id: 9c82852d13dcc5c6781d
tags:
- Azure
- AzureBastion
title: '[Microsoft] Azure Bastionを az コマンドラインから作成する'
updated: '2019-11-22T21:21:25+09:00'

---
# これはなに？  
  
Azure Bastionをコマンドラインから作成します。  
azコマンドを使います。  
  
# テンプレート  
  
早速ですが、こんなテンプレートで作成できます。  
  
**bastion-template.json**  
```json:bastion-template.json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string"
        },
        "bastionHostName": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        },
        "publicIpAddressId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-10-01",
            "type": "Microsoft.Network/bastionHosts",
            "name": "[parameters('bastionHostName')]",
            "location": "[parameters('location')]",
            "dependsOn": [],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "IpConf",
                        "properties": {
                            "subnet": {
                                "id": "[parameters('subnetId')]"
                            },
                            "publicIPAddress": {
                                "id": "[parameters('publicIpAddressId')]"
                            }
                        }
                    }
                ]
            },
            "tags": {}
        }
    ]
}
```  
  
# テンプレートの使い方  
  
azコマンドからは、このように使います。  
  
```sh
az group deployment create \
    --resource-group $GROUP_NAME \
    --template-file bastion-template.json \
    --parameters \
        location=${LOCATION} \
        bastionHostName=${BASTION_HOST_NAME} \
        subnetId=${SUBNET_ID} \
        publicIpAddressId=${IPADDRESS_ID}
```  
  
`GROUP_NAME` `LOCATION` `BASTION_HOST_NAME` は自由に決めてください。  
  
  
# 事前に必要なもの  
  
サブネットとパブリックIPは、事前に作成しておいてください。  
それぞれ条件があります。  
  
## サブネット作成時の条件  
  
* /27 より広くする  
* 名前を **AzureBastionSubnet** にする  
* NSGを割り当てる場合は、全通しにする  
  
## パブリックIP作成時の条件  
  
* SKUをStandardにする  
* （アロケーションメソッドは自動的にStatic）  
  
## IDの取得方法  
  
作成後、それぞれのIDは下記のコマンで取得できます。  
  
### サブネット のIDを取得  
  
```sh

az resource show \
        --resource-group ${GROUP_NAME} \
        --namespace Microsoft.Network \
        --parent virtualnetworks/${VNET_NAME} \
        --resource-type subnets \
        --name AzureBastionSubnet \
        --query id \
        --output tsv
```  
  
`GROUP_NAME` `VNET_NAME` は既存のものを使います。  
  
### パブリックIPのIDを取得  
  
```sh
az resource show \
        --resource-group ${GROUP_NAME} \
        --resource-type Microsoft.Network/publicIPAddresses \
        --name ${PUBLIC_IP_NAME} \
        --query id \
        --output tsv
```  
  
# 参考リンク  
  
- [Azure Bastion (Preview) を試してみる](https://qiita.com/tetsuya-ooooo/items/365ee7766c039a5033cf)  
  
  
# 最後に  
  
Azure Bastionは、踏み台サーバというよりブラウザ内で動くコンソールです。  
これはこれで便利ですが、名前とあっていないかなぁ。  
Bastionなら、sshトンネルとか使えて欲しい。  
