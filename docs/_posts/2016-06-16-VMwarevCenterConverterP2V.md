---
date: '2016-06-16T09:45:06+09:00'
layout: post
published: true
qiita_article_id: a080345414f4bdf98392
tags:
- vmware
- 仮想化
- P2V
title: VMware vCenter Converterを使用したP2Vに失敗するときにチェックすること
updated: '2016-06-16T09:45:06+09:00'

---
# 概要  
  
VMware vCenter Converterを使用して、物理マシンを仮想マシン化するときに失敗することがあります。  
  
例えばこんな風に  
  
```
FAILED: A file I/O error occurred while accessing
```  
  
![vcenter1.PNG](/assets/images/ce0af122-6434-0967-c242-363e61e6bbc9.png)  
  
そんなときにチェックするべきことです。  
  
# 物理マシンをchkdskする  
  
コンバートの半ばで止まるときは、物理マシンのディスクがあやしいです。  
`chkdsk /b`でディスクを検査します。  
ついでにdefragもします。  
  
# ログを見る  
  
失敗したタスクを右クリックして、ログをエクスポートします。  
大量のログがZIPファイルにまとめられてエクスポートされるので開きます。  
  
![vcenter2.PNG](/assets/images/cb32f1c8-3860-5e61-6fd2-99a1da63ea21.png)  
  
## Converter自体のログを見る  
  
最初に見るのは`vmware-converter-server-*.log`です。  
  
  
```
2016-06-16T09:05:46.747+09:00 error vmware-converter-server[04780] [Originator@6876 sub=Default] [task,350] [LRO] Unexpected Exception: converter.fault.FileIOFault
2016-06-16T09:05:46.763+09:00 info vmware-converter-server[04780] [Originator@6876 sub=Default] [task,379] [task-4] -- ERROR -- Convert: converter.fault.FileIOFault
--> (converter.fault.FileIOFault) {
-->    faultCause = (vmodl.MethodFault) null, 
-->    file = "", 
-->    msg = ""
--> }
```  
  
とくに情報ないですね。。。  
  
## Agentのログを見る  
  
worker-diagnostics-task-*-omo.zipを開きます。  
  
![vcenter3.PNG](/assets/images/d2e96cfa-109b-93af-8c44-6db23072b4a7.png)  
  
agentTask-task-*.zipを開きます。  
  
![vcenter4.PNG](/assets/images/3129425c-0b53-f052-8b70-9ff995e4daa3.png)  
  
vmware-converter-agent-*.logを開きます。  
  
```
2016-06-16T09:05:21.671+09:00 warning vmware-converter-agent[03808] [Originator@6876 sub=Default] [,0] [NFC ERROR] NfcNewAuthdConnectionEx: Failed to connect to peer. Error: Host address lookup for server vms02 failed: No such host is known
2016-06-16T09:05:21.671+09:00 info vmware-converter-agent[03808] [Originator@6876 sub=Default] Sysimgbase_DiskLib_OpenWithPassPhrase failed with 'NBD_ERR_NETWORK_CONNECT' (error code:2338)
```  
  
原因見つかりました！  
  
仮想マシンホストの名前解決ができていないみたいです。  
なんででしょな。。。  
  
# VMwareのKBを検索する  
  
https://kb.vmware.com/selfservice/microsites/searchEntry.do  
  
製品にVMware vCenter Convertgerを選び、単語「fail」で検索すると結構ヒットします。  
  
以上です。 :smile:   
