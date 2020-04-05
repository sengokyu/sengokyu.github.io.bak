---
date: '2020-03-15T23:55:11+09:00'
layout: post
published: true
qiita_article_id: 4d5484aab573d3b17b1f
tags:
- Microsoft
- SQLServer
title: '[Microsoft] うちのSQL Serverがなにかおかしい - デッドロック編'
updated: '2020-03-15T23:55:11+09:00'

---
「うちのSQL Server、なんかおかしくない？」  
「デッドロックしてました！」  
というときに、その内容を確認する方法です。  
  
環境はSQL Server 2017 Standard Editionです。  
  
# デッドロック内容の見方  
  
SQL Serverのログファイルを開き、**xml_deadlock_report** 行をクリックするとDeadlockタブが表示されます。  
  
![image.png](/assets/images/788fcdef-a7c5-2e7b-8abe-710cdc7af22c.png)  
  
楕円がプロセスで、四角がリソースです。矢印がロックの所望(Request)と所有(Owner)を意味します。  
  
上の例では、3つのプロセスがお互いを待っています。  
表にするとこんな感じです。  
  
| プロセス | 待ち相手 |  
|:-:|:-:|  
| :bow_tone1: ① | :bow_tone3: ③ |  
| :bow_tone2: ② | :bow_tone1: ① |  
| :bow_tone3: ③ | :bow_tone2: ② |  
  
:bow_tone1: は :bow_tone3: が終わるのを待ち、  
:bow_tone3: は :bow_tone2: が終わるのを待ち、  
:bow_tone2: は :bow_tone1: が終わるのを待っています。  
これでは世界の終わりまで待っても終わらないですね。  
  
仕方がないので、SQL Serverはプロセスをひとつ止めてロールバックしました。  
プロセスに書かれている青い✖️印が止められたことを意味します。  
  
そのプロセスが選ばれた理由は、トランザクションログが一番少なかったから(?)かもしれません。  
  
## 矢印の見方  
  
矢印は、プロセスが所望(Request)しているロックと、所有(Owner)しているロックです。  
  
矢印の横に書いてある**S**や**SIX**というのがロックモードです。  
こんな意味だそうです。  
  
| ロックモード | 意味 |  
|:--|:--|  
| S (Shared)  | 共有ロック。読み込み時の一貫性を保つため。 |  
| U (Update)  | 更新ロック。？  |  
| X (eXclusive) | 排他ロック。他トランザクションを完全排除。   |  
| IS (Intent Shared) | インテント共有ロック。？ |  
| IX (Intent eXclusive) | インテント排他ロック。？ |  
| SIX (Shared with Intent eXclusive) | 共有インテント排他ロック。？  |  
  
更新ロックはなんであるんだろ？排他ロックでよくね？  
と思いましたが、排他ロックは他のロックが存在すると獲得できないために必要だそうです。  
  
見慣れないのがインテントロックです。これはパフォーマンス向上のための機能だそうです。  
後のほうに、なんとなくわかったことを書いてみました。  
  
  
## プロセスの見方  
  
マウスオーバーすると、実行したSQLが表示されます。  
よく見たいときは、Detailsタブを開いてXMLをダブルクリックします。  
  
表示されている各項目はこんな意味だそうです。  
  
| 項目 | 意味 |  
|:-:|:--|  
| Server process Id  | プロセスを識別する一意のIDです  |  
| Server batch Id | バッチ実行されているときはそのIDです。バッチでないときは0です。  |  
| Execution context Id | 他のServer processに紐づくサブスレッドの場合はそのIDです。そうでなければ0です。  |  
| Deadlock priority  | デッドロック優先度です。 低:-5 通常:0 高:5  |  
| Log used  | 使用したトランザクションログ領域のサイズです。  |  
| Owner Id | トランザクションのIDです。  
| Transaction descriptor | なんだかわかりません。トランザクションの場合は、状況取得のためのポインタだそうです。  
  
  
### プロセスが待つ理由  
  
