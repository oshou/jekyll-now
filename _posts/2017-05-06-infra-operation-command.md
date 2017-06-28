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
- ユーザー一覧の確認、編集
  - **$ vipw**
- グループ一覧の確認、編集
  - **$ vigr**
- 現在ログイン中のユーザー一覧
  - **$ w**
- ログイン失敗履歴
  - **$ lastb**
- ログイン成功履歴
  - **$ last**

## プロセス関連
- プロセス一覧
  - **$ ps auxww**
- リソース使用率上位のプロセス一覧
  - **$ top**
  - オプションzx押してからShift+< or Shift+>で項目ごとに降順ソート可能。
  - オプション-fで表示項目を選択可能。
- ディスクIO使用率上位のプロセス一覧
  - $ iotop
- プロセスが開いているファイルの特定
  - **$ lsof -p `pgrep プロセス名`**
  - **$ ls -l /proc/プロセスID/fd**
- プロセスの強制停止
  - $ kill -SIGKILL [PID]
- プロセスの再起動
  - $ kill -SIGHUP [PID]
- プロセスの退勤時かけ流し実行(実行前の場合)
  - nohup コマンド & 
- プロセスの退勤時かけ流し実行(既に実行中の場合)
  - Ctrl+Z //コマンド中断
  - bg ジョブ番号 //バックグランドにジョブを回す
  - jobs ジョブ番号 //ジョブの確認
  - disown %(ジョブ番号) //ジョブの切り離し

## カーネル関連
- カーネルパラメータ一覧
  - **$ sysctl -a**
  - http://eno0514.hatenadiary.jp/entry/20150622/1434905054

## ファイル操作
- ファイル内容の編集
  - **$ vim**
- ファイル内容の表示
  - **$ less**
- ファイルの場所検索
  - $ locate ファイル名
- ファイル比較
  - $ diff {ファイル名1} {ファイル名2}
  - $ diff -r {ディレクトリパス1} {ディレクトリパス2}
- ファイル文字コード表示、変更
  - $ vimで開いて「:set fenc?」       //確認の場合
  - $ vimで開いて「:set fenc=utf-8」  //変更の場合
- 改行コード表示、変更
  - $ vimで開いて「:set ff?」         //確認の場合
  - $ vimで開いて「:set ff=unix」     //変更の場合

## ネットワーク
- IP割当状況
  - **$ ip a**
  - **$ ifconfig**
- 通信状況
  - **$ netstat -pantu**
- ポート疎通確認
  - **$ curl -v telnet://<ip address>:<port num>**
- 通信経路確認
  - **$ traceroute -nI [ip address]** //ICMPの場合
  - **$ traceroute -nT -p [ポート番号] [ip address]** //ICMPの場合
- **パケットキャプチャ**
  - **$ tcpdump -nn -i ethxx**  //基本セット
  - **$ tcpdump -nn -i ethxx icmp** //ICMPパケットのフィルタ
  - **$ tcpdump -nn -i ethxx port xx**  //port指定
  - **$ tcpdump -nn -i ethxx host xxx.xxx.xxx.xxx**  //特定ホストを抽出
  - **$ tcpdump -nn -i ethxx src host xxx.xxx.xxx.xxx**  //特定ホストを除外
  - Flagsの意味
    - **.(ACK) / S(SYN) / F(FIN) / P(PUSH) / R(RST) / U(URG) / W(ECN CWR) / E(ECN-Echo)**
  - 基本的なパターン  
    - http://d.hatena.ne.jp/nattou_curry_2/20090822/1250931250
    - TCPコネクション開始 --> (CLI). -> (SRV)S. -> (CLI). //3-way handshake
    - TCPコネクション切断 --> (CLI)F. -> (SRV)F. -> (CLI).
- ルーティングテーブルの情報確認
  - $ netstat -rn
- IP別接続数の確認
  - $ netstat -pantu | awk '{print $4}' | cut -d":" -f1 | sort | uniq -c | sort -rn

## cron関連
- 全ユーザーが持つcron一覧
  - $ cat /var/spool/cron/*
- ユーザー個別のcron
  - $ cat /var/spool/cron/ユーザー名

## 名前解決関連
- **$ dig [ドメイン名] @[ネームサーバー名]**

## パフォーマンス
**リアルタイム負荷は何はなくともvmstat。出来ればdstatをインストール。  
過去履歴はsarで確認。  
負荷の高いプロセスの特定はtop、iotop。**

### システム全体の負荷(リアルタイム)
- **dstat**
  - **$ dstat -tlcmsdrn**
- **$ vmstat 2**
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

### システム全体の負荷(過去履歴)
- **$ sar**
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


### これだけは入れておきたいツールまとめ
- tcpdump
- fio

## 参考
- 80 Linux Monitoring Tool
  - https://www.serverdensity.com/monitor/linux/how-to/
- Linuxコマンドでやりたいことをやるためのリンク集
  - http://qiita.com/ponsuke0531/items/73895dcdbb5ddd7d3331
- 原因調査用Linuxコマンド
  - http://blog.father.gedow.net/2012/10/23/linux-command-for-trouble/
