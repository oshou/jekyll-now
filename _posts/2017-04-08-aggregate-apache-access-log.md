---
layout: post
title: Apacheアクセスログからのアクセス数集計方法
tags: 
- Apache
---
いつも忘れて調べるので備忘録
  
<!-- more -->
## アクセス元IP毎のアクセス数(Top30)
- **cat ログファイル名 | awk '{print $1}' | sort | uniq -c | sort -rn**
  - 例) grep "18/Dec/2016" /var/log/httpd/access_log | awk '{print $1}' | sort | uniq -c | sort -rn

## 1時間毎のアクセス数(Top30)
- **cat ログファイル名 | awk '{print $4}' | cut -b 2-15 | sort | uniq -c | sort -rn**
  - 例) grep "18/Dec/2016" /var/log/httpd/access_log | awk '{print $4}' | cut -b 2-15 | sort | uniq -c

## アクセス先URL毎のアクセス数(Top30)
- **cat ログファイル名 | awk '{print $7}' | sort | uniq -c | sort -rn**
  - 例) grep "18/Dec/2016" /var/log/httpd/access_log | awk '{print $7}' | sort | uniq -c | sort -rn

## HTTPステータスコード別の1時間毎のリクエスト数
- **cat ログファイル名 | awk '{print $9,$4}' | cut -b 1-19 | sort | uniq -c

## 補足
- リアルタイムのアクセス数集計方法はこちら
  - **netstat -pantu | grep -e ':80' -e ':443' | awk '{print $6}' | sort | uniq -c**
