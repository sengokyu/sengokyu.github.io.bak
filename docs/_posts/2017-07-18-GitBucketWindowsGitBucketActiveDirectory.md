---
date: '2017-07-18T13:43:57+09:00'
layout: post
published: true
qiita_article_id: 6362c9b663b906c412bf
tags:
- Windows
- GitBucket
- ActiveDirectory
title: '[GitBucket][Windows] GitBucketとActive Directoryの連携'
updated: '2017-07-18T15:47:53+09:00'

---
GitBucketに、Active Directoryで使用しているログインIDとパスワードでログインできるようにします。  
  
# 環境  
  
- GitBucket環境  
 - Ubuntu 16.04  
 - GitBucket 4.14.1  
 - OpenJDK Runtime 1.8.0_131  
- Active Directory環境  
 - Microsoft Windows Server 2012 R2  
  
  
# 設定  
  
rootユーザでGitBucketへログインし、メニューから[System Settings]を辿ります。  
  
Authenticationの項目にLDAPがあるので有効にします。  
  
各項目の設定値は次のとおりです。  
  
<dl>  
<dt>LDAP host</dt>  
<dd>ドメインコントローラホスト名</dd>  
<dt>LDAP port</dt>  
<dd>389</dd>  
<dt>Bind DN</dt>  
<dd>ActiveDirectoryにログインできるユーザのDistinguished Name 例）<code>cn=administrator,ou=users,dc=example,dc=com </dd>  
<dt>Bind password</dt>  
<dd>上記ユーザのパスワード</dd>  
<dt>Base DN</dt>  
<dd>ユーザを探しに行くツリーの根本 例）<code>dc=example,dc=com</code></dd>  
<dt>User name attribute</dt>  
<dd><code>sAMAAccountName</code></dd>  
<dt>Additional filter condition</dt>  
<dd>（空）</dd>  
<dt>Full name attribute</dt>  
<dd><code>displayName</code></dd>  
<dt>Mail address attribute</dt>  
<dd><code>mail</code></dd>  
<dt>Enable TLS</dt>  
<dd>No</dd>  
<dt>Enable SSL</dt>  
<dd>No</d>  
<dt>Keystore</dt>  
<dd>（空）</d>  
</dl>  
  
# はまりどころ  
  
Active Directory側がちゃんと整えられていないとログインできないです。  
  
- ユーザのcommon nameは日本語にせず、英数字にする  
- mail属性に値を入れる  
