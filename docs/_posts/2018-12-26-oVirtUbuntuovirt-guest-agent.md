---
'created_at: ': '2018-12-26T10:19:00+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Ubuntu
      versions: []
    - name: oVirt
      versions: []
  state: 0
title: "[oVirt] Ubuntu\u3067\u30B2\u30B9\u30C8\u30A8\u30FC\u30B8\u30A7\u30F3\u30C8\
  \ ovirt-guest-agent \u304C\u52D5\u304B\u306A\u3044\u3068\u304D\u306E\u89E3\u6C7A\
  \u7B56"
'updated_at: ': '2018-12-26T10:19:00+09:00'

---
# 前フリ  
  
oVirtの管理画面から仮想マシンを見ると  
「ゲストエージェントが古い！」  
と警告が表示されていることがあります。  
  
どれどれと仮想マシンにログインして見てみると  
  
****  
```terminal:
# systemctl status ovirt-guest-agent
* ovirt-guest-agent.service - oVirt Guest Agent
   Loaded: loaded (/lib/systemd/system/ovirt-guest-agent.service; enabled; vendor preset: enabled)
   Active: failed (Result: exit-code) since Wed 2018-12-26 09:16:35 JST; 42min ago
  Process: 16439 ExecStart=/usr/bin/python /usr/share/ovirt-guest-agent/ovirt-guest-agent.py (code=exited, status=1/FAILURE)
```  
  
ゲストエージェントが動いてない！  
  
# 環境  
  
* oVirt 4.2.6  
* Ubuntu 16.04  
* oVirt Guest Agent 1.0.11.3-1.1  
  
  
# 原因  
  
ログを見てみます。  
  
**/var/log/ovirt-guest-agent/ovirt-guest-agent.log**  
```text:/var/log/ovirt-guest-agent/ovirt-guest-agent.log
OSError: [Errno 13] Permission denied: '/dev/virtio-ports/ovirt-guest-agent.0'
```  
  
デバイスファイルのパーミッションが悪いみたいです。  
  
****  
```terminal:
# ls -l /dev/virtio-ports/
lrwxrwxrwx 1 root root 11 Dec 26 09:55 com.redhat.spice.0 -> ../vport2p3
lrwxrwxrwx 1 root root 11 Dec 26 09:55 org.qemu.guest_agent.0 -> ../vport2p2
lrwxrwxrwx 1 root root 11 Dec 26 09:55 ovirt-guest-agent.0 -> ../vport2p1
```  
  
シンボリックリンクになっていました。実体は  
  
****  
```terminal:
# ls -l /dev/vport2p*
crw------- 1 root root 248, 1 Dec 26 09:55 /dev/vport2p1
crw------- 1 root root 248, 2 Dec 26 09:55 /dev/vport2p2
crw------- 1 root root 248, 3 Dec 26 09:55 /dev/vport2p3
```  
  
rootユーザしか読み書きできないです。  
  
# 解決策  
  
udevへ指示するファイルを作成します。  
ファイル名は、拡張子が`.rules`であればなんでもよいです。  
  
**/etc/udev/rules.d/80-virtio-ports.rules**  
```text:/etc/udev/rules.d/80-virtio-ports.rules
SUBSYSTEM=="virtio-ports",GROUP="ovirtagent",MODE="0660"
```  
  
ルールを適用するには、`udevadm`コマンドを使います。  
  
```terminal
udevadm trigger --subsystem-match=virtio-ports
```  
  
「デバイスが変わったよー」ということを通知しています。  
  
  
無事パーミッションが設定されました。  
  
****  
```terminal:
# ls -l /dev/vport2p*
crw-rw---- 1 root ovirtagent 248, 1 Dec 26 09:55 /dev/vport2p1
crw-rw---- 1 root ovirtagent 248, 2 Dec 26 09:55 /dev/vport2p2
crw-rw---- 1 root ovirtagent 248, 3 Dec 26 09:55 /dev/vport2p3
```  
  
  
  
# 落穂ひろい  
  
## udevルールの読み方  
  
`==`で結ばれているところは検索条件です。上では、SUBSYSTEMがvirtio-portsにマッチするデバイスを意味します。  
  
`=`で結ばれているところはアクションです。上では、オーナーグループとモードを設定しています。  
  
詳しくは udev(7) マニュアルを見てください。  
  
## udevルールの作り方  
  
検索条件として何を指定すればよいかは、`udevadm`コマンドで調べられます。  
  
```terminal
# udevadm info --query=all --attribute-walk --name=vport2p1
(...省略...)
  looking at device '/devices/pci0000:00/0000:00:05.0/virtio2/virtio-ports/vport2p1':
    KERNEL=="vport2p1"
    SUBSYSTEM=="virtio-ports"
    DRIVER==""
    ATTR{name}=="ovirt-guest-agent.0"
(...省略...)
```  
  
`udevadm`コマンドに調べたいデバイス名を渡してください。  
  
  
  
  
  
