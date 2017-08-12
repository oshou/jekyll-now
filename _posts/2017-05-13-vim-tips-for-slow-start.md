---
layout: post
title: Vimの起動が遅い時のチェックポイント
tags: 
- vim
---
Vim起動が遅い時の原因特定に役立ったので備忘録
  
<!-- more -->
以下コマンドで所要時間をファイルに書き出す(fooは適当なファイル名でOK）
> $ vim --startuptime foo

出力結果を確認
> $ cat foo

以下のような形で各所要時間をファイルに書き出してくれる
> 1
 2
 3 times in msec
 4  clock   self+sourced   self:  sourced script
 5  clock   elapsed:              other lines
 6
 7 000.003  000.003: --- VIM STARTING ---
 8 000.071  000.068: Allocated generic buffers
 9 000.134  000.063: locale set
10 000.138  000.004: window checked
11 000.660  000.522: inits 1
12 000.667  000.007: parsing arguments
13 000.668  000.001: expanding arguments
14 000.676  000.008: shell init
15 000.918  000.242: Termcap init
16 000.953  000.035: inits 2
17 001.008  000.055: init highlight
18 001.662  000.225  000.225: sourcing /usr/share/vim/vim72/syntax/syncolor.vim
19 001.787  000.408  000.183: sourcing /usr/share/vim/vim72/syntax/synload.vim
20 016.369  014.532  014.532: sourcing /usr/share/vim/vim72/filetype.vim
21 016.411  015.107  000.167: sourcing /usr/share/vim/vim72/syntax/syntax.vim
22 016.469  000.012  000.012: sourcing /usr/share/vim/vim72/filetype.vim
23 016.562  000.055  000.055: sourcing /usr/share/vim/vim72/ftplugin.vim

.vimrc内の各設定にかかった時間を知りたい場合は、以下NeoBundleプラグインを導入
> NeoBundle 'git://github.com/mattn/benchvimrc-vim.git'

その後、Vimを再起動してプラグインインストール
> :NeoBundleInstall

Vim画面より.vimrcを開いて以下を実行
> :BenchVimrc

各設定毎にかかった時間が表示されます。
>41   0.000008 00041: set tabstop=4
42   0.000008 00042: set softtabstop=4
43   0.000007 00043: set shiftwidth=4
44   0.000014 00044: set expandtab
45   0.000009 00045: set autoindent
46   0.000009 00046: set backspace=indent,eol,start
47   0.000008 00047: set wrapscan
48   0.000008 00048: set showmatch
49   0.000007 00049: set wildmenu
50   0.000009 00050: set formatoptions+=mM
51            00051:
52   0.000008 00052: set nobackup
53   0.000010 00053: set noswapfile
54            00054:
55            00055: "ファイル形式検出、プラグイン、インデントをO
56   0.039253 00056: syntax on
57   0.000220 00057: filetype plugin indent on

## 参考
* http://mattn.kaoriya.net/software/vim/20120315221158.htm
* http://blog.takuma7.com/blog/2014/01/02/vim-setting/




 
