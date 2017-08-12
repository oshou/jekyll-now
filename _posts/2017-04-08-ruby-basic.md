---
layout: post
title: Ruby基礎文法最速マスター(随時更新)
tags: 
- ruby
---

Rubyの基本文法、基礎知識をまとめてみました。  
随時更新。
<!-- more -->

## 必須資料
- **リファレンスマニュアル**
  - **https://docs.ruby-lang.org/ja/latest/doc/index.html**

## Rubyの特徴
- **Ruby言語 ミニマム**
  - **http://i.loveruby.net/ja/rhg/book/minimum.html**
- **Rubyの特徴**
  - **http://docs.ruby-lang.org/ja/latest/doc/spec=2fintro.html**
- 扱う対象全てがオブジェクト。
  - 文字列はStringクラスのメソッド、配列はArrayクラスのメソッド、、、等
- コンパイル言語と比較して型安全性が低い。
- 標準の組み込みライブラリが充実している
  - HTTPクライアントならnet/http、ベンチマークならbenchmark、ユニットテストならminitest、JSONならjson....etc
- ユーザによるメモリ管理が不要。不要となったオブジェクトはインタプリタに組み込みのGCが勝手に回収してくれる。
- モジュールによるMix-in機能を持つ。

## コーディング規約
- **https://shugo.net/ruby-codeconv/codeconv.html**
- **https://github.com/fortissimo1997/ruby-style-guide/blob/japanese/README.ja.md**

### ファイル
- **実行ファイルの拡張子が「.rb」**
- **エンコーディングはutf-8**
- **文末にセミコロン(;)を付けない**
- **演算子(+-*/={}等)の前後、カンマ(,)、コロン(:)、セミコロン(;)の後ろには1スペースを入れる**

### インデント
- **スペース2つ分、タブは使用しない。(環境によりタブ幅が異なるため)**

### 桁数
- 1行の桁数は最大80行まで

### 空行
- 複数のクラス定義がある場合、各クラス定義の間は1行あける

### コメント
```
# 単一行コメント

=begin
複数行コメント
複数行コメント
複数行コメント
複数行コメント
=end
```

## データ型
**オブジェクト型のみ**  
**一般的な文字列、配列等はすべてクラス扱い(Stringクラス、Arrayクラス等) **
```
# 文字列
# //Stringクラス
# //シングルクォーテーション、ダブルクォーテーションどちらかで囲む
str1 = "こんにちは"
str2 = 'こんばんは'

# 数値
# //Fixnumクラス
val = 1

# 配列
# //Arrayクラス
ary1 = [1,2,3,4,5]

# //インデックスは0からカウント
ary[0]
=> 1

# //未指定箇所はデフォルトでnil
ary[5]
=> nil

# ハッシュ(連想配列)
# //Hashクラス
# //文字列はkey,valueともに""で囲むこと
user1 = {"name"=>"sato","mail"=>"sato@hoge.co.jp"}

# //インデックス指定で要素参照
user1["name"]
=> "sato"

# 真偽値
# 偽はfalseとnilのみ
# それ以外は真。0も空白(" ")も真
```

### 命名規則
- ファイル名

```
# lower_snake_case
hello_world.rb
```

- ディレクトリ名

```
# lower_snake_case
lib/hello_world/hello_world.rb
```

- 定数

```
# UPPER_SNAKE_CASE
SAMPLE_CONSTANT = 10

# 組み込み定数
STDIN, STDOUT, STDERR, ARGV, ARGF, ENV, RUBY_VERSION...etc.
```

- 変数

```
# lower_snake_case
sample_variable = "sample"

# //文字列内の変数は#{変数名}で表現
puts "値は#{sample_variable}です"


# グローバル変数の場合は、先頭に$
$global_sample_variable = "global"
 
# インスタンス変数の場合は、先頭に@
@instance_sample_variable = "instance"
 
# クラス変数の場合は、先頭に@@
@@class_sample_variable = "class"
```

- クラス、モジュール名

```
# UpperCamelCaseを使う

class SampleClass
end

module SampleModule
end
```

- メソッド名

