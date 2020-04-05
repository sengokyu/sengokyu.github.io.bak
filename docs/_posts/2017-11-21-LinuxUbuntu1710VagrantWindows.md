---
date: '2017-11-21T14:19:38+09:00'
layout: post
published: true
qiita_article_id: 8572de2146994fac2aeb
tags:
- Windows
- Ubuntu
- Vagrant
- ubuntu17.10
title: '[Linux] Ubuntu 17.10ホストにVagrantを入れてWindowsを動かす'
updated: '2019-03-20T12:12:32+09:00'

---
# はじめに  
  
Windows/macOSホストにVagrantを入れて、仮想環境としてUbuntuを動かす話ではなく、UbuntuホストでVagrantを動かす話です。  
  
# インストール  
  
```
sudo apt-get install virtualbox
```  
  
レポジトリにあるVagrantは1.9.1なので、最新のものをダウンロードしてインストールします。  
https://www.vagrantup.com/downloads.html  
  
```
sudo dpkg -i vagrant_2.0.1_x86_64.deb
```  
  
  
  
## Safe Bootを無効にする  
  
UEFIシステムだと、virtualboxをインストールしたときにsafe bootを無効にするよう促されます。指示に従い無効にします。  
  
  
## Virtualbox起動時に表示される警告  
  
virtualboxを起動したとき、下記のメッセージが表示されるかもしれません。  
  
```
WARNING: The character device /dev/vboxdrv does not exist.                                                                                                                          
         Please install the virtualbox-dkms package and the appropriate                                                                                                             
         headers, most likely linux-headers-generic.                                                                                                                                
                                                                                                                                                                                    
         You will not be able to start VMs until this problem is fixed.       
```  
  
いったん`virtualox-dkms`を消し、再インストールすれば解消します。  
  
```
sudo apt-get remove virtualbox-dkms
sudo apt-get install virtualbox-dkms
```  
  
## お試し  
  
`vagrant`コマンドを実行します。  
  
```shell-session
% vagrant
Usage: vagrant [options] <command> [<args>]  

    -v, --version                    Print the version and exit.                          
    -h, --help                       Print this help.                                     

Common commands:                             
     box             manages boxes: installation, removal, etc.                           
     destroy         stops and deletes all trac
...
```  
こんな感じにメッセージが表示されればOKです。  
  
  
# Edge on Windows 10を使う  
  
とりあえずこちらを使ってみます。  
https://app.vagrantup.com/Microsoft/boxes/EdgeOnWindows10  
  
`vagrant init`して  
  
```shell-session
% mkdir edge-on-windows
% cd edge-on-windows
% vagrant init Microsoft/EdgeOnWindows10
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```  
  
`vagrant up`します。  
  
```
% vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'Microsoft/EdgeOnWindows10'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'Microsoft/EdgeOnWindows10' is up to date...
==> default: Setting the name of the VM: windows-edge_default_1511237915037_52865
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Authentication failure. Retrying...

```  
  
sshしようとして失敗しています。何か足りないようです。  
  
  
  
## Vagrantfileを編集  
  
ぐぐると https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13592617/ が見つかりました。  
  
Vagrantfileに追記しました。  
  
**Vagrantfile**  
```rb:Vagrantfile
  config.vm.guest = :windows
  config.vm.communicator = "winrm"
  config.winrm.username = "IEUser"
  config.winrm.password = "Passw0rd!"
```  
  
内容確認します。  
  
```
vagrant validate
```  
  
  
  
## リトライ、そしてまたエラー  
  
まだ何か足りないようです。  
  
  
```shell-session
% vagrant reload
==> default: Attempting graceful shutdown of VM...
    default: Guest communication could not be established! This is usually because
    default: SSH is not running, the authentication information was changed,
    default: or some other networking issue. Vagrant will force halt, if
    default: capable.
==> default: Forcing shutdown of VM...
==> default: Checking if box 'Microsoft/EdgeOnWindows10' is up to date...
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 5985 (guest) => 55985 (host) (adapter 1)
    default: 5986 (guest) => 55986 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: WinRM address: 127.0.0.1:55985
    default: WinRM username: IEUser
    default: WinRM execution_time_limit: PT2H
    default: WinRM transport: negotiate
Timed out while waiting for the machine to boot. This means that
Vagrant was unable to communicate with the guest machine within
the configured ("config.vm.boot_timeout" value) time period.

If you look above, you should be able to see the error(s) that
Vagrant had when attempting to connect to the machine. These errors
are usually good hints as to what may be wrong.

If you're using a custom box, make sure that networking is properly
working and you're able to connect to the machine. It is a common
problem that networking isn't setup properly in these boxes.
Verify that authentication configurations are also setup properly,
as well.

If the box appears to be booting properly, you may want to increase
the timeout ("config.vm.boot_timeout") value.
```  
  
## Windows側の設定を変更する  
  
Remote Managementが使えるようになっていないのと、ファイアウォールが原因でした。  
  
PowerShellを管理者権限で起動します。  
  
```
Get-NetConnectionProfile | Set-NetConnectionProfile -NetworkCategory Private
winrm quickconfig
```  
  
![image.png](/assets/images/46a438a0-7b57-2c38-843b-1d022b5bf6b0.png)  
  
  
## これでOK？  
  
```shell-session
% vagrant reload       
==> default: Attempting graceful shutdown of VM...
==> default: Checking if box 'Microsoft/EdgeOnWindows10' is up to date...
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 5985 (guest) => 55985 (host) (adapter 1)
    default: 5986 (guest) => 55986 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: WinRM address: 127.0.0.1:55985
    default: WinRM username: IEUser
    default: WinRM execution_time_limit: PT2H
    default: WinRM transport: negotiate
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default: 
    default: Guest Additions Version: 5.0.2
    default: VirtualBox Version: 5.1
==> default: Mounting shared folders...
    default: /vagrant => /localhome/Vagrant/windows-edge
```  
  
## せっかくなので、Box作成  
  
```
vagrant package
```  
  
これで他でも使えるかな。  
  
  
# 環境  
  
設定した環境です。  
  
- Ubuntu 17.10  
- VirtualBox 5.1.30  
- Vagrant 2.0.1  
  
  
  
  
  
  
