---
layout: post
title: MySQL基礎コマンド チートシート(随時更新)
tags: 
- mysql
---
忘れがちなMySQL基礎コマンドをまとめてみた。  
随時更新。
  
<!-- more -->
先頭に「mysql> 」が付いたものはmysqlログイン後、ついていないものはログイン前に実施する。


## 基本情報
MySQLにはデフォルトで以下DBが用意されている。
- information_schema
  - MySQL関連のメタデータを一元管理するDB
- performance_schema
  - パフォーマンス関連のデータを一元管理するDB
- sys
  -  performance_schemaやinformation_schemaの情報を見やすくするためのビュー、ストアドプロシージャまとめ
- mysql

## 基本操作

### サーバ起動
$ service mysqld start //CentOS7
$ systemctl start mysqld

### サーバ停止
$ mysqladmin -uroot -p shutdown

### サーバ接続開始
$ mysql -h[ホスト名orIP] -u[ユーザー名] -p[パスワード]  
$ mysql -h[ホスト名orIP] -u[ユーザー名] -p[パスワード] -e "[実行コマンド]" //コマンド直接実行
$ mysql -h[ホスト名orIP] -u[ユーザー名] -p[パスワード] DB名 < input.sql //特定ユーザで接続し外部SQLファイルを投入する場合  
$ mysql -h[ホスト名orIP] -u[ユーザー名] -p[パスワード] DB名 > output.sql //特定ユーザで接続しやりとりを外部SQLファイルに記録

### サーバ接続終了
$ mysql> exit;

### 初期ログイン時パスワードセット
**$ mysql> set password for 'ユーザ名'@'接続元ホスト'='パスワード';**  
以下メッセージが出たらok。  
Query OK, 0 rows affected (0.07 sec)


## 設定情報確認
### MySQLバージョン確認
**$ mysql -V**  
以下のような画面が表示されたらok  
$ mysql  Ver 14.14 Distrib 5.5.34, for Linux (x86_64) using readline 5.1

### アクティブプロセス一覧
**$ mysql> show full processlist;**

### 登録済ユーザー・権限一覧
- 登録済ユーザー一覧
**$ mysql> select host,user from mysql.user;**
- グローバルレベルの権限情報一覧
**$ mysql> select * from information_schema.user_privileges;**
- DBスキーマレベルの権限情報一覧
**$ mysql> select * from information_schema.schema_privileges;**
- テーブルレベルの権限情報一覧
**$ mysql> select * from information_schema.table_privileges;**
- カラムレベルの権限情報一覧
**$ mysql> select * from information_schema.column_privileges;**

### 各種環境変数の確認
- **MySQLで扱う環境変数は以下2種類がある。
  - セッション変数：一時設定。現在の接続だけが影響する。
  - グローバル変数：恒久設定。変数設定後のすべての接続が影響する。
**$ mysql> show global variables;**  
**$ mysql> show session variables;**  
絞り込み表示したい場合は以下  
$ mysql> show variables like 'キーワード'  
キーワード内は%をつけることでアスタリスク的な使い方が出来る  
$ mysql> show variables like '%character_set%'  

### 現在の状態確認
**$ mysql> show global status;**

### 現在のINNODBの状態確認
**$ mysql> show engine innodb status;**

### MySQLプラグイン一覧確認
**$ mysql> show plugins;**

### エラー一覧
**$ mysql> show warnings;**

### 過去の最大コネクション数
**$ mysql> show status like '%Max_used%';

### 現在のコネクション数
**$ mysql> show status like '%Threads_connected%';

### メモリ設定確認用SQL
**こちらでかなり整理してまとめて頂いていたのでリンク**
**http://masato.ushio.org/blog/index.php/2015/12/31/uco-tech_mysql-memory-usage/**
```
$ mysql> 
select
@@GLOBAL.INNODB_BUFFER_POOL_SIZE/1024 as GLOBAL_INNODB_BUFFER_POOL_SIZE_mb,
@@GLOBAL.INNODB_LOG_BUFFER_SIZE/1024 as GLOBAL_INNODB_LOG_BUFFER_SIZE_mb,
@@GLOBAL.NET_BUFFER_LENGTH/1024 as GLOBAL_NET_BUFFER_LENGTH_mb,
@@GLOBAL.MAX_CONNECTIONS as MAX_CONNECTIONS,
(@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE)/1024/1024 as THREAD_BUFFER_SIZE_mb,
(@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.NET_BUFFER_LENGTH + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS)/1024/1024 AS TOTAL_MEMORY_SIZE_mb,
(@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.NET_BUFFER_LENGTH + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS)/1024/1024/1024 AS TOTAL_MEMORY_SIZE_gb
\G
```

## ユーザー操作
### ユーザー作成
$ mysql> create user 'ユーザー名'@'接続元ホスト' IDENTIFIED BY 'パスワード';

### ユーザー削除
$ mysql> drop user 'ユーザー名'@'接続元ホスト';

### ユーザー毎の権限情報確認
$ mysql> show grants for 'ユーザー名'@'接続元ホスト';

### ユーザー毎の権限情報確認
$ mysql> grant all privileges on [DB2名].[テーブル名] to 'ユーザー名'@'接続元ホスト' WITH GRANT OPTION;


## DB操作
### 作成済DBの一覧表示
$ mysql> show databases;

### 作成済DBの作成用SQL出力(文字コードを確認)
$ mysql> show create database DB名;

### DB作成
$ mysql> create database DB名;  
以下メッセージが出たらok  
$ Query OK, 1 row affected (0.00 sec)

### DB削除
$ mysql> drop database DB名;  
以下メッセージが出たらok。  
$ Query OK, 0 rows affected (0.13 sec)  
※show databasesで消えている事を確認。

