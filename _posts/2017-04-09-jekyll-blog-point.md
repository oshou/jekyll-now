---
layout: post
title: jekyllブログを始めたらまず最初に設定したいポイント
tags: 
- blog
---
エンジニア中心に人気が出ているjekyllブログを触ってみました。  
初期設定はあちこち調べてまとめたので基本設定をまとめてみました。  
プラグインやテーマはお好みでいじれそうなので色々いじり倒してみたくなりそう、、
  
<!-- more -->

## jekyllとは
- 静的サイトブログジェネレータ
- ただのhtmlファイルを生成するだけなので、動的サイトは基本的にはつくれない。(プラグイン等でカスタムは可能)

## 特徴
- 無料
- Markdownで書ける!
- 独自ドメイン使用可能
- 記事データのバックアップ不要。全てGithub上に残ってる。
- Githubのサーバ上で稼働する

## 設定したいポイント
### 管理者情報の入力
- /.config.ymlで以下を入力
  - name: ※ブログサイトのタイトル
  - description: ※ブログサイトの説明文
  - avatar: アイコン画像のURL
  - footer_links: mailや各種SNSのアカウント

### プロフィールの編集
- /about.mdで連絡先、プロフィールを入力

### GoogleAnalytics連携
- 自分のGoogleAnalytics管理画面からjekyllブログ用アカウントを作成
  - GoogleAnalyticsログイン
  - 「管理」--> 「新しいアカウントを作成」
  -  アカウント情報を入力して「トラッキングIDを取得」
    - アカウント名　例)jekyllブログ
    - ウェブサイトの名前 例)0からのインフラ日誌
    - ウェブサイトのURL 例)https://oshou.github.io/
    - 業種 例)その他
    - レポートのタイムゾーン 例)日本
  - 完了画面の「トラッキングID」をコピー
- jekyll設定ファイルにトラッキングIDを設定
  - /.config.yml
    - google_analytics: UA-**********

### コメント機能追加(DISQUS連携)
- コメント欄には「DISQUS」という外部オンラインサービスを使います。
- DISQUSとは?
  - ブログやサイトに多機能コメント欄を提供するオンラインサービスです。
  - こちらのサイトでわかりやすくまとまっていました。
    - http://webbusiness-kan.com/disqus/
- 導入の仕方
  - DISQUSアカウントを作成
  - jekyll設定ファイルにdisqusショートネームを設定

### フォント
- デフォルトフォントの編集
  - /style.scssを編集
    - 私の場合は、font-typeは"Meiryo"、font-size: h1は28px,h2=24px,h3=18p辺りは変更する。

### テーマ
- 充実しているのでお好みで選ぶ。
  - [jekyllthemes.io](https://jekyllthemes.io)
  - [jekyllthemes.org](https://jekyllthemes.org)

### プラグイン
- インストール方法
  - http://jekyllrb-ja.github.io/docs/plugins/

### 参考
- 30分のチュートリアルでjekyllを理解する
  - https://goo.gl/toJfsf