そもそもなぜ待つのかというと、別のプロセスによるロックが存在しているからです。  
  
| 獲得したいロック | 獲得を妨げるロック |  
|:-:|:-:|  
| S  | X, IX, SIX |   
| U  | U, X, IX, SIX |   
| X  | S, U, X, IS, IX, SIX  |   
| IS  | X  |   
| IX  | S, U, X, SIX  |   
| SIX  | S, U, X, IX, SIX  |  
  
先の例ですと、共有インテント排他ロックが存在するので、共有ロックを獲得できなくて待っています。  
  
  
## リソースの見方  
  
リソースにはロック対象とロック粒度が書かれています。  
リソースの上辺に書かれているのがロック粒度です。ロック粒度というのは、ロックする範囲を意味します。  
  
| ロック粒度 | 意味 |  
|:-:|:--|  
| RID  | テーブルの行単位でロックします。一番小さい単位です。  |  
| PAGE  | 行等が含まれるページ（SQL Serverにおけるデータの格納単位）単位でロックします。  |  
| TABLE  | テーブル全体をロックします。  |  
| DATABASE  | データベース全体をロックします。  |  
  
RIDロックで十分なのにPAGEロックになるのは、性能のためだそうです。  
  
RIDロックやPAGEロックが多くなると判断されると、自動的にTABLEロックに切り替わります。ロックエスカレーションというそうです。  
  
  
  
  
# インテントロックとは？  
  
インテントロックとは、すでに実ロックを所有していることを示すか、ロックの獲得を妨げるためのものです。  
  
例えば、RIDレベルのロックを所有しているとき、より大きい粒度のPAGEやTABLEのインテントロックを所有します。  
こうすることで、他プロセスがロックを所望したときに、小さな粒度でロックの有無を調べる必要がなくなります。  
  
![image.png](/assets/images/83231198-1ca5-3df1-58c8-048fd29e7d5b.png)  
  
## インテント共有ロック (IS)  
  
ロック対象より小さい粒度のリソースの一部について、共有ロック獲得を妨げます。  
  
## インテント排他ロック (IX)  
  
ロック対象より小さい粒度のリソースの一部について、排他ロック獲得を妨げます。  
  
## 共有インテント排他ロック (SIX)  
  
ロック対象より小さい粒度のリソース**すべて**の共有ロック獲得を妨げます。  
ロック対象より小さい粒度のリソースの一部をインテント排他ロックしています。  
  
共有インテント排他ロックは、こんな感じのSELECT〜UPDATEの流れで獲得されるそうです。  
  
1. SELECTするとき、行の共有ロックを獲得  
2. 行が含まれるページまたはテーブルのインテント共有ロックを獲得  
3. UPDATEするとき、ページまたはテーブルのインテント排他ロックを要求  
4. 同一リソースに複数のロックは所有できないので、共有インテントロックを獲得  
5. 更新対象行の排他ロックを獲得  
  
  
# ところで、共有インテント排他ロックは同時に存在できないはずじゃ？  
  
先のグラフをよく見ると、同じページIDに対してSIXロックを獲得しています。  
  
![image.png](/assets/images/a3537d5a-fe81-8182-5c5b-f0aaf3600c59.png)  
  
どういうこと・・・？  
  
  
# リンク  
  
  
* [Deadlock Analysis (Examples)](https://docs.microsoft.com/en-us/archive/blogs/saponsqlserver/deadlock-analysis-examples)  
* [Understanding SQL Server Lock Modes](https://logicalread.com/2013/09/11/sql-server-lock-modes-mc03/#.XmB4Dumxhpg)  
* [Transaction Locking and Row Versioning Guide](https://docs.microsoft.com/en-us/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide?view=sql-server-ver15)  
* [  
Why do we need Intent Locks in SQL Server?](https://www.sqlpassion.at/archive/2016/05/16/why-do-we-need-intent-locks-in-sql-server/)  
* [SQL Server でロックのエスカレーションが原因で発生するブロッキング問題を解決する方法](https://support.microsoft.com/ja-jp/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in)  