### 使用するDBの指定
$ mysql> use DB名;  
以下メッセージが出たらok。  
Database changed

### 指定したDBへの特定ユーザーの権限付与
$ mysql> grant all on DB名.テーブル名 to 'ユーザー名';

## テーブル関連
### 作成済テーブルの一覧表示
$ mysql> show tables;

### 作成済テーブルの作成用SQL出力(文字コードを確認)
$ mysql> show create table テーブル名;

### テーブル定義の確認
$ mysql> desc テーブル名;  
※descはdescriptionの略

### テーブル作成
$ mysql> create table テーブル名;  
    -> (  
    -> id INT(10),  
    -> name VARCHAR(30)  
    -> );  
$ Query OK, 0 rows affected (0.09 sec)

- テーブル削除
$ mysql> drop table テーブル名;

### テーブルの中身を全て表示
$ mysql> select * from テーブル名;

### テーブルの中身の追加
$ mysql> insert into テーブル名 フィールド名 values フィールドの値;  
例) $ mysql> insert into table (field1,field2,field3) values (value1,value2,value3);

### テーブルの中身の削除
$ mysql> delete from テーブル名 where 条件文;  
例) $ mysql> delete from table1 where id=3;

### テーブルの中身の全消去
$ mysql> truncate テーブル名;  
例) $ mysql> truncate table1;

### テーブルの中身の更新
$ mysql> update テーブル名 set フィールド名 where 条件;  
例) $ mysql> update table1 set (field1=1) where (field2=4);

### テーブルの最適化
$ mysql> analize table テーブル名;
例) $ mysql> analize table table1;

## バックアップ
### 全DBのバックアップ
- データ + 定義情報
  - mysqldump -u'ユーザー名' -p'パスワード' --all-databases > backup.sql
- 定義情報のみ
  - mysqldump -u'ユーザー名' -p'パスワード' -d --all-databases > backup.sql

### 特定DBのバックアップ
- データ + 定義情報
  - mysqldump -u'ユーザー名' -p'パスワード' DB名 > backup.sql
- 定義情報のみ
  - mysqldump -u'ユーザー名' -p'パスワード' -d DB名 > backup.sql

### 実行時にエラーが出た場合
以下エラーが出た場合はmy.cnfを修正  
「mysqldump: unknown variable 'symbolic-links=0'」  
変更箇所  
- MySQLの設定ファイル「my.cnf」にて「symbolic-links」をコメントアウト

## リストア
### 全DBのリストア
- mysqldump -u'ユーザー名' -p'パスワード' < backup.sql

### 特定DBのリストア
- mysqldump -u'ユーザー名' -p'パスワード' DB名 < backup.sql

## レプリケーション
### マスター稼働状況
- $ mysql> show master status \G

### スレーブ稼働状況
- $ mysql> show slave status \G

## 運用のポイント
- 接続時の不要なDNS問い合わせを避ける
  - /etc/my.cnfにskip-name-resolveオプションをつけよう
- キャッシュサイズは適切か?
  - ヒット率が6割を下回るようであれば見直しをしたほうが良いかもしれません。
  - http://qiita.com/muran001/items/14f19959d4723ffc29cc
  - クエリキャッシュヒット率
  　- ＝キャッシュヒット数 / クエリ発行総数
  　- ＝キャッシュヒット数 / (キャッシュヒット数＋キャッシュミス数)
  　- ＝Qcache_hits / ( Qcache_hit + Com_select)　
- メモリサイジングを適切にしよう
  - MySQLのメモリは、サーバ全体で使用する「GlobalBuffer」と、各接続毎に確保される「ThreadBuffer」の2種類がある。
  - **全体のメモリ容量 > GlobalBuffer一式 + (max_connections * ThreadBuffer一式)に収まるようにする。**
select
@@GLOBAL.INNODB_BUFFER_POOL_SIZE as GLOBAL_INNODB_BUFFER_POOL_SIZE,
@@GLOBAL.INNODB_LOG_BUFFER_SIZE as GLOBAL_INNODB_LOG_BUFFER_SIZE,
@@GLOBAL.KEY_BUFFER_SIZE as GLOBAL_MyISAM_KEY_BUFFER_SIZE,
@@GLOBAL.TMP_TABLE_SIZE as GLOBAL_TMP_TABLE_SIZE,
@@GLOBAL.QUERY_CACHE_SIZE as GLOBAL_QUERY_CACHE_SIZE,
@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE as THREAD_BUFFER_SIZE,
@@GLOBAL.MAX_CONNECTIONS as MAX_CONNECTIONS,
@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.NET_BUFFER_LENGTH + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS AS TOTAL_MEMORY_SIZE,
 (@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.NET_BUFFER_LENGTH + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS)/1024 AS TOTAL_MEMORY_SIZE_kb,
 (@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.NET_BUFFER_LENGTH + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS)/1024/1024 AS TOTAL_MEMORY_SIZE_mb,
 (@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.NET_BUFFER_LENGTH + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS)/1024/1024/1024 AS TOTAL_MEMORY_SIZE_gb
 \G
- EXPLAINで実行計画を立ててクエリを最適化しよう
  - 必ずクエリ側でWHERE句による対象絞り込みをし、全件フェッチを避けよう。

## パフォーマンス
- global buffer
  - innodb_buffer_pool_size(80%程度), myisam_key_size(25%程度)で用途に応じて設定できているか
- thread_cache_size = max_connections/3になっているか
  - Treads_createdが目安


## 参考
- MySQL 運用時に便利なコマンド
  - https://www.qoosky.io/techs/3a369dd466
- mysqldumpまとめ
  - http://qiita.com/PlanetMeron/items/3a41e14607a65bc9b60c
