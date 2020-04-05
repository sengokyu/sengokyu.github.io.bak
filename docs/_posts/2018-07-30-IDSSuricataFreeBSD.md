---
date: '2018-07-30T15:19:14+09:00'
layout: post
published: true
qiita_article_id: ca0e227410500b9a69ec
tags:
- FreeBSD
- yamaha
- セキュリティ
- suricata
title: '[IDS][Suricata] FreeBSDで始める侵入検知生活'
updated: '2018-07-30T15:37:43+09:00'

---
FreeBSDを使って、侵入(Intrusion)検知(Detection)生活(~~Seikatsu~~System)していきます。  
  
この分野だと [Snort](https://www.snort.org/) が最も有名ですが、今回は使ったことのない [Suricata](https://suricata-ids.org/) を試してみます。  
  
# 環境  
  
* OS: FreeBSD 11.2-RELEASE  
* ネットワークインターフェース名: em0  
  
  
# インストール  
  
## 準備  
  
IPSとして動かす場合には`ipfw`が必要です。  
IPSとして動かす予定はないですが、一応入れておきます。  
  
それぞれファイルに追記します。  
  
**/etc/rc.conf**  
```sh:/etc/rc.conf
firewall_enable="YES"           # Set to YES to enable firewall functionality
firewall_type="open"            # Firewall type (see /etc/rc.firewall)
```  
  
**/boot/loader.conf**  
```sh:/boot/loader.conf
ipfw_load="YES"
ipfw_nat_load="YES"
ipdivert_load="YES"
dummynet_load="YES"
libalias_load="YES"
```  
  
  
## Suricataをインストール  
  
`pkg`を使ってさっくりと入れます。  
  
```shell-session
pkg install suricata
```  
  
## とりあえず動かしてみる  
  
`-i`オプションにネットワークインターフェース名を渡します。  
`--init-errors-fatal`オプションを付けて、ルールにエラーがあったら落ちるようにします。  
  
  
****  
```shell-session:
# suricata -i em0 --init-errors-fatal
22/7/2018 -- 14:11:03 - <Warning> - [ERRCODE: SC_WARN_FASTER_CAPTURE_AVAILABLE(275)] - faster capture option is available: NETMAP (--netmap=em0). Use --pcap=em0 to suppress this warning
22/7/2018 -- 14:11:03 - <Notice> - This is Suricata version 4.0.4 RELEASE
22/7/2018 -- 14:11:03 - <Warning> - [ERRCODE: SC_ERR_NO_RULES(42)] - No rule files match the pattern /usr/local/etc/suricata/rules/botcc.rules
```  
  
ルールファイルがなくて落ちちゃいました。  
  
## ルールファイルを取得  
  
### oinkmasterをインストール  
  
ルールをダウンロードするため、oinkmasterを使います。  
  
****  
```shell-session:
pkg install oinkmaster
```  
  
### oinkmasterを設定  
  
**/usr/local/etc/oinkmaster.conf**  
```text:/usr/local/etc/oinkmaster.conf
url = http://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
path = /bin:/usr/bin:/usr/local/bin
update_files = \.rules$|\.config$|\.conf$|\.txt$|\.map$
```  
  
### oinkmasterを実行  
  
`oinkmaster`を実行します。  
  
****  
```shell-session:
oinkmaster -o /usr/local/etc/suricata/rules
```  
  
`/usr/local/etc/suricata/rules`にファイルが保存されました。  
  
****  
```shell-session:
# ls /usr/local/etc/suricata/rules/
BSD-License.txt                         emerging-dns.rules                      emerging-rpc.rules                      files.rules
app-layer-events.rules                  emerging-dos.rules                      emerging-scada.rules                    gen-msg.map
botcc.portgrouped.rules                 emerging-exploit.rules                  emerging-scan.rules                     gpl-2.0.txt
botcc.rules                             emerging-ftp.rules                      emerging-shellcode.rules                http-events.rules
ciarmy.rules                            emerging-games.rules                    emerging-smtp.rules                     modbus-events.rules
classification.config                   emerging-icmp.rules                     emerging-snmp.rules                     rbn-malvertisers.rules
compromised-ips.txt                     emerging-icmp_info.rules                emerging-sql.rules                      rbn.rules
compromised.rules                       emerging-imap.rules                     emerging-telnet.rules                   reference.config
decoder-events.rules                    emerging-inappropriate.rules            emerging-tftp.rules                     sid-msg.map
dns-events.rules                        emerging-info.rules                     emerging-trojan.rules                   smtp-events.rules
drop.rules                              emerging-malware.rules                  emerging-user_agents.rules              stream-events.rules
dshield.rules                           emerging-misc.rules                     emerging-voip.rules                     suricata-2.0-enhanced-open.txt
emerging-activex.rules                  emerging-mobile_malware.rules           emerging-web_client.rules               tls-events.rules
emerging-attack_response.rules          emerging-netbios.rules                  emerging-web_server.rules               tor.rules
emerging-chat.rules                     emerging-p2p.rules                      emerging-web_specific_apps.rules        unicode.map
emerging-current_events.rules           emerging-policy.rules                   emerging-worm.rules
emerging-deleted.rules                  emerging-pop3.rules                     emerging.conf
```  
  
## とりあえず動かしてみる(2)  
  
ルールファイルができたのでまた動かしてみます。  
  
`em0`は`--pcap`オプションを使ったほうがよいらしいので、そうします。  
  
****  
```shell-session:
# suricata  --pcap=em0 --init-errors-fatal
22/7/2018 -- 14:27:47 - <Notice> - This is Suricata version 4.0.4 RELEASE
22/7/2018 -- 14:27:57 - <Notice> - all 3 packet processing threads, 4 management threads initialized, engine started.
```  
  
いい感じに動きました。  
  
`/var/log/suricata`にファイルができています。  
  
****  
```shell-session:
# ls /var/log/suricata/
eve.json        fast.log        stats.log       suricata.log
```  
  
# Suricataの初期設定  
  
使いながらおいおい煮詰めていくことにして、とりあえず`HOME_NET`だけ設定しました。  
  
**/usr/local/etc/suricata/suricata.yaml**  
```yaml:/usr/local/etc/suricata/suricata.yaml
vars:
  address-groups:
    HOME_NET: "[10.1.1.0/24]"
```  
  
# ログのローテート  
  
`newsyslog`任せにします。  
  
次のファイルを作りました。  
  
**/usr/local/etc/newsyslog.conf.d/suricata.conf**  
```text:/usr/local/etc/newsyslog.conf.d/suricata.conf
# logfilename          [owner:group]    mode count size when  flags [/pid_file] [sig_num]
/var/log/suricata/*.log root:wheel      644  3     100  *  GJC    /var/run/suricata.pid
```  
  
これは、3世代保存、100kb超えたらローテート、bzip2で圧縮し、ファイルがなければ作成、`/var/run/suricata.pid`ファイルに書かれているプロセスIDにSIGHUPを送る、という意味です。  
  
flagsに`G`を付けないと、ワイルドカードを使えません。  
  
  
# 起動設定  
  
OS起動時に`suricata`も起動するようにします。  
  
次のファイルに追記しました。  
  
**/etc/rc.conf.local**  
```sh:/etc/rc.conf.local
## Suricata the IDS 
##
suricata_enable="YES"	# Set to YES to enable suricata
suricata_interface="em0"	# Network interface(s) to sniff
```  
  
## 確認  
  
起動するか確認します。  
  
```shell-session
/usr/local/etc/rc.d/suricata start
```  
  
  
```shell-session
# ps axww | grep suricata
2843  -  Ss      0:07.75 /usr/local/bin/suricata -D --pcap=em0 --pidfile /var/run/suricata.pid -c /usr/local/etc/suricata/suricata.yaml
```  
  
# 設置  
  
IPSとして動かすのであれば、ブリッジにしたりしますが、今回はIDSにするだけなので、ルータかスイッチのポートをミラーリングしてパケットを拾います。  
  
自分のとこでは、YAMAHA RTX1200を使っていました。  
  
  
  
  
## YAMAHA RTX1200の場合  
  
SuricataをインストールしたマシンをLAN1のポート8に接続しました。  
監視対象はLAN1のポート5です。  
  
RTX1200へログインし、管理者モードで以下のコマンドを入力します。  
  
```
lan port-mirroring lan1 8 out 5 in 5
```  
  
ポート5への入出力双方をポート8へミラーリングします。  
  
em0から伸びたケーブルをRTX-1200のLAN1ポート8に挿します。  
  
# トラブルシューティング  
  
何も記録されいないときは、パケットが流れていないか拾えていないのかもしれません。  
  
```shell-session
tcpdump -i インターフェース名
```  
  
として、何かパケットキャプチャできるか見てみてください。何かキャプチャできるときは、suricataの設定がおかしいです。  
  
もしかすると、インターフェースが寝ているのかもしれません。  
  
```shell-session
ifconfig インターフェース名 up
```  
  
として、インターフェースを起こしてください。  
  
  
  
# 次は？  
  
何か可視化ツールをインストールしてみます。  
可視化の結果を見ながら、設定やルールを詰めていきます。  
  
  
