---
layout: post
title: Slack通知設定まとめ
tags: 
- Slack
---
Slackの通知の基準がよくわからない、なんか夜間通知されていない気がする、、、という方は以下を見てみて下さい。  
  
<!-- more -->
全チャネル共通設定は「Only Direct Messages & Highlight Keyword」(自分宛メッセージ&キーワードのみ)
確認必須の内容をやりとりするチャネルは「Everything」を設定が良いと思います。  
iPhoneの方はデフォルトで夜間通知がされないので、必要により変更が必要かも

## 各個人の全チャネル共通設定
* 以下URLにアクセス
  https://チーム名.slack.com/account/notifications
* [Desktop Notifications] [Mobile Push Notifications]それぞれ通知レベルを設定
　Activity of any kind ・・・すべて通知
　Only Direct Messages & Highlight Keyword ・・・自分宛のメッセージ、キーワードにヒットするメッセージのみ対象(キーワードは設定している場合のみ）
　Nothing・・・通知しない

## 各個人の個別チャネル設定
* 以下URLにアクセス
  https://チーム名.slack.com/account/notifications
* [Channel Specific Settings]でチャンネルを選択の上、それぞれ通知レベルを設定
  Everything ・・・すべて通知
  Mention ・・・自分宛のメッセージ、キーワードにヒットするメッセージのみ対象(キーワードは設定している場合のみ）
  Nothing・・・通知しない

## (iPhoneの方のみ)通知スケジュール設定
* iPhoneだとデフォルトで夜間帯(22:00～08:00)は通知しません。
* 変更する場合は以下で設定
　「Setting」-->[Do not Disturb]-->[Schejuled]をオフ、もしくは時間変更
  ※以下参照
　  https://nenza.net/2016/06/27136.html
