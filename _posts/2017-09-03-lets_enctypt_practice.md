---
layout: post
title: nginxにLet's Enctyptを導入してみた
tags: 
- SSL
---
検証環境
  
<!-- more -->
個人用サービス常時SSL化を検討した際に、無料のSSL証明書サービスがあるということで試してみた。  
導入・運用コストも低く(自動化してしまえば手間いらず)、個人利用なら管理コストを大幅に減らせそうでかなりおすすめ。

## Let's Enctyptとはなんぞや
無料のSSL/TLSサーバ証明書を提供するサービス
- 即時発行
- 有効期間は90日
- ワイルドカード証明書の発行は不可
- **DV-SSL(ドメイン認証)のみ対応。企業、商用サイトでブランディング、安全性アピールをするは別途有料版の購入の検討が必要。**  
**(SSL証明書の概要、種類は以下参照)**

## SSL証明書のおさらい
- 以下目的で使用される
  - **①Webサイトでやりとりする情報の「暗号化」**
  - **②Webサイト所有者の「身元証明」**
- **SSL証明書には以下3種類がある。目的①はどれも行うが、目的②の審査の厳格さが違う**
  - DV-SSL(ドメイン認証SSL)
    - **暗号化のみ。運営元の身元証明をしない。**
    - 個人用途等
  - OV-SSL(企業実在認証SSL)
    - **暗号化 + Webサイト所有者の身元証明**
    - 企業用途。電話確認による企業・組織の実在性確認を行う。
  - EV-SSL(強化認証SSL)
    - **暗号化 + Webサイト所有者の厳格な身元証明**
    - 企業用途。URL欄を緑色にして組織名を表示、ひと目で安全性をアピール


## 導入してみた
公式に各OS別のインストール手順があるのでそのまま実行  
今回はCentOS6、nginx使用の環境で検証した。  
https://letsencrypt.jp/usage/install-certbot.html#CentOS6

- Certboxクライアントのインストール
$ yum -y install epel-release  
$ wget https://dl.eff.org/certbot-auto  
$ chmod a+x certbot-auto  
$ ./certbot-auto  

- 無料SSL証明書の発行(Webサーバの停止が必要)
$ service nginx stop
$ certbot-auto certonly --standalone -d www.example.com

- WebサーバのSSL鍵認証ファイルに以下ファイル場所を設定
  - 公開鍵(SSL証明書)
    - /etc/letsenctypt/live/ドメイン名/cert.pem
  - 中間証明書
    - /etc/letsenctypt/live/ドメイン名/chainN.pem
  - 証明書+中間証明書
    - /etc/letsenctypt/live/ドメイン名/fullchainN.pem
  - 秘密鍵
    - /etc/letsenctypt/live/ドメイン名/privkey.pem

##  証明書更新を自動化してみた
Let's Encryptで取得した証明書は有効期限が90日で切れてしまうため、証明書更新をcron登録等で自動化しておくと良い。  
以下はnginxでの実行例  
* * * 1 * /root/certbot-auto renew && service nginx reload

## 参考
- 無料のSSL証明書【Let’s Encrypt】を使ってみた
  - https://www.rivercrane.com/blog/1106/
- SSLサーバ証明書の違い
  - https://goo.gl/c7myED
- SSL種類の比較と利用用途
  - https://jp.globalsign.com/service/ssl/knowledge/types-of-ssl.html
