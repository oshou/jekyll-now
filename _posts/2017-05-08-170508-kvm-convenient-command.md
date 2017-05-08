---
layout: post
title: KVM運用でよく使うコマンド一覧
---
KVM運用でよく使うコマンド類の備忘録
<!-- more -->

- ホスト管理のVM一覧 
  -  virsh list --all
- ホスト管理の各VM詳細ステータス(割当メモリ等)
  - virsh dominfo [VM名]
- ホスト管理の各VM構成情報
  - virsh dumpxml [VM名]
- ホスト管理の仮想ネットワーク一覧
  - virsh net-list
- ホスト管理の物理インターフェース一覧
  - virsh iface-list --all
- ホスト管理のストレージプール一覧
  -  virsh  pool-list
- 各ストレージプールの詳細表示
  - virsh  pool-info [ストレージプール名]
- ゲストOS 起動
  - virsh start [VM名]
- ゲストOS 自動起動
  - virsh autostart [VM名]
- ゲストOS 停止
  - virsh shutdown [VM名]
- ゲストOS 自動起動停止
  - virsh autostart --disable [VM名]
- ゲストOS 設定変更
  - virsh edit [VM名]


## 参考
- 10分で始めるKVM
  - http://ymotongpoo.hatenablog.com/entry/20100806/1281084634
- KVM virshコマンド一覧
  - http://chidipy.jpn.com/topics/?p=
