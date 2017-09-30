---
layout: post
title: Docker初心者の自分が最初に知りたかった事
tags: 
- Docker
---
最近Dockerやってます!って言いたいがために気軽に調べてみたら、  
概念をおさえるまでに時間がかかったのでまとめてみました。
  
<!-- more -->
## Dockerって何？
- Go言語製のオープンソースのコンテナ管理ソフトウェア
- Linux Kernel3.10以上で動作

## 何がうれしいの？
### 軽量、高速
- 開発のライフサイクルが早くなる

### 独立したアプリ実行環境が作れる
- ファイルシステム、CPU、メモリ等すべて独立した実行環境が作れる

### ポータビリティが高い
- アプリ実行環境がパッケージングされているので、自分のローカルPC、他開発メンバーのローカルPC、データセンター上の物理サーバ、VM、クラウド等、まったく同じ環境がどこでも手軽に作れる

### スケールアップ、ダウンが簡単に出来る

### インフラ環境の構成をコードで表現できる

## 登場人物
- ホストマシン
  - Dockerをインストールするマシン。Linux Kernel3.10必須。
- **Dockerエンジン**
  - ホストマシン上で動くDockerサーバ。各コンテナの管理を行う。
- **DockerFile**
  - **Dockerイメージを作るための手順書**
- **Dockerイメージ**
  - **手順書(DockerFile)を元に作られた読込専用のテンプレートイメージ**
- **Dockerレジストリ**
  - **作成したDockerイメージの保管場所**
- **DockerHub**
  - **Dockerレジストリのホスティング環境を提供するSaaSサービス。**
  - dockerでイメージ取得を行う際のデフォルトレポジトリとなる。
- Dockerコンテナ
  - アプリケーションの実行環境。
  - **Dockerイメージをインスタンス化、プロセス化したもの**

## インストールしてみる
- Dockerインストール
  - $ yum -y install epel-release
  - $ yum -y install docker-io
- インストール確認
  - $ docker version
```
Client:
 Version:         1.12.6
 API version:     1.24
 Package version: docker-1.12.6-32.git88a4867.el7.centos.x86_64
 Go version:      go1.7.4
 Git commit:      88a4867/1.12.6
 Built:           Mon Jul  3 16:02:02 2017
 OS/Arch:         linux/amd64
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```
- 起動
  - $ systemctl start docker

## コンテナを作成してみる
- 設計書(DockerFile)を記述
  - $ vi Dockerfile
- 設計書(Dockerfile)を元にイメージテンプレート(DockerImage)作成
  - $ docker build -t [イメージ名] [配置ディレクトリ]
- イメージテンプレート(DockerImage)を元にコンテナ作成
  - $ docker run -dit [イメージ名]
- コンテナ起動を確認
  - $ docker ps 

## コンテナに接続してみる
- コンテナに接続
  - $ docker exec -it [イメージ名] /bin/bash

## Dockerfileの基本文法
- http://morizyun.github.io/docker/about-dockerfile-description.html

## Docker運用のベストプラクティス
コンテナは素早く柔軟にスケールできるようにするために、ユーザーデータを持たせずいつでも使い捨てられる環境を保つ。  
そのために以下の方針が必要。

- **コンテナをいつでも捨てられる状態にする。(コンテナから、ソースコード・データ・ログが分離した状態にする)**
- **ホストマシンの導入・設定は最小限にする**
  - ホストマシン上には極力パッケージ導入や設定変更等を行わない。
  - **移植性を高めるために、コンテナ内で必要なものは、コンテナ内で用意する。**
- **コンテナ内の導入・設定は最小限にする。余計なミドルウェアを入れない。**
- **1コンテナ、1プロセス**
  - 例えばLAMP構成の場合は、Apache、MySQL、PHP等それぞれ単機能なコンテナを作り連携させる。  
  - コンテナを増やしたり、プロセス単位で停止、起動したい時に扱いやすくするため。
- **パッケージや設定はコンテナ内に保存する**
  - ミドルウェア
  - 設定ファイル
  - 一時的なキャッシュデータ
- **ユーザーデータ、永続データはコンテナ外に保存する**
  - アプリケーションコード
  - ユーザーデータ
  - ログデータ
