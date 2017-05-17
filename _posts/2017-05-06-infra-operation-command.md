---
layout: post
title: インフラ運用コマンド チートシート(随時更新)
tags: 
- os
---
インフラ運用でこれはすぐ叩けるように覚えておきたい、というコマンドをまとめてみた  
随時更新
  
<!-- more -->

## ユーザー関連
- 現在ログイン中のユーザー一覧
  - $ w
- ログイン失敗履歴
  - $ lastb
- ログイン成功履歴
  - $ last
- ユーザー一覧の確認、編集
  - $ vipw
- グループ一覧の確認、編集
  - $ vigr

## プロセス関連
- プロセス一覧のリアルタイム表示
  - $ top
  - オプションzx押してからShift+< or Shift+>で項目ごとに降順ソート可能。
  - オプション-fで表示項目を選択可能。
- プロセス一覧のリアルタイム表示
  - $ htop
- プロセス一覧
  - $ ps auxww
- プロセス検索(プロセスIDを取得)
  - $ pgrep キーワード

## ファイル操作
- ファイル内容の編集
  - $ vim
- ファイル内容の表示
  - $ less
- ファイル比較
  - $ diff {ファイル名1} {ファイル名2}
  - $ diff -r {ディレクトリパス1} {ディレクトリパス2}
- ファイル文字コード確認
  - $ nkf -g ファイル名

## ネットワーク
- IP情報の確認
  - $ ifconfig
  - $ ip addr show

## パフォーマンス
**とりあえずvmstat覚えておく。可能ならdstatをインストールする**

### システム全体の負荷
- **$ vmstat 2**
- vmstatの見方
  - procs
    - r: 実行可能なプロセス数
    - b: 不可避なIO待ちプロセス数
  - memory
    - swpd: スワップされたメモリ
    - free: 空きメモリ
    - buffer: バッファキャッシュ
    - cache: ページキャッシュ
  - swap
    - si: スワップイン
    - so: スワップアウト
  - io
    - bi: ブロックイン(ブロックデバイスからの読込み)
    - bo: ブロックアウト(ブロックデバイスへの書込み)
  - System
    - in: 1秒間の割り込み回数(タイマー含む)
    - cs: 1秒間のコンテキストスイッチ回数
  - cpu
    - us: ユーザーCPU時間
    - sy: システムCPU時間
    - id: アイドル時間
    - wa: IO待ち時間
    - st: 仮想マシンに盗まれた(steal)時間

### 過去の負荷履歴
- $ sar
  - ロードアベレージ
    - sar -q
  - メモリ使用状況
    - sar -r
  - スワップ頻度
    - sar -W
  - ディスクIO使用状況
    - sar -b
  - ネットワークトラフィック(送受信パケット数、バイト数)
    - sar -n DEV 1 20

### CPUコア毎の負荷
- $ mpstat -P ALL
- I/Oの統計情報の確認
  - iostat
    - 各デバイス毎のI/O性能を確認可能
    - 詳しいオプションは以下参照  
    https://blogs.oracle.com/yappri/entry/iostat

## 参考
- 80 Linux Monitoring Tool
  - https://www.serverdensity.com/monitor/linux/how-to/
- Linuxコマンドでやりたいことをやるためのリンク集
  - http://qiita.com/ponsuke0531/items/73895dcdbb5ddd7d3331
