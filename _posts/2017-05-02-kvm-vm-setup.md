---
layout: post
title: CentOS7でKVM仮想マシン環境を作成してみた
---
KVM仮想化のゲストOS作成手順をまとめてみた
<!-- more -->

## 概要
### KVMとは
=Kernel-based Virtual Machineの略。  
仮想化は大きく以下3種類がありますが、今回使うKVMは「ホストOS型」と呼ばれるものです。
- ホストOS型
  - **ホストOSの上でゲストOSが稼働する。**
  - QEMU,KVM,VirtualBox等
- HyperVisor型
  - 低レイヤでHyperVisorと呼ばれる仮想化実行環境を構成し、ゲストOSが稼働する。
  - オーバーヘッドが低い
  - ESXi,Xen,Hyper-V等
- コンテナ型
  - コンテナと呼ばれるアプリ実行環境を作成する。
  - Docker等

### KVM環境構築の流れ
KVMはホストOSの上に、複数のゲストOSが稼働します。  
まずはホストOSでのKVM環境の作成を行います。  
その上で、ゲストOS用のイメージ容量確保しゲストOSのVMを作成します。  
この時OSオリジナルイメージからのインストール&初期設定が必要ですが、  
この作業を「KickStart」という仕組みを使って自動化してみました。

※今回は以下環境で作成  
KVMホストOS：CentOS7+KVM環境  
KVMゲストOS：CentOS7  


## 実際つくってみる
### ホストOS上でKVM環境を作成
必要ソフトウェア一式(KVM,Kickstart)をインストール
```
yum -y install \
  libguestfs \
  libvirt \
  libvirt-client \
  python-virtinst \
  qemu-kvm \
  virt-manager \
  virt-top \
  virt-viewer \
  virt-who \
  virt-install \
  bridge-utils \
  pykickstart
```
自動起動設定
```
systemctl start libvirted
```
起動
```
systemctl enable libvirted
```

### ゲストOS通信用のネットワーク設定
ゲストOSが外部と通信できるようにBridgeインターフェースの設定を行います。  
具体的にはBridgeインターフェースを新規作成し、ホストOSの外部通信用の物理NIC資源と紐付けます。
```
(外部と通信している物理NICがeth0の場合)
Bridgeインターフェースの定義
$ cp -rp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-br0
$ vim /etc/sysconfig/network-scripts/ifcfg-br0
DEVICE=br0      <--ここだけ変更
TYPE=Bridge     <--ここだけ変更
#HWADDR=xxxxx   <--ここだけ変更(コメントアウトする)

物理NIC資源をBridgeインターフェースに紐付け
$ vim /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
TYPE=Ethernet
ONBOOT=yes
BRIDGE=br0

設定反映のためのnetworkサービス再起動
$ systemctl restart network

br0の認識,IPアドレス割り当てを確認
$ ifconfig

br0に紐づくインターフェースとして物理NIC資源eth0が表示されている事を確認
$ brctl show
```

### ゲストOSに使うISOファイルの準備
今回はCentOS7のプレーンイメージなので公式からISOデータをダウンロード
https://www.centos.org/download/
```
cd /tmp
curl -Lo http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-DVD-1611.iso
```

### ゲストOSのVMディスク領域の確保
まずはゲストOS用のVMディスク容量を確保する。この時点では容量確保のみで中身は空の状態。
ディスクフォーマットは、raw(sparce)、raw(non-sparce)、qcow2の3種類があるのでいずれかを選択。  
各フォーマットの詳しい説明は以下。  
http://d.hatena.ne.jp/kt_hiro/20120819/1345351773  
作成済イメージのディスクフォーマットの調べ方は以下。  
http://d.hatena.ne.jp/kt_hiro/20120819/1345351773  
今回はサイズ300GB、ディスクフォーマットraw(sparce)で作成。
```
cd /path/to
qemu-img create -f raw centos7-test.img 300G
```

### KickStart(OSインストール&初期設定自動化機能)用の設定
KickStartという仕組みを使ってOSインストール&初期設定を行います。  
VM作成時に読み込ませるために、主に言語やキーボード、パーティション構成、セキュリティ設定等を1つの設定ファイルにまとめます。
```
$ vim centos7-test.ks.cfg

#version=DEVEL

# Set System Authorization
auth --enableshadow --passalgo=sha512

# Install start
install

# Use CD-ROM Installation Media
cdrom

# Set Text Install Mode
text
skipx

# Set Non-interactive Mode
cmdline

# Run the Setup Agent on first boot
firstboot --disabled

# Set Locale
lang ja_JP.UTF-8
keyboard --vckeymap=jp106 --xlayouts=jp
timezone Asia/Tokyo

# Set Bootloader
zerombr
bootloader --location=mbr

# Delete existing partition & create new partition
clearpart --all --initlabel
part / --fstype=xfs --grow --size=1 --asprimary --label=root

# Set root password
rootpw --plaintext password

# Set Network
network --activate --bootproto=dhcp --noipv6

# Set SELinux
selinux --disabled

# Set Firewall
firewall --disabled

# Reboot
reboot

%packages
%end

作成したファイルの検証
$ ksvalidator centos7-test.ks.cfg
```


### 仮想マシンの作成
仮想マシンの作成はvirt-install一発で実行可能。
```
virt-install \
--name centos7-template \
--virt-type kvm \
--arch x86_64 \
--vcpus=2 \
--ram=4096 \
--disk /path/to/centos7-test.img \
--network bridge=br0 \
--os-type=linux \
--boot=hd \
--location=/data/iso/CentOS-7-x86_64-DVD-1611.iso \
--hvm \
--accelerate \
--graphics none
```

各オプションの意味は以下
- --name
  - VMの名前
- --virt-type
  - VM仮想化の種類。kvm,qemu,xenのいずれかから選択。
- --vcpus
  - CPU数
- --ram
  - メモリ容量(MB)を指定。
- --disk
  - 前手順で作成したディスクイメージを指定
- --network
  - 前手順で定義したブリッジインターフェースを指定
- --os-type
  - OS種別。windows/linux/unix/otherのいずれかを選択
- --boot
  - ブートデバイスの指定。通常はディスク起動なのでhdを指定
- --location
  - インストールソースの指定
- --hvm
  - 完全に仮想化されたゲストとしてゲストサーバを作成


## KVMの運用コマンド
- ホスト管理のVM一覧
  - virsh list --all
- VMの詳細表示
  - virsh dominfo [VM名]
- VMの構成情報の出力
  - virsh dumpxml [VM名]
- ホスト管理のストレージプール一覧
  - virsh pool-list
- ホスト管理の仮想ネットワーク一覧
  - virsh net-list
- ホスト管理の物理インターフェース一覧
  - virsh iface-list --all

## 参考
- RHEL6 KVMによる仮想化
  - https://www.slideshare.net/moriwaka/rhel6-kvm
- [KVM] virshコマンドまとめ（おまけつき）
  - http://chidipy.jpn.com/topics/?p=272
- [KVM] ゲストOS作成方法によってパフォーマンスが違う件 #2
  - https://nullpopopo.blogcube.info/2012/01/kvm-performance2.html
- 10分で始めるKVM
  - http://ymotongpoo.hatenablog.com/entry/20100806/1281084634
