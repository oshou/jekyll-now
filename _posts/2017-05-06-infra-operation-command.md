---
layout: post
title: インフラ運用コマンド チートシート(随時更新)
tags:
- os
---
インフラ運用で個人用にこれだけはすぐ叩けるように覚えておきたい必須コマンドをまとめてみました。
随時更新

<!-- more -->

## 始める前に
- **RTFM (Read The Fucking Manual) マニュアル、ヘルプを読む**
  - **$ man [コマンド名]**
  - **$ man [セクション番号] [コマンド名]**
  - **セクション番号は以下でコマンドはいずれか、もしくは複数のセクションに所属する。**
    1. ユーザーコマンド(ls, mv等)
    2. システムコール(fork等)
    3. ライブラリ関数
    4. デバイスファイル等(zero,null等、/dev以下のすべてのファイル)
    5. ファイルフォーマット(ext4等)
    6. ゲーム(sl等)
    7. 規格等
    8. システム管理用コマンド(visudo, reboot, shutdown等)
- **ログを見る**
- **サーバに負荷を書ける時は実行優先度を下げる**
  - **$ ionice -c 2 -n 7 nice -n 19**

## カーネル関連
- **カーネルバージョン,CPUアーキテクチャの確認**
  - **$ cat /proc/version**
- **OSバージョンの確認**
  - **$ cat /etc/system-release**
- **カーネルモジュール一覧**
  - **$ lsmod**
- **特定モジュールの詳細情報取得**
  - **$ modinfo [モジュール名]**
- **カーネルモジュールの追加、削除(依存性考慮してくれる)**
  - **$ modprobe [モジュール名]**      //追加
  - **$ modprobe -r [モジュール名]**   //削除
- **カーネルパラメータ一覧の表示**
  - **$ sysctl -a**
  - http://eno0514.hatenadiary.jp/entry/20150622/1434905054
- カーネルパラメータの変更
  - **$ sysctl -w パラメータ名=値**
  - **$ sysctl -p**
- **システムコールの使用状況**
  - **$ strace -p [プロセスID]**

## デバイス関連
- **デバイス一覧(ブロックデバイス、キャラクタデバイス)**
  - **$ cat /proc/devices**

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
- 自身のプロセスIDの確認
  - **$ echo $$**
- プロセス一覧
  - **$ ps aux -H   //プロセスを階層構造で表示**
  - **$ ps aux -L   //スレッド数、スレッドも表示**
- リソース使用率上位のプロセス一覧
  - **$ top**
    - **オプションzx押してからShift+< or Shift+>で項目ごとに降順ソート可能。**
    - **オプションfで表示項目を選択可能。Spaceで選択/非選択、ESCで決定**
    - **オプションeでメモリの単位切替可能。**
    - **オプション1でCPUコア毎の使用率を切替可能。**
    - **プロセスの状況**
      - **D(割込不可sleep≒I/Owait(ex.diskI/O, NetworkI/O is busy))**
      - **R(実行中)**
      - **S(割込可スリープ)**
      - **T(停止中)**
      - **t(停止中)**
      - **W(スワップアウト)**
      - **Z(ゾンビ)**
      - **X(死亡)**
    - 以下オプション
      - <    high-priority (not nice to other users)
      - N    low-priority (nice to other users)
      - L    has pages locked into memory (for real-time and custom IO)
      - s    is a session leader
      - l    is multi-threaded (using CLONE_THREAD, like NPTL pthreads do)
      - +    is in the foreground process group
- プロセス毎のメモリ消費量詳細
  - **pmap [プロセスID]**
- ディスクIO使用率上位のプロセス一覧
  - $ iotop
- 特定ポートを使用中のプロセスの確認
  - **$ lsof -i4TCP:8888**
- プロセスが開いているファイルの特定
  - **$ lsof -p `pgrep プロセス名`**
  - **$ ls -l /proc/プロセスID/fd**
- プロセスが開いているポートの特定
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


## パッケージ関連
- **yumレポジトリ一覧**
  - **yum-config-manager**
- **rpmのパッケージ一覧**
  - **rpm -qa**
- **特定パッケージの提供ファイル一覧確認**
  - **rpm -ql パッケージ名**
- **特定ファイルの提供パッケージ確認**
  - **rpm -qf パッケージ名**

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
- ダミーファイル作成
  - $ dd if=/dev/zero of=/path/to/work bs=1M count=1000
