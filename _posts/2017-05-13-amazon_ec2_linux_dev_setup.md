---
layout: post
title: Amazon EC2 Linux開発環境構築メモ
tags: 
- OS
---
AWS環境で開発環境構築した時の各種手順をまとめてみました。
  
<!-- more -->
AWSへSSH接続可能になったら以下を実施

## 環境
- RHEL6.5
- Apache2.2.15(Unix)
- PHP5.4.33
- MySQL5.6

## OS基本設定

### rootパスワード設定
- rootパスワードを設定
    - ec2-userでログイン
    - sudo su -
    - passwd
    - 新パスワードを設定
    - 「all authentication tokens updated successfully.」と表示されたらOK

### 作業用ユーザーを作成
- デフォルトのec2-user以外のユーザーを作成
    - su -
    - useadd user1
    - passwd user1
    - 「all authentication tokens updated successfully.」と表示されたらOK
- 作成ユーザーにsudo権限を与える
    - usermod -G wheel user1
    - visudo
        - 以下コメントアウトを外す
        - #%wheel ALL=(ALL) ALL  →  %wheel ALL=(ALL) ALL

### SSHポート番号変更
- sshのデフォルト22番から変更
    - vi /etc/ssh/sshd_config
        - port 22 → それ以外(2222とか）
- sshd再起動
    - service sshd restart
- 変更後のポートに対してsshログイン可能であることを確認

### セキュリティグループの設定
- HTTP,HTTPS,SSHのみ許可する
    - EC2管理コンソールにログイン
    - サブメニューのセキュリティグループからインスタンスで使用するグループを選択
    - Inbound上で以下を許可する
        - SSH(デフォルト22以外の変更後のポート番号)
        - HTTP(80)
        - HTTPS(443)
        - MySQL(3306)
      
### タイムゾーン変更
- 日本時間に変更する
    - date で現在の設定を確認
    - sudo cp -p /usr/share/zoneinfo/Japan /etc/localtime
    - date 日本時間になっている事を確認

### 日本語設定
- 日本語設定に変更
    - vi /etc/sysconfig/i18n
        - LANG=en_US.UTF-8 → LANG=ja_JP.UTF-8
       
### Fix更新
- yum update -y

### cloud-init用の事前環境設定
- EC2インスタンスコピー時に環境設定が変わってしまう問題を防ぐために以下を行います。
    - vi /etc/cloud/cloud.cfg
        - preserve_hostname:true　 ##再起動時にホスト名が変更されしまう現象を防ぐ
        - repo_upgrade: none
        - locale: ja_JP.UTF-8      ##ja_JP.UTF-8指定の場合
    - vi /etc/sysconfig/clock
        - ZONE="Asia/Tokyo"        ##Asia/Tokyo指定の場合
        - UTC = True

### メールアドレス設定
- 障害時の通知用メールアドレスを設定します。
    - vi /etc/aliases で以下を更新
        - #Person who should get root's mail  
            #root:          marc  
↓  
root:           通知用メールアドレス
    - 更新内容を反映させる
        - newaliases
    - テストメール送信
        - echo test|mail root
    - 指定したメールにテストメールが届いた事を確認

### 停止スケジュール設定  
- EC2は時間課金なので、使わない時間帯に自動停止してくれる仕組みを作ります
    - cronインストール
        - yum install crontabs -y
    - cron自動起動設定
        - chkconfig httpd on
        - chkconfig --list httpd でonになっているか確認
    - cron起動
        - service crond start
    - 自動停止スケジュール登録
        - crontab -e　　
        - 例）夜23:00の場合  
            - 00 23 * * * /sbin/shutdown -h now

## LAMP環境構築

### レポジトリ追加
- レポジトリとは、インターネット上にあるソフトウェアの倉庫のようなもの
- 登録しておくことで、より多くの種類、より新しいバージョンのソフトウェアをインストール出来たりします
- 主要レポジトリインストール(EPEL、REMI、RPMForge)  
※必ずEPEL→REMIの順でインストールする。
    - 各レポジトリダウンロード
        - wget https://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        - wget http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
        - wget http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el6.rf.i686.rpm
    - 各レポジトリをインストール
        - sudo rpm -Uvh epel-release-6-8.noarch.rpm
        - sudo rpm -Uvh remi-release-6.rpm
        - sudo rpm -Uvh rpmforge-release-0.5.3-1.el6.rf.i686.rpm
    - 各レポジトリインストール確認
        - rpm -qa | grep epel-release  
        「epel-release-6-8.noarch」が返ってくればOK
        - rpm -qa | grep remi-release  
        「remi-release-6.5-1.el6.remi.noarch」が返ってくればOK
        - rpm -qa | grep rpmforge-release  
        「rpmforge-release-0.5.3-1.el6.rf.x86_64」が返ってくればOK

### Apacheインストール、設定
- yum install httpd -y
- Apache起動、接続確認
    - service httpd start
    - ブラウザから以下URL接続でテストページが表示されたらOK  
    http://xxx.xxx.xxx.xxx　※IPアドレスを指定
- 導入バージョン確認
    - httpd -v
- Apache自動起動設定
    - sudo chkconfig httpd on
    - sudo chkconfig --list httpd でonになっているか確認
- Apache設定ファイルバックアップ(httpd.conf.org)
    - cp /etc/httpd/conf/httpd.conf /etc/httpd/conf/httpd.conf.org
- Apache設定ファイル編集
    - 最低限以下5点を編集
    - vi /etc/httpd/conf/httpd.conf

            - ブラウザからディレクトリ構造を表示できないようにする
                - Options Indexes FollowSymLinks → Options -Indexes FollowSymLinks
            - ファイルが見つからない時の「Forbidden」ページからOS情報を非表示にする
                - ServerTokens OS → ServerTokens Prod
            - ファイルが見つからない時の「Forbidden」ページからIPやApacheの情報を非表示にする。
                - ServerSignature On → ServerSignature Off
            - エラーが起きた時の通知先メールアドレス
                - ServerAdmin メールアドレス
            - 登録ホスト名
                - #ServerName www.example.com:80 → ServerName localhost:80
                
- Apache設定チェック
    - service httpd configtest
    - 「Syntax OK」と表示されればOK。
- httpd再起動
    - service httpd restart
- もう一度接続確認
    - ブラウザから以下接続でテストページが表示されたらOK
        - http://xxx.xxx.xxx.xxx　※IPアドレスを指定

### PHPインストール、設定
- PHPインストール
    - yum install php php-devel php-mysql php-gd php-mbstring -y --enablerepo=remi
- 導入バージョン確認
    - php -v
- PHP設定ファイルバックアップ
    - cp /etc/php.ini /etc/php.ini.org
- PHP設定ファイル編集
    - vi /etc/php.ini
    
            - 軽度の通知含め全てのエラーを表示するようにする
                - error_reporting = E_ALL & ~E_DEPRECATED → error_reporting = E_ALL | E_STRICT
            - ブラウザにエラーを画面に表示しない
                - display_errors = Off
                
            - PHPスタートアップ時のエラーを表示しない
                - display_startup_errors = Off

            - エラーをログを出力する
                - log_errors = On

            - エラーログを/var/log/php_errors.logに出力する
                - ;error_logs = php_erros.log →　error_logs = /var/log/php_errors.log
                
            - タイムゾーンを日本語に変更
                - ;date.timezone = → date.timezone = Asia/Tokyo

            - 日本語設定
                - ;mbstring.internal_encoding = EUC-JP → ;mbstring.internal_encoding = UTF-8

                - ;mbstring.language = Japanese → mbstring.language = Japanese

                - ;mbstring.http_input = auto → mbstring.http_input = auto

                - ;mbstring.detect_order = auto → mbstring.detect_order = auto
            - phpのバージョンや、php使用有無の情報を公開しないようにする
                - expose_php = On → expose_php = Off

### MySQLインストール、設定
- MySQLインストール
    - yum install -y http://dev.mysql.com/get/mysql-community-release-el6-5.noarch.rpm
    - yum install -y mysql mysql-devel mysql-server mysql-utilities
    - 上手くインストール出来ない場合は以下手順で関連モジュール削除後再度実施
        - yum remove mysql*
        - rpm -qaで依存関係をチェック
        - rpm -e 依存モジュール名  で削除
- 導入バージョン確認
        - mysql -V
- 自動起動設定
    - sudo chkconfig mysqld on
    - sudo chkconfig --list mysqld でonになっているか確認
- MySQLサーバー起動
    - service mysqld start
    - 失敗する場合は以下ログを確認
        - cat /var/log/mysqld.log
- MySQL初期設定
    - mysql_secure_installation
        - MySQL管理用rootパスワード設定
        - 匿名ユーザーを削除するか → Y
        - リモートからrootログインするか → Y
        - testデータベースを削除するか → n
        - 権限のリロードをするか → Y
- rootパスワード設定
    - mysql -u root -p　でブランクパスワードでログイン
    - SET PASSWORD FOR root@localhost=PASSWORD('パスワード');
    - exitでログアウト
    - 変更後のパスワードでrootログイン出来る事を確認
- MySQL設定ファイルバックアップ
    - cp /etc/my.cnf /etc/my.cnf.org

## 運用ツールインストール
### dstat（リソース状況確認コマンド）
- ロードアベレージ、CPU、Memory、disk、NetWork等を一覧でリアルタイムに見れるので便利。
- 各種ステータス確認コマンドのまとめ、一括管理のために作られたコマンドらしい
    - yum install dstat
    - dstat 実行でモニター画面が表示される事を確認
    - 各確認項目にaliasを貼っておくと便利  
    以下を参考にさせて頂きました。  
http://d.hatena.ne.jp/hirose31/20120229/1330501968を参照
        - vim /etc/profile にて末尾に以下を追加
            - alias dstat='dstat -tlcmgdrn'
            - alias dstat-cpu='dstat -tlcr'
            - alias dstat-mem='dstat -tlcm'
            - alias dstat-disk='dstat -tlcdr'
            - alias dstat-net='dstat -tlcnd'


### logwatch（ログ監視コマンド）
- 1日のログ監視結果のレポートを/etc/aliasesで指定したrootアカウントメールに送ってくれる
    - logwatchインストール
        - yum install logwatch
    - レポート送信テスト
        - logwatch --print
        - 指定したメールアドレスに送信されているか確認して下さい。


## 参考URL
- http://d.hatena.ne.jp/knaka20blue/20130821/1377056662
- http://dev.classmethod.jp/cloud/aws/start_stop_ec2_instances_by_jenkins/
- http://tweeeety.hateblo.jp/entry/20140211/1392128981
- http://infra.blog.shinobi.jp/Entry/85/
- http://d.hatena.ne.jp/home0/20111112/1321062630
- http://tanaka.sakura.ad.jp/2011/05/centos-linux-apache-php-perl-mysql-lamp.html
- http://kzy52.com/entry/2014/07/10/064230
- http://linuxserver.jp/%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0/php/%E3%83%9E%E3%83%AB%E3%83%81%E3%83%90%E3%82%A4%E3%83%88%E7%92%B0%E5%A2%83%E3%81%AE%E6%96%87%E5%AD%97%E5%8C%96%E3%81%91%E5%AF%BE%E7%AD%96.php