- **外部接続用にコンテナ-Dockerサーバマシンとの設定を行う**
  - 「-P」オプションでDockerサーバとコンテナでポートフォワーディングを行う
  - 「--link」オプションでコンテナ間接続の指定を行う。
- **データ格納専用のコンテナを作る**
  - データ格納用のコンテナを作成し、永続データをまとめる。
- **変更内容はDockerfileに全て残す**
  - docker commitは使わない。
- **Dockerfileのレイヤ数は最小限にする**
  - レイヤはDockerfile上の実行ステップが増えるたびに容量が増える。  
テストの時はRUNを足していって、最後にステップを減らすようにお掃除すると良い。
- **.dockerignoreを使う。**
  - 不要なファイルは.dockerignoreに記入しビルド対象から外す。  .gitignoreと同じイメージ。

## Dockerfileの書き方
- MAINTAINERは入れよう
- コマンドが通らなかったらENVが通っているか確認しよう

## よく使う運用コマンド
### Docker本体

|用途|コマンド|
|:--|:--|
|バージョン確認|docker version|
|docker基本情報確認|docker info|


### イメージ関連
- Dockerイメージ一覧確認
  - docker images
- DockerHubからイメージを検索
  - docker search [キーワード]
- DockerHubからのイメージのダウンロード
  - docker pull [レポジトリ名]
- DockerHubへイメージをアップロード
  - docker push [レポジトリ名]
- Dockerイメージへタグ付け
  - docker tag [イメージID] [DockerHubアカウント名]/[イメージ名]/[タグ名]
- イメージのビルド(Dockerfile必須)
  - docker build -t イメージ名 Dockerfile配置フォルダ
- イメージの削除
  - docker rmi イメージ名
  - 強制削除する場合は「-f」をつける。

### コンテナ関連
- コンテナの生成
  - docker run --privileged -dit docker-httpd /bin/bash
    - -d: デーモンとしてコンテナを実行
    - -it: 対話型モードで起動
    - -v: 外部ディスクのマウント設定(-v ホストディレクトリ名:ゲストディレクトリ名)
    - -p: ポートフォワーディング設定(-p ホストポート名:ゲストポート名)
      - 例)-p 10080:80ならDockerホストの10080ポートがコンテナ80ポートに連結する。
    - --rm: コンテナから自動削除
    - --privileged: コンテナに拡張権限を与える
    - --name: コンテナ名を指定
- コンテナ削除
  - docker rm コンテナ名
  - 強制削除する場合は「-f」をつける。
  - 複数コンテナまとめて削除は「docker rm -f `sudo docker ps -aq`」
- コンテナ再起動
  - docker restart コンテナ名
- Dockerコンテナへの接続
  - docker exec -it コンテナ名 /bin/bash
- コンテナ内で稼働するプロセスの確認
  - docker top コンテナ名
- Dockerコンテナからの切断
  - Ctrl+p -> Ctrl+q
  - 「exit」だとDockerコンテナからの切断+コンテナ停止

## つまづいた所
- Docker上でCentOS7イメージを動かすと「Failed to get D-Bus connection: Operation not permitted」メッセージが出てsystemctlコマンドがエラーになる。
  - docker run 実行時に--privilegedオプションが必要。

## 参考
- Docker日本語マニュアル
  - まとまった情報が欲しいならこちら。
  - http://docs.docker.jp/pdf-download.html
- Docker基本のおさらい
  - https://www.slideshare.net/ngzm/docker-48648898
- 5分でわかるDockerの基本
  - http://www.ryuzee.com/contents/blog/6952
- Dockerfile を書くベスト・プラクティス
  - http://docs.docker.jp/engine/userguide/eng-image/dockerfile_best-practice.html
- 開発環境で使うDocker入門
  - http://dev.classmethod.jp/tool/docker/develop-with-docker/
- 開発環境をDockerに乗せる方法とメリットを3ステップで学ぶチュートリアル
  - http://qiita.com/KeitaMoromizato/items/ae1a57fc62b41b942d71
- Docker環境を本番で使うためのセキュリティ設定
  - http://qiita.com/muff1225/items/4edea7b039dd9f26098f
