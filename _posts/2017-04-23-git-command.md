---
layout: post
title: Gitで忘れがちなコマンドをまとめてみた
tags: 
- git
---
Gitで気づいたら何度も調べてるコマンドをまとめたので備忘録
  
<!-- more -->

## gitのリモートブランチと一致させたい
手元で色々いじったけどリモートに合わせたい時は以下
```
$ git fetch origin
$ git reset --hard origin/master
```

## リモートレポジトリの履歴毎消したい
リモートレポジトリ上に残った(黒)歴史を無かった事にしたい場合は以下
```
#ファイルの場合
$ git filter-branch --index-filter 'git rm --cached --ignore-unmatch [消したいファイルパス]' HEAD
$ git commit -m "delete targetfile"
$ git push origin master -f

#ディレクトリの場合
$ git filter-branch --index-filter 'git rm -r --cached --ignore-unmatch [消したいディレクトリ]' HEAD
$ git commit -m "delete targetfile"
$ git push origin master -f
```

## 特定ファイルの変更箇所を確認したい
```
$ git log -p ファイルパス
```
