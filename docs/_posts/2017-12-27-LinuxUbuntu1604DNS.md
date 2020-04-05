---
date: '2017-12-27T15:02:58+09:00'
layout: post
published: true
qiita_article_id: 34dd294a1604d480458c
tags:
- Windows
- Linux
- Ubuntu
- dns
- ActiveDirectory
title: '[Linux] Ubuntu 16.04でDNSレコードを動的に更新する'
updated: '2018-09-26T11:32:49+09:00'

---
今まではネームサーバへのレコード登録をDHCPサーバに任せていましたが、`nsupdate`コマンドを使って積極的に登録するようにしました。  
  
  
# 環境  
  
- DHCPクライアント: Ubuntu 16.04  
- DHCPサーバ: YAMAHA RTX1200  
- ネームサーバ: Windows Server 2012 R2  
  
# スクリプト  
  
dhclient enter hookの中で、`nsupdate`を実行するようにします。  
  
以下のスクリプトを`/etc/dhcp/dhclient-enter-hooks.d/`に置いてください。  
  
**/etc/dhcp/dhclient-enter-hooks.d/nsupdate**  
```sh:/etc/dhcp/dhclient-enter-hooks.d/nsupdate
#
# Script fragment to make dhclient update name server records.
#

# set to 'yes', temporary file will not remove
DEBUG="no"

ip_to_ptr() {
	local ip
	local p
	local ptr

	ip=$(echo $1 | tr '.' ' ')
	ptr="in-addr.arpa."

	for p in $ip; do
		ptr="${p}.${ptr}"
	done
	
	echo $ptr
}

echo_update() {
	echo update $*
	echo
}

del_ptr_record() {
	if [ "$1" != "" ]; then
		echo_update del $(ip_to_ptr $1) PTR
	fi
}

del_a_record() {
	if [ "$1" != "" ]; then
		echo_update del $1 A
	fi
}

add_ptr_record() {
	if [ "$1" != "" ] && [ "$2" != "" ]; then
		echo_update add $(ip_to_ptr $2) 1200 PTR ${1}
	fi
}

add_a_record() {
	if [ "$1" != "" ] && [ "$2" != "" ]; then
		echo_update add ${1} 1200 A ${2}
	fi
}

name_server() {
	local n

	for n in $1; do
		echo "server ${n}"
		echo
		break;
	done
}

exec_nsupdate() {
	if [ "$DEBUG" != "yes" ]; then
		nsupdate $1
		rm -f $1
	else
		echo '-------------------' >> /tmp/nsupdate.debug
		cat $1 >> /tmp/nsupdate.debug
		echo '-------------------' >> /tmp/nsupdate.debug
		nsupdate -d $1 >> /tmp/nsupdate.debug 2>&1
	fi
}

go_nsupdate() {
	local host
	local tmp

	host=$(hostname)
	tmp="/tmp/nsupdate.$$"

	case "$reason" in
	  BOUND|RENEW|REBIND|REBOOT|TIMEOUT)
		name_server $new_domain_name_servers	> ${tmp}
		del_ptr_record $old_ip_address		>> ${tmp}
		del_a_record ${host}.${old_domain_name}	>> ${tmp}
		add_ptr_record ${host}.${new_domain_name}. $new_ip_address	>> ${tmp}
		add_a_record ${host}.${new_domain_name} $new_ip_address	>> ${tmp}

		echo "send" >> ${tmp}

		exec_nsupdate $tmp
		;;
	  EXPIRE|FAIL|RELEASE|STOP)
		name_server $old_domain_name_servers	> ${tmp}
		del_ptr_record $old_ip_address		>> ${tmp}
		del_a_record ${host}.${old_domain_name}	>> ${tmp}

		echo "send" >> ${tmp}

		exec_nsupdate $tmp
		;;
	esac
}

if [ -x /usr/bin/nsupdate ]; then
	go_nsupdate
fi
```  
  
うまくいかないときは、ファイルを書き換えて`DEBUG="yes"`としてください。  
`/tmp/nsupdate.debug`というファイルを出力します。  