```
# lower_snake_caseを使う
def sample_method
end

# 真偽値を表すメソッドは、末尾に動詞または形容詞のあとに?をつける。is_は先頭につけない。
# // ○良い例
def visible?
end

# // x悪い例
def is_visible
end

def visible
end

# 破壊的なメソッドであれば、末尾に!をつける。
def sample_split!
end

# ただし、「!がなければ破壊的メソッドではない」わけではない。間違えやすいので注意。
# !付きでない破壊的メソッドは以下
# http://goo.gl/aM1Sci
def concat
end
```

### メソッド
- インスタンスメソッド
  - レシーバがインスタンスのメソッド
  - アクセサメソッド
    - インスタンス変数への参照、更新を行うためのインスタンスメソッド。
    - 以下3種類のみ存在する。
      - attr_accessor   　　#インスタンス変数の参照、更新が可能
      - attr_reader      　　#インスタンス変数の参照のみ可能
      - attr_writer       　　#インスタンス変数の更新のみ可能
  - initializeメソッド
    - オブジェクト生成後にインスタンスの初期化を行うためのインスタンスメソッド。定義しなくても可(初期化されないだけ)
    - コンストラクタ
- クラスメソッド
  - クラスをレシーバとして呼び出せるメソッド
  - newメソッド
    - オブジェクト生成のためのクラスメソッド。生成後にインスタンスメソッドinitializeで初期化される。
- モジュールメソッド
- 特異メソッド

## 演算子
- 基本演算
  - puts 7+5  =>12
  - puts 7*5   =>35
  - puts 7/5   =>1　#商
  - puts 7%5 =>2  #余り
- 代入演算
  - 多重代入
    - a,b = 1,2
  - 自己代入
    - 以下は同じ意味
      - num = num + 1
      - num += 1
    - 以下は同じ意味
      - b ||= 2
      - b = b || 2
  - %記法
      - %w(Alice Bob Chrlie) # => ["Alice", "Bob", "Chrlie"]
- 論理演算子
  - && かつ
  - || または


## 制御構造
- ブロック
  - 基本的にdo...endを使用する。
- 条件分岐
  - if～elseif～else end
  - unless ～
  - case           #if文は上から舐めていくがcaseは目的の箇所にジャンプできるので早い。
- ループ
  - for i in 1..100 do...
  - while xxxx do...
  - オブジェクト名.each do |value|

## 主な組み込みイブラリ
- 組み込みライブラリ一覧(require不要)
  - http://docs.ruby-lang.org/ja/2.1.0/library/_builtin.html
- Kernel
  - 出力
    - print #改行なし
    - puts  #改行あり
    - sprintf
  - 入力
    - gets
- 文字列(String)クラス
  - 文字列連結
    - str1+str2
  - 文字列末尾追加
    - str1.concat(str2)
    - str1 << str2
  - 文字列置換（正規表現で指定した条件を抽出）
    - str.gsub(/at/,"@")   #gsubは該当する全ての要素を置換する(atatatなら@@@に変換)
    - str.sub(/at/,"@")     #subは最初の要素だけ置換する(atatatなら@atatに変換)
  - 末尾改行文字を除外（特定文字の除外も可能）
    - str.chomp
    - str.chomp(":") #:で改行する場合
  - 大文字、小文字変換
    - "abcde".upcase   　　　 #=> "ABCDE"
    - "ABCDE".downcase　　 #=> "abcde
  - 指定文字列で文字列全体を分割、配列にする
    - "Alice Bob Charlie".split(" ")   #=> ["Alice", "Bob", "Charlie" ]
    - "Alice,Bob,Charlie".split(",")   #=> ["Alice", "Bob", "Charlie" ]
  - 指定した文字列を含んでいるか確認
    - "abcde".include?("bc")   #=> true
    - "abcde".include?("zy")   #=> false
  - 指定した文字列を含んでいれば、その開始位置を返す、存在しなければnilを返す
    - "abcde".index("a")    # index=0
    - "abcde".index("c")    # index=2
    - "abcde".index("abc") # index=0
    - "abcde".index("z")    # index=nil
  - 文字列の一部抽出
    - "abcde"[0]               #=> a
    - "abcde"[0,3]           #=> abc      ※index0から3文字目まで
