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
  

## 基本操作
### MySQLログイン
$ mysql -u'ユーザー名' -p'パスワード'  
$ mysql -u'ユーザー名' -p'パスワード' DB名 < input.sql (特定ユーザで接続し外部SQLファイルを投入する場合)  
$ mysql -u'ユーザー名' -p'パスワード' DB名 > output.sql (特定ユーザで接続しやりとりを外部SQLファイルに記録)  

### MySQLログアウト
$ mysql> exit;

### 初期ログイン時パスワードセット
$ mysql> set password for 'ユーザ名'@'接続元ホスト'=password('パスワード');  
以下メッセージが出たらok。  
Query OK, 0 rows affected (0.07 sec)


## DB操作
### 作成済DBの一覧表示
$ mysql> show databases;

### 作成済DBの文字コード情報確認
$ mysql> show create database DB名

### DB作成
$ mysql> create database DB名;
以下メッセージが出たらok
$ Query OK, 1 row affected (0.00 sec)

### DB削除
$ mysql> drop database DB名;
以下メッセージが出たらok。
$ Query OK, 0 rows affected (0.13 sec)
※show databasesで消えている事を確認。

### 使用するDBの切替
$ mysql> use DB名  
以下メッセージが出たらok。
Database changed

### 指定したDBへの特定ユーザーの権限付与
$ mysql> grant all on DB名.テーブル名 to 'ユーザー名';

## テーブル関連
### 作成済テーブルの一覧表示
$ mysql> show tables;

### 作成済テーブルの文字コード情報確認
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
例）
$ mysql> insert into table (field1,field2,field3) values (value1,value2,value3);

### テーブルの中身の削除
$ mysql> delete from テーブル名 where 条件文;
例）
$ mysql> delete from table1 where id=3;

### テーブルの中身の全消去
$ mysql> truncate テーブル名;
例）
$ mysql> truncate table1;

### テーブルの中身の更新
$ mysql> update テーブル名 set フィールド名 where 条件;
例）
$ mysql> update table set (field1=1) where (field2=4);

## 設定情報確認
### MySQLバージョン確認
$ mysql -V  
以下のような画面が表示されたらok  
$ mysql  Ver 14.14 Distrib 5.5.34, for Linux (x86_64) using readline 5.1

### 登録済ユーザー一覧と権限情報
$ mysql> select host,user from mysql.user;

### 接続ユーザー一覧の確認
$ mysql> show processlist;

### 各種設定確認
$ mysql> show variables;  
絞り込み表示したい場合は以下  
$ mysql> show variables like 'キーワード'  
キーワード内は%をつけることでアスタリスク的な使い方が出来る  
$ mysql> show variables like '%character%'  
- MySQLデフォルト文字コード設定確認
$ mysql> show variables like 'character_set%';

### MySQLプラグイン一覧確認
$ mysql> show plugins;

### メモリ設定確認用SQL
```
$ mysql> select
@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.INNODB_ADDITIONAL_MEM_POOL_SIZE + @@GLOBAL.NET_BUFFER_LENGTH as GLOBAL_BUFFER_SIZE,
@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE as THREAD_BUFFER_SIZE,
@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.INNODB_ADDITIONAL_MEM_POOL_SIZE + @@GLOBAL.NET_BUFFER_LENGTH
 + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS AS TOTAL_MEMORY_SIZE,
(@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.INNODB_ADDITIONAL_MEM_POOL_SIZE + @@GLOBAL.NET_BUFFER_LENGTH
 + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS)/1024 AS TOTAL_MEMORY_SIZE_kb,
(@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.INNODB_ADDITIONAL_MEM_POOL_SIZE + @@GLOBAL.NET_BUFFER_LENGTH
 + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS)/1024/1024 AS TOTAL_MEMORY_SIZE_mb,
(@@GLOBAL.KEY_BUFFER_SIZE + @@GLOBAL.INNODB_BUFFER_POOL_SIZE + @@GLOBAL.INNODB_LOG_BUFFER_SIZE + @@GLOBAL.INNODB_ADDITIONAL_MEM_POOL_SIZE + @@GLOBAL.NET_BUFFER_LENGTH
 + (@@GLOBAL.SORT_BUFFER_SIZE + @@GLOBAL.MYISAM_SORT_BUFFER_SIZE + @@GLOBAL.READ_BUFFER_SIZE + @@GLOBAL.JOIN_BUFFER_SIZE + @@GLOBAL.READ_RND_BUFFER_SIZE) * @@GLOBAL.MAX_CONNECTIONS)/1024/1024/1024 AS TOTAL_MEMORY_SIZE_gb
\G
```

## バックアップ
### 全DBのバックアップ
- mysqldump -u'ユーザー名' -p'パスワード' --all-databases > backup.sql

### 特定DBのバックアップ
- mysqldump -u'ユーザー名' -p'パスワード' DB名 > backup.sql

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