- バイナリダンプ確認
  - $ objdump {ファイル名}

## 時刻関連
- 現在時刻
  - date
- H/Wクロック
  - hwclock

## 環境変数関連
- 環境変数一覧
  - **$ env**

## ディスク関連
- ファイルシステム毎のディスク容量確認
  - **df -Th**

## ネットワーク
- IP割当状況
  - **$ ip a**
- ARPキャッシュ確認
  - arp -vn
- NICオフロード状況確認
  - ethtool -k ethxx
- 通信状況確認
  - **$ netstat -pantu**
  - IP別接続数の確認
    - $ netstat -pantu | awk '{print $4}' | cut -d":" -f1 | sort | uniq -c | sort -rn
  - 使用ポート一覧の確認
    - netstat -nat | awk '{print $4}' | sed -e 's/.*://' | sort | uniq -c
- 通信経路確認
  - **$ traceroute -nI [ip address]** //ICMPの場合
  - **$ traceroute -nT -p [ポート番号] [ip address]** //ICMPの場合
- ポート疎通確認
  - **$ curl -v telnet://[ip address]:[port num]**
- HTTPリクエスト&レスポンスヘッダ表示
  - **$ curl -v [ip or URL]**
- **パケットキャプチャ**
  - tcpdump
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
  - tshark
    - **$tshark -i ethxx  //基本セット**
    - **$tshark -i -V ethxx  //パケット詳細表示**
- ルーティングテーブルの情報確認
  - $ netstat -rn
- 認証方式指定のssh接続
  - $ ssh [接続先IP] -o PreferredAuthentications=publickey   //鍵認証
  - $ ssh [接続先IP] -o PreferredAuthentications=password    //パスワード
- 鍵の暗号化強度の確認
  - $ ssh-keygen -l -f ~/.ssh/id_rsa.pub

## 名前解決関連
- **$ dig [ドメイン名] @[ネームサーバー名]**
  - 例) dig example.jp @8.8.8.8

## cron関連
- 全ユーザーが持つcron一覧
  - $ cat /var/spool/cron/*
- ユーザー個別のcron
  - $ cat /var/spool/cron/ユーザー名

## 証明書関連
- 公開鍵情報
  - (全体)openssl x509 -noout -text -in {crt file}
  - (ハッシュ)openssl x509 -noout -modulus -in {crt file} | openssl md5
  - (期限) openssl x509 -noout -dates -in {crt file}
- 秘密鍵情報
  - (全体)openssl rsa -noout -text -in {key file}
  - (ハッシュ)openssl rsa -noout -modulus -in {key file} | openssl md5

## パフォーマンス
**リアルタイム負荷は何はなくともvmstat。出来ればdstatをインストール。
過去履歴はsarで確認。
負荷の高いプロセスの特定はtop、iotop。**

### システム全体の負荷(リアルタイム)
- **dstat**
  - **http://oxynotes.com/?p=7566**
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
- **mpstat -P ALL 1**
  - CPUコア毎の負荷。
  - 特定CPUだけ負荷が高い場合はシングルスレッドアプリケーションによるものと判断できる。
- dentryキャッシュの確認
  - 使用状況の確認
    - cat /proc/meminfo | grep Slab
    - slabtopでdentryの割合を確認
    - sar -vで内ネガティブdentryの割合を確認
  - キャッシュの飛ばし方
    - cat /proc/sys/vm/drop_caches

### システム全体の負荷(過去履歴)
http://d.hatena.ne.jp/end0tknr/20120206/1328499420
- **$ sar**
  - ロードアベレージ
    - sar -q
  - CPU使用率
    - sar -u
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
- wireshark
  - 詳細なネットワークキャプチャ。tsharkが使える
- curl
  - httpリクエスト疎通確認
- ncdu
  - 容量整理
- jq
  - json整形用


## 参考
- 80 Linux Monitoring Tool
  - https://www.serverdensity.com/monitor/linux/how-to/
- Linuxコマンドでやりたいことをやるためのリンク集
  - http://qiita.com/ponsuke0531/items/73895dcdbb5ddd7d3331
- 原因調査用Linuxコマンド
  - http://blog.father.gedow.net/2012/10/23/linux-command-for-trouble/
- https://gist.github.com/koudaiii/25d80004e16ba16c7e71
