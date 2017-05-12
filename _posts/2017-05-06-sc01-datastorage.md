---
layout: post
title: コンピュータサイエンス基礎まとめ(データストレージ)
---
普段扱っている技術の前提知識や、H/W、OS周りの低レイヤーの理解が不十分だなーと常々感じていた所、「入門 コンピュータ科学」という非常に良い本を見つけたのでまとめ。  
https://goo.gl/Lfhkbp  
<!-- more -->
  
全米の有名大学で定番教科書として使われているそう。  
コンピュータサイエンス周りを勉強したい！でも敷居が高くてまず何から始めれば良いのかわからない、といった人には、
内容も網羅的でこれを土台に深掘りできそうな良い教材だったのでおすすめ。  
    
勉強メモとして今回は1章のデータストレージからまとめ。  
所々意味的にまとめたり、出てこなかったキーワードの補足や、他サイトから情報引っ張ってきたり等しています。

## テーマ
現在のコンピュータでは情報はどのように表現され、格納されるのか

## 情報の表現、格納の仕組み
### すべての情報はビットパターンで表現される
- ビットとは
  - **0か1の値を持つ2進数1桁。情報の最小単位。**
  - **一般にn桁のビットがあれば、2^nビットの情報を表現できる。**
  - =binary digit、バイナリ、bit、ビット
- **テキスト、数字、プログラム、音声データ、画像データ等のあらゆる情報はすべてビットパターン(複数桁bitの組み合わせ)で表現される。**
- **可読性向上のためあくまで表現上は16進数が使われる事が多い。**
  - 2進数は、1桁が0,1の2種類で構成される。
  - 16進数は、1桁が0～Fの16種類で構成される。  
16進数(hexa-decimal)、略してヘキサコード等と表される事が多い。

### なぜ情報はビットパターン(2進数)で表現されるのか
- **コンピュータ内の電子回路が2進数だとシンプルかつ効率的に設計できるため**
  - **半導体や真空管の場合は、電圧の高低を 0,1で表現**
  - **磁気記憶装置の場合は、磁化された方向 を 0,1で表現**

### どのようにビットパターン(2進数)の組み合わせを表現するか
ブール演算を利用する。
- **ブール演算**
  - =ブール演算、二項演算、BinaryOperation
  - 1(true) or 0(false)のいずれかの入力に対して1つの値を出力する演算。
  - コンピュータの情報格納やRDBの原理、プログラムのビット演算等に利用される。
  - ブール演算は以下4つで構成される。
    - AND(AかつB)
    - OR(AまたはB)
    - XOR(AまたはBだが、AかつBではない)
    - NOT(Aではない)

### ビットパターン格納の仕組み、H/W実装
ビットパターンで表現された情報は以下のように電子回路上に格納される。
- ゲート
  - **1(true) or 0(false)いずれかの入力に対して、1つの値を出力する基本的な電子回路部品。**
  - 0,1を電圧の高低で表現する。
  - 主に以下が存在する。
    - AND -->ANDゲート
    - OR --> ORゲート
    - XOR --> XORゲート
    - NOT --> インバータ
- フリップフロップ
  - **1bit分の情報を格納する事を目的とした基本的な電子回路。**
  - **コンピュータのメインメモリ、キャッシュメモリ、レジスタ等に応用される。**
  - ゲートの組み合わせで構成される。
  - 以下特徴を持つ。
    - 2つの入力に対して2つの値を出力し、いずれかの出力が正となる。
    - 特定種類の入力(セット入力,リセット入力)があるまでは、値を保持する。  
http://www.way-on.com.tw/PCbasal/kiso/memory1.htm

## メモリ
- **=主記憶装置、一時記憶装置**
- **長所は処理が高速、短所は揮発性・低容量**
- **フリップフロップに類似した回路を大量に持つ。**
- **管理上「セル」と呼ばれる単位で構成される。1セルは1byte=8bit。**
- **各セルにはアドレスがあり、アドレス指定で順不同にアクセス出来る。  
この性質は「RAM(=RandomAccessMemory)」と呼ばれ、マスストレージと対比される。**
- 揮発性(電源供給が切れると情報も失われる)という性質から「DRAM(=Dynamic Rancom Access Memory)」とも呼ばれる。

## マスストレージ
- **=補助記憶装置、2次記憶装置、MassStorage**
- **長所は不揮発性・大容量・低価格、短所は処理が低速**
- **磁気システム、光学システム、フラッシュドライブ等の種類がある(後述)。**
- **マスストレージ内の情報は、まずファイル単位でグループ化され、更にブロック単位で読み書きが行われる。**
- ファイルは更に以下の観点で細かくグループ化される。  
  - 物理レコード
    - マスストレージの物理性質に合わせて分割したブロック
  - 論理レコード
    - 全従業員情報が1人1人の従業員情報から構成されるように内容から自然と分割されるブロック
- **物理レコードと論理レコードが一致する事はほぼないため、メモリとマスストレージ間のやりとりは物理レコードの倍数のデータ転送を行い、メインメモリ等にあるデータは論理レコードの形式で参照する。**
- 上記のやりとりで使用されるメモリ領域を一般にバッファと呼ぶ。