- 配列(Array)クラス
  - 配列の要素数を確認
    - [].length         #=> 0
    - [1,2,3].length #=> 3
    - [1,2,3].size     #=> 3
  - 文字列への変換
    - ary.to_s
  - 配列要素の中で最大値、最小値を求める
    - [1,3,7,2,5].max   #=> 7
    - [1,3,7,2,5].min    #=> 1
  - 配列の先頭から削除、追加
    - ary = [1,3,7,2,5]
    - ary.shift               #=> 1               ※引数指定しない場合は、先頭から1文字取得&配列から除外
    - ary                     #=> [3,7,2,5]
    - ary.shift(2)            #=> [3,7]           ※引数指定する場合は、先頭から引数分だけ取得&配列から除外
    - ary                     #=> [2,5]
    - ary.unshift(1)          #=> [1,2,5]
  - 配列の末尾から削除、追加
    - ary = [1,3,7,2,5]
    - ary.pop                 #=> 5                 ※引数指定の場合は末尾から指定要素数分だけ取得、配列からは削除
    - ary                     #=> [1,3,7,2]
    - ary.pop(2)              #=> [7,2]               ※引数指定の場合は末尾から指定要素数分だけ取得、配列からは削除
    - ary                     #=> [1,3]
    - ary.push(8)             #=> [1,3,8]
  - 配列が空かどうかの確認
    - [].empty?         #=> true
    - [1,2,3].empty? #=> false
  - 配列要素の中で、指定した特定要素を含むか確認
    - [1,2,3].include?(1)    #=> true
    - [1,2,3].include?(10)  #=> false
  - 末尾に要素追加
    - [1,2,3] << 4    #=> [1,2,3,4]
  - 先頭に要素追加
    - [1,2,3] .unshift(0)  #=>[0,1,2,3]
  - 先頭要素の削除
    - ary=[0,1,2,3]
    - ary.shift  #=> 0
    - ary  #=>[1,2,3]
      - [1,2,3] .unshift(0)  #=>[0,1,2,3]
  - ランダムに要素取得
    - [1,3,7,2,5].sample   #=> 5  ※結果は毎回ランダムで選ばれる。
  - 配列の複製
    - tmp = self.dup
  - 指定したインデックスの要素の削除 ※破壊的メソッド
    - ary = [0,1,2,3]
    - ary.delete_at(2) =>2
    - ary   #=> [0,1,3]
- 数値(Fixnum)クラス
  - 絶対値を取得
    - (-1).abs          #=> 1
- 浮動小数値(Float)
  - 小数値を特定桁で四捨五入
    - (2.58926).round(2)  #=> 2.59
  - 少数値を特定行で切り上
    - (2.42).ceil
- IO
  - io#gets

## ライブラリ管理の基礎
- gem
  - Ruby用ライブラリの管理方式。
  - pythonのpip,PHPのpear or composer,javaのmaven,perlのcpan
- Rubygems
  - ライブラリのインストール元となる共有レポジトリ。
- rbenv，rvm
  - Ruby本体のバージョン管理システム。ライブラリはBundlerが担当するので区別して覚える。
  - 複数のRubyバージョンの管理、切換えが可能。
- Bundler
  - Ruby用ライブラリのバージョン管理システム。
  - 必要なライブラリ同士で互換性に問題が無いようにインストールしてくれる。
http://komaken.me/blog/2013/07/05/%E9%A0%AD%E3%81%8C%E5%BC%B1%E3%81%99%E3%81%8E%E3%81%A6ruby-rbenv-gem-bundle-rails%E3%81%AE%E4%BB%95%E7%B5%84%E3%81%BF%E3%81%8C%E7%90%86%E8%A7%A3%E3%81%A7%E3%81%8D%E3%81%AA%E3%81%84%E3%83%BB/

## 環境設定
- Ruby本体のインストール
  - 複数のrubyバージョンを管理する場合
    - rbenv,ruby-buildのインストール
  - rubyのインストール
    - rbenv versions //現状のrubyバージョン確認
    - rbenv install xxxxxx //指定バージョンをインストール
    - rbenv global xxxxxx //デフォルトバージョンをインストールしたものに変更
    - rbenv version //デフォルトバージョンが指定された事を確認
- Ruby用ライブラリのインストール
  - Bundlerインストール
  - Gemfileに記載して各ライブラリをbundle install