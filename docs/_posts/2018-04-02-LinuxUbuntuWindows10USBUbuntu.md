---
'created_at: ': '2018-04-02T14:00:42+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Windows
      versions: []
    - name: Linux
      versions: []
    - name: Ubuntu
      versions: []
  state: 0
title: "[Linux][Ubuntu] Windows 10\u30A4\u30F3\u30B9\u30C8\u30FC\u30EB\u7528USB\u30E1\
  \u30E2\u30EA\u3092Ubuntu\u4E0A\u3067\u4F5C\u6210\u3059\u308B"
'updated_at: ': '2018-04-02T14:00:42+09:00'

---
# これはなに？  
  
Ubuntuマシンしか持たないひとが、Windows 10のインストーラを作成するお話です。  
  
# 手順  
  
WoeUSBを使います。手順としては、下記URLに書いてあるとおりです。  
  
http://www.webupd8.org/2017/06/tool-to-create-bootable-windows-usb.html  
  
  
## :one: Windows 10のISOイメージを用意  
  
Windows 10のISOイメージをダウンロードします。  
  
https://www.microsoft.com/ja-jp/software-download/windows10ISO  
  
## :two: WoeUSBをインストール  
  
```bash
sudo add-apt-repository ppa:nilarimogard/webupd8
sudo apt-get update
sudo apt install woeusb
```  
  
[オプショナル] aptレポジトリを削除します。  
  
```bash
sudo add-apt-repository --remove ppa:nilarimogard/webupd8
sudo apt-get update
```  
  
## :three: ISOファイルをUSBメモリへ書き込み  
  
WoeUSBを起動します。  
  
ISOファイルと、書き込み先USBメモリを選択します。  
  
![image.png](/assets/images/1a5ddd95-9de1-e24f-1018-c1cba6af6c5c.png)  
  
  
待ちます。  
  
![image.png](/assets/images/20ca9911-a033-1cf5-63eb-95653e1e06b7.png)  
  
  
これでおしまいです。  
お疲れ様でした。  
  
  
