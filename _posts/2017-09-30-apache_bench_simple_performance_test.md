---
layout: post
title: Apache Benchによる簡易的な負荷テストのやり方
tags: 
- test
- Apache
---

ガッツリ性能テストの前に、とりあえず簡易的な負荷を見たいときはApache Benchが良さそう
  
<!-- more -->


## 前提
- 今回見ることが出来るのはサーバ側の処理性能です。  
  クライアント側でのJavascript実行時間、HTMLレンダリング等の利用は含まれていません。
- 簡易ベンチマークツールabはApache httpdに付属のコマンドです。  
無い場合はサクッとインストールしてしまいましょう。

```
$ yum install httpd -y
```

## 負荷のかけ方

```
// KeepAliveありの場合
$ ab -n <Total発行リクエスト数> -c <同時接続数> <URL>

// KeepAlive無しの場合
$ ab -k -n <Total発行リクエスト数> -c <同時接続数> <URL>

```

## 結果の見方
以下のように出力結果にはたくさんの情報が出力されますが見るべきパラメータは以下です。

|パラメータ名|意味|
|:--|:--|
|成功したリクエスト数|Complete requests|
|失敗したリクエスト数|Failed requests|
|秒間リクエスト数|Requests per second|
|1リクエストの平均レスポンスタイム|Time per request(mean, across all concurrent requests)|

以下のような観点でそれぞれ必要となります。
1. すべてのリクエストが正しく処理できているか
  - 「Failed Request」が0以上になっていないか
  - **0以上になっていてもLengthFailedのみである場合は動的ページ生成した際のContent-lengthとのズレをエラーとして扱っているだけなので無視して良い場合もあります。**
    - http://kimoto.hatenablog.com/entry/2014/07/28/140843
    - https://stackoverflow.com/questions/6475692/investigating-apache-benchmark-failed-request
2. レスポンスタイムにどの程度遅延が見られるか
  - 「Time per request(mean, across all concurrent requests)」が大きな値となっていないか
  - **「Time per Request(meac)」はよく似ていますが、こちらは(1リクエストの平均レスポンスタイム)x(同時接続数)なので対象外です。要注意。**
3. 秒間何リクエストさばけるか
  - 「Request per second」がどの程度の値となっているか
  - 「月間1億PV」とかだと、100,000,000 / 30 / 24 / 60 / 60 ≒ 38.5で実は秒間38.5req/s程度なんですね。  
途方もない数字に見えても、秒間計算だと割りと身近な数値になってる気がする不思議。

- 出力結果例

```
Server Software:        unit/0.1
Server Hostname:        localhost
Server Port:            8300

Document Path:          /
Document Length:        55174 bytes

Concurrency Level:      100
Time taken for tests:   2.038 seconds
Complete requests:      1000
Failed requests:        106
   (Connect: 0, Receive: 0, Length: 106, Exceptions: 0)
Write errors:           0
Total transferred:      55266887 bytes
HTML transferred:       55173887 bytes
Requests per second:    490.67 [#/sec] (mean)
Time per request:       203.804 [ms] (mean)
Time per request:       2.038 [ms] (mean, across all concurrent requests)
Transfer rate:          26482.06 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    1   1.9      0       9
Processing:    17  194  29.4    200     222
Waiting:        5  193  29.5    199     222
Total:         17  194  28.7    200     230

Percentage of the requests served within a certain time (ms)
  50%    200
  66%    203
  75%    204
  80%    206
  90%    209
  95%    212
  98%    214
  99%    221
 100%    230 (longest request)
 ```
## 参考
- Apache Bench(ab)を使って初めての負荷検証を行う
  - https://simple-it-life.com/2016/08/28/apache-bench/
- ポイントだけApache Bench
  - https://sankame.github.io/blog/2014-06-07-apache_bench/
- 
  - http://kimoto.hatenablog.com/entry/2014/07/28/140843
