---
layout: post
title: KVMの運用でよく使うコマンド一覧
---
KVMの基本的なコマンドメモ
<!-- more -->

- 仮想マシンの一覧表示
  -  virsh list --all
- 各仮想マシンの構成情報一式の表示
  - virsh dumpxml [仮想マシン名]
- 各仮想マシンの詳細表示
  - virsh dominfo [仮想マシン名]
- ストレージプールの一覧表示
  -  virsh  pool-list
- 各ストレージプールの詳細表示
  - virsh  pool-info [ストレージプール名]
- 仮想ネットワークの表示
  - virsh net-list
- ゲスト起動
  - virsh start testvm
- ゲスト自動起動
  - virsh autostart testvm
- ゲスト自動起動停止
  - virsh autostart --disable testvm
- ゲストOSの設定の変更
  - virsh edit testvm

## 参考
- 10分で始めるKVM
  - http://ymotongpoo.hatenablog.com/entry/20100806/1281084634
- KVM virshコマンド一覧
  - http://chidipy.jpn.com/topics/?p=
