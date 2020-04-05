---
date: '2016-09-17T20:17:51+09:00'
layout: post
published: true
qiita_article_id: b93c57cff7245d7070fc
tags:
- Redmine
- FreeBSD
title: '[FreeBSD][Redmine] FreeBSD用Redmine起動rcスクリプト'
updated: '2016-09-17T20:17:51+09:00'

---
軽く検索したけれど、見つからなかったので備忘録です。  
portsは依存関係でだいぶ大変なことになってしまったので、tarボールから入れました。 :sweat_smile:   
  
# 前提  
  
- FreeBSD 10R  
- Redmine 3.3  
- Webサーバはthin  
  
# rcスクリプト  
  
**/usr/local/etc/rc.d/redmine**  
```bash:/usr/local/etc/rc.d/redmine
# !/bin/sh

. /etc/rc.subr

name="redmine"

redmine_dir="/usr/local/redmine"

pidfile="/var/run/redmine.pid"
procname="ruby"

command="${redmine_dir}/bin/rails"
command_args="server thin"
command_args="${command_args} --daemon"
command_args="${command_args} --binding=0.0.0.0"
command_args="${command_args} --pid=${pidfile}"
command_args="${command_args} --environment=production"

load_rc_config $name
run_rc_command "$1"
```  