### 磁気システム
- **例)磁気ディスク、磁気テープ等**
- **ディスクを回転させ、読み書きヘッドを指定トラックまで移動、トラック上のセクタへのアクセスを行う。**
- 各セクタには連続したビットパターンが記録されている。
- フォーマットにより既存ビットパターンの消去・初期化が出来る。
- 容量は、磁気ディスク枚数、トラック数、セクタ密度等によって決まる。
- 時間は、シーク時間(ヘッドの移動時間)＋レイテンシ時間(トラック合わせた上で同円上の目的データまでたどり着く時間)で求められる。

### 光学ディスク
- **例)CD、DVD、BD等**
- **ディスクを回転させ、レーザー照射によりCD表面の反射材の表面に変調を施されている不規則性を読み取る。**

### フラッシュドライブ
- **例)Flashメモリ、Flashドライブ、SDメモリ(SD,SDHC,SDXC)等**
- 電気信号をストレージに直接送りビットパターンを記録するため、物理操作が発生しない。
- 便利だが、2017.04時点の技術ではブロック単位でしかアクセス出来ず、データの信頼性に欠ける(変更回数により格納域の劣化が発生しやすい)


## 様々な情報とビットパターンとの対応
- 様々な情報がどのようにビットパターンとして表現されるか(符号化と呼ばれる)
- 情報としては、主にテキスト、数値(整数、小数)、画像、音声等の種類がある。

### テキスト
- 文字コード
  - テキスト文字<-->ビットパターンの対応ルール。
  - 文字コードは一般に「文字集合」と「文字符号化方式」という2つの要素で構成される。
- 文字集合
  - **「どのテキスト文字を扱うか？」の定義**
  - 例)ASCII、JIS X 0201、JIS X 0208、JIS X 0213、Unicode等
    - ASCIIは文字コード乱立を避けるためにANSI(アメリカ規格協会)によって作られた、最初の標準統一規格。  
7bitコードで2^7=128文字を表現可能。
英数字(A-Z、a-z、0-9)、一部記号(!@#$%等)等を扱う。
    - アルファベット圏はASCIIがメインだが、カタカナ、ひらがな、常用漢字等を考えると日本では「JIS X xxxx」系が必要
    - 世界中の文字を網羅した統一的な文字集合がUnicode。普及&整備途中だが将来的に主力となる予定。
- 文字符号化方式
  - **「それぞれのテキスト文字をどのビットパターンに割り振るか？」の定義**
  - **例)EUC-JP、Shift-JIS(cp932もShift-JIS亜種)、UTF-8、UTF-16等**
    - UNIX/LinuxではEUC-JP(Extended UNIX Code Format for Japanese)がメイン。
    - WindowsではShift-JIS亜種のcp932がメイン
    - Unicode対応のためUTF-8(Unicode Transformation Format-8)、UTF-16(Unicode Transformation Format-16)の普及が進んでいる。
- テキストには2種類あるので要注意。
  - テキストファイル
    - 文字情報のみを保管する。
    - 例).txt等
  - リッチテキストファイル
    - 文字情報 + 装飾情報(文字色、フォント、太字等)を保管する。
    - 例).doc、.pdf、.rtf等

### 整数(正負)
以下2通りの方法がある。
- 2の補数
  - 最左端ビットを符号ビットとする。
  - 補数はある整数のビットパターンの最右端のビットを除く全ビットを反転させた値の事。
- エクセス記法

### 小数
ビットパターンの途中で基準点(=小数点)を設定し、
基準点より上の値を2^1～、基準点より下を、～2^(-1)で表現する。
- 単精度浮動小数点
- 倍精度浮動小数点

### 画像
大別すると、ビットマップ形式とベクター形式のいずれかで表現される。
- ビットマップ(ラスタ)
  - 例).jpg、.gif、.png、.tiff、.bmp、.pct
  - 画像をビットマップ=点(pixel,画素)の集まりとして表現する。
  - 複雑な画像を表現するのが得意だが、拡大縮小に弱い。(拡大でピクセルが大きくなり画像が粗くなってしまう)
  - 白黒画像の場合
    - 1ピクセル1ビット(1bitで白or黒を表現)
    - 1ピクセル8ビット(8bitで白黒の濃淡を表現)
  - カラー画像の場合
    - 1ピクセル24ビット(RGBそれぞれ8bitで濃淡を表現)
    - 1ピクセル24ビット(明度、赤色クロミナンス、青色クロミナンスで濃淡を表現)
- ベクター
  - 例).eps
  - 画像を数式を使って描画する。
  - シンプルな画像を表現したり拡大縮小の劣化を防ぐ事が出来るが、複雑な画像の表現は不得意。
  - 例)始点、終点の座標、色、太さを指定して線を引く、等。

### 音声
一定間隔で音波の振れ幅を標本採取する。
現在の音楽用CDでは44,100回/s


## データ圧縮
以下2通りあり。
- ロス無し圧縮
- ロス有り圧縮

## 参考
- 符号化文字集合と文字符号化方式の違い
  - https://goo.gl/HLILDX
- Unicodeは文字集合か符号化方式か
  - https://goo.gl/dbdW