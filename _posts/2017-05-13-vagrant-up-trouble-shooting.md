---
layout: post
title: 「Your VM has become "inaccessible."」エラーでVagrant起動に失敗した場合の復旧方法
tags: 
- vagrant
---
Windows環境でVagrantのVM起動(Vagrant up)でエラーメッセージで起動に失敗した時の復旧方法をまとめました
  
<!-- more -->

## エラー内容
vagrant upすると、VMにアクセスできません、という旨のメッセージが出ていました。
VirtualBox上でもイメージが正常認識されていないようです。
>
C:\HashiCorp\Vagrant\localdev> vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
Your VM has become "inaccessible." Unfortunately, this is a critical error
with VirtualBox that Vagrant can not cleanly recover from. Please open VirtualBox
and clear out your inaccessible virtual machines or find a way to fix
them.
C:\HashiCorp\Vagrant\localdev> 


## 復旧方法
ここで、対象のVirtualBox用ディレクトリにある仮想マシンイメージファイル（.vox）を確認すると、
「xxxx.vbox-tmp」という名前に変わっていました。  
ここで以下手順を実施。
- 「xxxxx.vbox-tmp」を「xxxxxx.vbox」にリネーム
- Virtualboxを再起動
- Virtualbox上でイメージが「中断」状態で正常認識された事を確認
- この状態で再度vagrantupを実行
vanrant upで無事正常起動を確認できました。
>
C:\HashiCorp\Vagrant\localdev> vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
[default] Clearing any previously set forwarded ports...
[default] Creating shared folders metadata...
[default] Clearing any previously set network interfaces...
[default] Preparing network interfaces based on configuration...
[default] Forwarding ports...
[default] -- 22 =>  xxxx (adapter 1)
[default] Booting VM...
[default] Waiting for machine to boot. This may take a few minutes...

直前にvagarant halt実行しないまま、うっかりPCシャットダウンしてしまったので、間違いなくそれが原因だと思います。。
この時、起動時に参照するべきVirtualBox用仮想マシンイメージファイル（.vox）が正常終了していないためにtmpファイルとして別名保存されていたようです。

同様の現象の場合はお試しあれ。
