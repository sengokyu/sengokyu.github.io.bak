---
date: '2015-05-07T09:56:07+09:00'
layout: post
published: true
qiita_article_id: f4d27527bd6c98617606
tags:
- Windows
- IIS
title: '[Windows] ソース:Schannel、イベントID:36888のエラーまとめ'
updated: '2015-05-07T09:56:43+09:00'

---
IISでWebサーバを運用しているときのお話です。  
  
# :fearful:  イベントID:36888エラーがしょっちゅう記録される  
  
IISでWebサーバを運用していると、次のようなエラーがしょっちゅう記録されます。  
  
> Message:  次の致命的な警告が生成されました: 40。内部エラーの状態は 1205 です。  
  
# :anguished: どうして？？？  
  
これは、httpsポートにオイタな接続をしようとしていることが原因なのだそうです。  
  
https://support.microsoft.com/ja-jp/kb/2904855 より  
> Schannel ID: 36888 (警告10, 内部エラー 1203) エラーについては、SSL/TLS で利用されるポート (443/tcp) に対して、SSL/TLS 以外の目的で通信を受けた場合に記録されるイベントです。  
  
# :pensive: 無視してよいらしい  
  
サーバー側が原因ではないので無視してよいエラーだそうです。  
  
# :grimacing: それでも気になる  
  
SSL的などんな具合の悪いことがあったかは、メッセージ『次の致命的な警告が生成されました: **40**』の数字部分で判別できます。この数字は[RFC2246](https://www.ietf.org/rfc/rfc2246.txt) 付録A.3 Alert Messageにまとまっているそうです。  
  
以下にRFC2246から抜粋します。  
  
|警告レベル|内容|  
|:---|:--|  
|0|close_notify|  
|10|unexpected_message|  
|20|bad_record_mac|  
|21|decryption_failed|  
|22|record_overflow|  
|30|decompression_failure|  
|40|handshake_failure|  
|42|bad_certificate|  
|43|unsupported_certificate|  
|44|certificate_revoked|  
|45|certificate_expired|  
|46|certificate_unknown|  
|47|illegal_parameter|  
|48|unknown_ca|  
|49|access_denied|  
|50|decode_error|  
|51|decrypt_error|  
|60|export_restriction|  
|70|protocol_version|  
|71|insufficient_security|  
|80|internal_error|  
|90|user_canceled|  
|100|no_renegotiation|  
  
# イベントID:36874も？  
  
警告レベル40の場合は、イベントID:36874も一緒に記録されるかもです。  
  
# :nose: くさいにおいは元から断たなきゃダメ！？  
  
こんな接続をしてくるクライアントはファイアウォールでアクセス拒否したいですよね。  
  
けど、クライアントのIPアドレスを調べるにはパケットキャプチャしないといけないそうです。 :-1: :-1: :-1: :-1: :-1:   
  
他にいい方法がないか探索中です。  
