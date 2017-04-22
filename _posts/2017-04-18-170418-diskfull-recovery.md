---
layout: post
title: ディスク容量がいっぱいの時にやる事
---
Qiitaから転載ですが、ディスク容量がいっぱいの時にすぐ出来る削減手順をまとめました。
<!-- more -->

TreasureDataログイン後の左メニューから「Admin」アイコンをクリック  

## 現在のディスク容量の確認
### 全体のディスク容量を確認
- **df -h**
```
[root@ip-xxx-xxx-xxx-xxx ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1      6.0G  4.1G  1.6G  73% /
tmpfs           498M     0  498M   0% /dev/shm
```

### ディスク容量の内訳を確認
- **du -sh [容量内訳を確認したいディレクトリのパス]**
```
例) du -sh /\*　(ルート直下）    
例) du -sh ./\*　(カレントディレクトリ直下）  
例) du -sh /var/\*　(varディレクトリ直下）
例) du -sh /\home -x  (homeディレクトリ直下でマウントポイントある場合除外する)

root@ip-xxx-xxx-xxx-xxx ~]# du -sh /
7.8M    /bin
47M     /boot
4.0K    /cgroup
140K    /dev
28M     /etc
104K    /home
253M    /lib
26M     /lib64
16K     /lost+found
4.0K    /media
0       /misc
4.0K    /mnt
0       /net
8.0K    /opt
0       /proc
16M     /root
15M     /sbin
0       /selinux
4.0K    /srv
0       /sys
204K    /tmp
2.3G    /usr
1.3G    /var
```

## 複数階層をまとめて見たい場合
- **du -h --max-depth [階層数] [容量内訳を確認したいディレクトリのパス]**   
```
例) du -h --max-depth 2 /\*　(ルート直下)
```


## 不要データの削除
### yumインストールデータのキャッシュ削除
- **du -sh /var/cache/yum（ディスク使用容量を確認）**
- **yum clean all(すべてのキャッシュを削除）**

### tmpデータの削除
- 削除可能と判断出来るものは削除する。
- 保管間隔を狭めたい場合は以下の時間を編集する  
**/etc/cron.daily/tmpwatch**  
```
例）/var/tmp内のファイルで720時間アクセス無しのものは削除  
　　/usr/sbin/tmpwatch 720 /var/tmp
```
