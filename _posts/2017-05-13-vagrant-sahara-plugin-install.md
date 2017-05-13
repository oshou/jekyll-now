---
layout: post
title: Vagrant-Saharaプラグインのインストール手順
tags: 
- vagrant
---
vagrant専用プラグインでサーバー状態の保管・復元出来るsaharaプラグインをインストールしてみた
※vagrant導入済みの前提
  
<!-- more -->
Vagrant Saharaはsandboxモードというモードがあり、このモードに切り替わる事で状態の保管、復元が出来るようになります。

### Vagrant Saharaプラグインのインストール
$ cd 作業用パス  
$ vagrant plugin install sahara  
以下メッセージが出たらインストール完了！  
vagrant plugin install sahara Installing the 'sahara' plugin. This can take a few minutes... Installed the plugin 'sahara (0.0.16)'!   

### sandboxモードの開始
$ vagrant sandbox on  
進捗が100%まで進行しエラーが無ければ完了。  

# 状態の保存
$ vagrant sandbox commit  
進捗が100%まで進行しエラーが無ければ完了。  

# 状態の復元(commitした時点までのロールバック)
$ vagrant sandbox rollback  
進捗が100%まで進行しエラーが無ければ完了。  

# sandboxモード終了（commitしていないものは破棄される）
$ vagrant sandbox off  
進捗が100%まで進行しエラーが無ければ完了。  

# 現在sandboxモードかどうかの確認
$ vagrant sandbox status  
以下のメッセージでon、offいずれかが表示される。  
Sandbox mode is on/off
