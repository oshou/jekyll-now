---
layout: post
title: インフラ運用コマンド チートシート(随時更新)
tags:
- os
---
インフラ運用で個人用にこれだけはすぐ叩けるように覚えておきたい必須コマンドをまとめてみました。  
随時更新
  
<!-- more -->

## 困ったら
- ヘルプを読む
  - **$ man コマンド名**
- ログを見る

## ユーザー関連
- ユーザー一覧の確認、編集
  - **$ vipw**
- グループ一覧の確認、編集
  - **$ vigr**
- 特定ユーザーの所属グループ一覧
  - **$ id ユーザー名**
- 現在ログイン中のユーザー一覧
  - **$ w**
- ログイン失敗履歴
  - **$ lastb**
- ログイン成功履歴
  - **$ last**
- ユーザーのロック操作
  - **$ passwd -S ユーザー名** //ロック状況確認
  - **$ passwd -l ユーザー名** //ロック
  - **$ passwd -u ユーザー名** //アンロック

## プロセス関連
- プロセス一覧
  - **$ ps aux -H   //プロセスを階層構造で表示**
  - **$ ps aux -L   //スレッド数、スレッドも表示**
- リソース使用率上位のプロセス一覧
  - **$ top**
    - **オプションzx押してからShift+< or Shift+>で項目ごとに降順ソート可能。**
    - **オプションfで表示項目を選択可能。Spaceで選択/非選択、ESCで決定**
    - **オプションeでメモリの単位切替可能。**
    - **プロセスの状況は、S(スリープ) / D(割込不可sleep) / T(停止中) / R(実行中) / Z(ゾンビ) / W(スワップアウト)**
- ディスクIO使用率上位のプロセス一覧
  - $ iotop
- プロセスが開いているファイルの特定
  - **$ lsof -p `pgrep プロセス名`**
  - **$ ls -l /proc/プロセスID/fd**
- プロセスの再起動
  - $ kill -SIGHUP [PID]
- プロセスの強制停止
  - $ kill -SIGKILL [PID]
- 実行中プロセスの優先度変更
  - **renice [優先度] -p [PID] ※優先度は高い順に-20(最高)～19(最低)**
- プロセスのかけ流し実行(実行前の場合)
  - **nohup [コマンド] &**
- プロセスのかけ流し実行(既に実行中の場合)
  - Ctrl+Z //コマンド中断
  - jobs //ジョブ番号の確認
  - bg %ジョブ番号 //バックグランドにジョブを回す
  - jobs //ジョブ実行中である事を確認
  - disown %(ジョブ番号) //ジョブの切り離し

## ファイル操作
- ファイル内容の編集
  - **$ vim**
- ファイル内容の表示
  - **$ less**
- しぼりこみ検索
  - **$ grep "キーワード" 検索範囲**
- ファイルの所在検索
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

## カーネル関連
- カーネルパラメータ一覧の表示
  - **$ sysctl -a**
  - http://eno0514.hatenadiary.jp/entry/20150622/1434905054
- カーネルパラメータの変更
  - **$ sysctl -w パラメータ名=値**
  - **$ sysctl -p**

## 環境変数関連
- 環境変数一覧
  - **$ env**

## ディスク関連
- ファイルシステム毎のディスク容量確認
  - **df -Th**

## ネットワーク
- IP割当状況
  - **$ ip a**
- 通信状況確認
  - **$ netstat -pantu**
  - IP別接続数の確認
    - $ netstat -pantu | awk '{print $4}' | cut -d":" -f1 | sort | uniq -c | sort -rn
- 通信経路確認
  - **$ traceroute -nI [ip address]** //ICMPの場合
  - **$ traceroute -nT -p [ポート番号] [ip address]** //ICMPの場合
- ポート疎通確認
  - **$ curl -v telnet://[ip address]:[port num]**
- HTTPリクエスト&レスポンスヘッダ表示
  - **$ curl -v [ip or URL] 2> /dev/null**
- **パケットキャプチャ**
  - **$ tcpdump -vnn -i ethxx**  //基本セット
  - **$ tcpdump -vnn -i ethxx icmp** //ICMPパケットのフィルタ
  - **$ tcpdump -vnn -i ethxx port xx**  //port指定
  - **$ tcpdump -vnn -i ethxx host xxx.xxx.xxx.xxx**  //特定ホストを抽出
  - **$ tcpdump -vnn -i ethxx not host xxx.xxx.xxx.xxx**  //特定ホストを除外
  - **Flagsの意味**
    - **.(ACK) / S(SYN) / F(FIN) / P(PUSH) / R(RST) / U(URG) / W(ECN CWR) / E(ECN-Echo)**
  - **Flagの基本的なパターン**
    - http://d.hatena.ne.jp/nattou_curry_2/20090822/1250931250
    - **TCPコネクション開始 --> (CLIENT). -> (SRV)S. -> (CLIENT). //3-way handshake**
    - **TCPコネクション切断 --> (CLIENT)F. -> (SRV)F. -> (CLIENT).**
- ルーティングテーブルの情報確認
  - $ netstat -rn

## 名前解決関連
- **$ dig [ドメイン名] @[ネームサーバー名]**
  - 例) dig example.jp @8.8.8.8

## cron関連
- 全ユーザーが持つcron一覧
  - $ cat /var/spool/cron/*
- ユーザー個別のcron
  - $ cat /var/spool/cron/ユーザー名

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
- dentryキャッシュの確認
  - 使用状況の確認
    - cat /proc/meminfo | grep Slab
    - slabtopでdentryの割合を確認
    - sar -vで内ネガティブdentryの割合を確認
  - キャッシュの飛ばし方
    - cat /proc/sys/vm/drop_caches

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
  - ネットワークトラフィック
    - sar -n DEV
  - ネットワークトラフィック(リアルタイムモニタ。指定秒単位での送受信パケット数、バイト数)
    - sar -n DEV 監視する間隔(秒)
      - 例)sar -n DEV 1


### とりあえずこれだけはyumインストールしたいツールまとめ
- vim-common
  - 万能テキストエディタ
- git
  - バージョン管理,github連携
- dstat
  - リアルタイムのパフォーマンスモニタはこれ1つだけあればOK
- tcpdump
  - 詳細なネットワークキャプチャ
- curl
  - httpリクエスト疎通確認
- jq
  - json整形用


## 参考
- 80 Linux Monitoring Tool
  - https://www.serverdensity.com/monitor/linux/how-to/
- Linuxコマンドでやりたいことをやるためのリンク集
  - http://qiita.com/ponsuke0531/items/73895dcdbb5ddd7d3331
- 原因調査用Linuxコマンド
  - http://blog.father.gedow.net/2012/10/23/linux-command-for-trouble/
