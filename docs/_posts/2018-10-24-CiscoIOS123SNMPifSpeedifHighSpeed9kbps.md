---
date: '2018-10-24T12:51:49+09:00'
layout: post
published: true
qiita_article_id: 19903bc92da2951faa7a
tags:
- zabbix
- snmp
- Cisco
title: '[Cisco] IOS 12.3 SNMPで取得できるifSpeed/ifHighSpeedが小さすぎる。9kbpsとか'
updated: '2018-10-24T12:51:49+09:00'

---
# 前段  
  
[ZABBIX](https://www.zabbix.com/)でCiscoルータを監視したかったんです。  
  
ZABBIXには[低レベルディスカバリ](https://www.zabbix.com/documentation/current/manual/discovery/low_level_discovery/snmp_oids)という便利機能があり、テンプレートを割り当てるだけでネットワークインターフェースを探し出してトリガとグラフが使えるようになります。  
  
トリガのひとつとして、帯域90%超えを報告してくれるものがあります。  
  
このとき、帯域をSNMPの`IF-MIB::ifHighSpeed` OIDから取得しています。  
  
この値が小さすぎるのです。  
  
# 実機での様子  
  
`IF-MIB::ifHighSpeed`だと値が0になるので、`IF-MIB::ifSpeed`を取得してみます。  
  
```shell-session
$ snmpwalk -c public -v 2c mycisco IF-MIB::ifSpeed
IF-MIB::ifSpeed.1 = Gauge32: 100000000
IF-MIB::ifSpeed.2 = Gauge32: 100000000
IF-MIB::ifSpeed.3 = Gauge32: 16000
IF-MIB::ifSpeed.4 = Gauge32: 64000
IF-MIB::ifSpeed.5 = Gauge32: 64000
IF-MIB::ifSpeed.6 = Gauge32: 100000000
IF-MIB::ifSpeed.7 = Gauge32: 100000000
IF-MIB::ifSpeed.8 = Gauge32: 100000000
IF-MIB::ifSpeed.9 = Gauge32: 100000000
IF-MIB::ifSpeed.10 = Gauge32: 100000000
IF-MIB::ifSpeed.11 = Gauge32: 100000000
IF-MIB::ifSpeed.12 = Gauge32: 100000000
IF-MIB::ifSpeed.13 = Gauge32: 100000000
IF-MIB::ifSpeed.14 = Gauge32: 4294967295
IF-MIB::ifSpeed.15 = Gauge32: 100000000
IF-MIB::ifSpeed.16 = Gauge32: 9000
IF-MIB::ifSpeed.17 = Gauge32: 56000
IF-MIB::ifSpeed.18 = Gauge32: 100000000
IF-MIB::ifSpeed.19 = Gauge32: 100000000
IF-MIB::ifSpeed.20 = Gauge32: 56000
IF-MIB::ifSpeed.21 = Gauge32: 144000
IF-MIB::ifSpeed.22 = Gauge32: 16000
IF-MIB::ifSpeed.23 = Gauge32: 64000
IF-MIB::ifSpeed.24 = Gauge32: 64000
IF-MIB::ifSpeed.25 = Gauge32: 100000000
IF-MIB::ifSpeed.26 = Gauge32: 56000
```  
  
それぞれネットワークインターフェースが何かというと、こんなんです。  
  
```shell-session
$ snmpwalk -c public -v 2c mycisco IF-MIB::ifDescr
IF-MIB::ifDescr.1 = STRING: FastEthernet0
IF-MIB::ifDescr.2 = STRING: FastEthernet1
IF-MIB::ifDescr.3 = STRING: BRI0
IF-MIB::ifDescr.4 = STRING: BRI0:1
IF-MIB::ifDescr.5 = STRING: BRI0:2
IF-MIB::ifDescr.6 = STRING: FastEthernet2
IF-MIB::ifDescr.7 = STRING: FastEthernet3
IF-MIB::ifDescr.8 = STRING: FastEthernet4
IF-MIB::ifDescr.9 = STRING: FastEthernet5
IF-MIB::ifDescr.10 = STRING: FastEthernet6
IF-MIB::ifDescr.11 = STRING: FastEthernet7
IF-MIB::ifDescr.12 = STRING: FastEthernet8
IF-MIB::ifDescr.13 = STRING: FastEthernet9
IF-MIB::ifDescr.14 = STRING: Null0
IF-MIB::ifDescr.15 = STRING: Vlan1
IF-MIB::ifDescr.16 = STRING: Tunnel25
IF-MIB::ifDescr.17 = STRING: Dialer1
IF-MIB::ifDescr.18 = STRING: Vlan128
IF-MIB::ifDescr.19 = STRING: Vlan240
IF-MIB::ifDescr.20 = STRING: Dialer2
IF-MIB::ifDescr.21 = STRING: BRI0-Physical
IF-MIB::ifDescr.22 = STRING: BRI0-Signaling
IF-MIB::ifDescr.23 = STRING: BRI0:1-Bearer Channel
IF-MIB::ifDescr.24 = STRING: BRI0:2-Bearer Channel
IF-MIB::ifDescr.25 = STRING: Virtual-Access1
IF-MIB::ifDescr.26 = STRING: Virtual-Access2
```  
  
BRIが64kbps、Dialerが56kbpsなのは由来がわかります。  
Tunnelが9kbpsなのは、どこからきた数字なんでしょ？  
  
# 困ったこと  
  
9kbps超えて、アラート出っぱなしです。  
  
  
# 解決  
  
ルータの設定を変更します。  
  
```
interface tunnel 25
bandwidth 100000
```  
  
  
# 感想  
  
こういう素人問題でも、投稿するとなぜか自己解決しますね。  
  
やっぱり情報発信してくといいんじゃないかな。  
  
  
