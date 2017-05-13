---
layout: post
title: サーバー基本情報の収集コマンド
tags: 
- os
---
忘れては調べの繰り返しで早半年、いい加減メモとして残しておきます
<!-- more -->
## CPU
プロセッサ数＝ソケット数 * コア数で求められるが、ハイパースレッディング使用だと一致しない場合あり。
- CPUモデル名
  - cat /proc/cpuinfo |grep "model name" | head -1
- プロセッサ数
  - cat /proc/cpuinfo | grep processor | wc -l
- ソケット数
  - cat /proc/cpuinfo | grep "physical id" | sort | uniq -c | wc -l
- コア数
  - cat /proc/cpuinfo | grep "cpu cores"

## メモリ使用量
最終行にメモリ全容量/使用量/空き容量の記載有り。
- free -m | head -3 | gawk 'NR != 2 {print $0} NR == 2 {printf("%s %d/%d/%d\n",$0,$2,$3,$4)}'

## ディスク使用量
最終行にメモリ全容量/使用量/空き容量の記載有り。
- df -m | gawk 'NR == 1 {print $0; next} /mapper/ {if (NF==1) {getline;i+=$1;j+=$2;k+=$3} else {i+=$2;j+=$3;k+=$4};printf("%s         %d/%d/%d\n",$0,i,j,k); next} /[a-z/]+/ {i+=$2;j+=$3;k+=$4;printf("%s         %d/%d/%d\n",$0,i,j,k)}'

## OSバージョン
- cat /etc/redhat-release

## 参考
- https://open-groove.net/linux/cpu-core-hyper-threading/
