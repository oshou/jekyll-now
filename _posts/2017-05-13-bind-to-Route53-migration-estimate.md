---
layout: post
title: オンプレBIND運用からRoute53に乗り換えた場合の費用を見積もってみた
tags: 
- bind
- route53
---
社内オンプレでBIND運用しているけど、終わりなきBIND脆弱性対応から開放されたい、、、Route53を検討してみた
<!-- more -->
- 社内オンプレ環境でBIND使ってるけどRoute53安いらしいし使いやすそう、なによりbind脆弱性対応から開放されたい、、、
- ただ従量課金て聞くし実際いくらかかるのか怖い、、、
- という事で費用見積の仕方をまとめてみた。

## Route53とは
- Amazonが提供するDNSサービス(ドメインとIPアドレスの対応表)
- Port53を使用する事が由来。国道53号線は無関係

## Route53の特徴
- 高可用性
    - 高い。全世界に分散配置されたネームサーバ4台に登録。
    - SLA100%(停止した場合に使用量を払い戻し。違約金等は無し？)
        - https://goo.gl/qcRzMV
- 高速
    - 分散配置されたDNSサーバの内、ユーザーから最も論理的に近いサーバを自動選択
- コスト
    - 従量課金
- 操作性
    - GUIコンソール提供
    - API提供有り
- 保守運用
    - bind脆弱性対応に関してはAmazonにおまかせ

## 課金体系
- ホストゾーン数課金＋クエリ数課金で計算。クエリ数が金額の大部分を占める。
    - ホストゾーン課金
        - 0.50 USD/月,1ホストゾーン (ホストゾーン数<=25の場合)
        - 0.10 USD/月,1ホストゾーン (25<ホストゾーン数の場合)
    - クエリ数課金
        - 0.40 USD/100万クエリ (月の総クエリ数<=10億クエリの場合)
        - 0.20 USD/100万クエリ (10億クエリ<月の総クエリ数の場合)

## 費用の求め方
### ホストゾーン数の求め方
- bindのzoneファイル数をカウント

### クエリ数の求め方
- named.confのloggingタグに以下を追記(以下設定だと10Mずつ30世代保管)
    ```
    logging{
    ・・・
      channel query_log {
        file "/var/log/named/nsquery.log" versions 30 size 10m;
        severity info;
        print-category yes;
        print-severity yes;
        print-time yes;
    };
    category queries { query_log; };
    ・・・
    }
    ```
- BIND再起動
- /var/log/named/nsquery.log.xxxという形でクエリログファイルが作成される。
- 指定したログディレクトリの開始、終了時間、リクエストログの行数を元に単位時間あたりのクエリ数を求める。
    - 収集時間
        - nsquery.logの最も新しいもの、古いもののクエリログの時間を確認
    - 収集時間帯のクエリ数
        - cat /var/log/named/nsquery.log* | wc -l

## 参考
- 5分でわかる　Route53
    - https://recipe.kc-cloud.jp/archives/7581
- Amazon Route53 使ってみた
    - https://www.slideshare.net/mikiT1/2011127-amazon-route53-m1
