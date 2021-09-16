# 振り仮名注釈コーパス（青空文庫コーパス）
青空文庫及びサピエの点字データから作成した振り仮名のデータセット

## 1.概要
振り仮名注釈コーパス（青空文庫コーパス）（以下、「本コーパス」）は、
青空文庫の公開作品のテキストデータと、
社会福祉法人日本点字図書館がシステムを管理し、全国視覚障害者情報提供施設協会が運営している、視覚障害者情報総合ネットワーク「サピエ」が視覚障害者に提供している点字データの二つを材料として、
青空文庫の分かち書きを行い、その漢字に点字データから作成した振り仮名を付与したものです。
注釈データ中に登場する全ての漢字に振り仮名を付与してあります。大量のデータを必要とする機械学習等への利用を想定しています。

## 2.データについて
zipで圧縮されており、次のURLから取得可能です。

https://lab.ndl.go.jp/dataset/huriganacorpus/aozora_dataset.zip

メタデータは本リポジトリのaozora_metadata.csvを参照してください。


### 2.1. データの詳細
青空文庫の入力文字数に対して何らかの注釈（アノテーション）を付ける事ができた文字数との割合を収集率と定義したとき、
収集率90%以上を満たす作品の注釈コーパスデータ(タブ区切りtxt形式、UTF-8)と、作品情報等を記載したメタデータ（csv形式、UTF-8）を公開しています。
収集率が100%とならない理由には、点字データにのみ存在する注記や両者の表現の違いにより、対応付けがうまくいかなかったため等があります。

作品数は1944点です。

### 2.2. フォルダ構成
次に例示する通り、著者ごと作品ごとに階層構造を有しています。

```
.
├── マン_パウル・トーマス
│   ├── トリスタン/
│   │   └── tristan.txt
│   ├── ルイスヒェン/
│   │   └── luischen.txt
│   ├── トニオクレエゲル/
│   │   └── tonio.txt
│   ├── 予言者の家で/
│   │   └── yogenshano.txt

```
### 2.3. 注釈コーパスデータの形式
行番号を示した行の後、入力文全体の行、入力文全体の読みの行が続き、
その後に分かち書きされた要素ごとに、「入力文の一部」「振り仮名」「注釈対象の性質（漢字、ひらがな、記号）」が改行区切りで入力文末尾まで記述されています。

下記は「吾輩は猫である」の一文「名前はまだ無い。」の箇所の注釈コーパスデータの抜粋です。
|行番号: 3| | |
----|----|---- 
|名前はまだ無い。| 		| [入力文] |
|	|なまえわ まだ ない。|	[入力 読み]|
| 名前 | なまえ | 漢字|
|は |	わ |	ひらがな|
|　　|　　 	|分かち書き|
|まだ	|まだ	|ひらがな|
|	|	|分かち書き|
|無	|な|	漢字|
|い	|い|	ひらがな|
|。|	。|	記号|

### 2.4. メタデータの形式
「著者名」「作品名」「入力文字数」「収集率」「不一致のあった行数」がカンマ区切りで記述されています。


### 2.5. 作成手順の概要
分かち書きされた仮名の点字データを活用して、機械学習に基づく読み推定モデルの学習に必要となる振り仮名付きの日本語コーパスを構築しました。
青空文庫のテキストと該当する点字のデータから、対応する文のペアをパターンマッチングで選び出します。
次に、この文対に対して、既存の振り仮名注釈付きコーパスや形態素解析辞書(※)などから事前に収集した漢字に対する振り仮名候補に基づく文字レベルのマッチングを行い、振り仮名注釈付き日本語コーパスを構築しました。
なお、青空文庫と点字の文のマッチングは、下記の方法で行いました。
1. 点字バイナリーデータをテキストに変換
2. 青空文庫の前処理
　外字を包摂し、注記・ルビ・等を削除したテキストを作成
  同時にレイアウト・ルビの情報を得る
3. 点字データの前処理
点字データの表紙、目次、注記、注解、奥付を除去
4. 目次とミダシを利用して、青空文庫と点字の本の対応を行う
5. 青空文庫を形態素解析により、仮名に変換
6. レーベンシュタイン距離で青空文庫と点字のペアのカナの文を作成
7. 最終的に、青空文庫の漢字かな交じり文と点字のペアの文を作成

※ 下記の形態素解析辞書の「表層」と「読み」を使用しています。
1. MeCabのIPA辞書
http://taku910.github.io/mecab/
2. MeCab-ipadic-NEologdの辞書
https://github.com/neologd/mecab-ipadic-neologd
3. 国語研究所の現代書き言葉UniDicと現代話し言葉UniDicの辞書
https://ccd.ninjal.ac.jp/unidic/download
4. sudachiの辞書
https://github.com/WorksApplications/Sudachi


詳細は参考文献に記載の論文を参照してください。


## 3.注意点等

・点字の仮名は、話言葉の発声を前提に作成されます。
（例：「当時」→「とーじ」、「麹町」→「こーじまち」、「名前はまだない」→「なまえわまだない」）
本コーパスにおける振り仮名の記載もこれに準じています。
通常の振り仮名に変換したい場合には追加の置換等が必要です。ご注意ください。

点字における仮名入力の詳細は、下記をご参照ください。
『点訳のてびき 第4版』（特定非営利活動法人 全国視覚障害者情報提供施設協会、2019年2月 発行）


・注釈コーパスデータには旧字体が含まれていますので、テキストエディタによっては、正しく文字が表示されなかったり、編集すると文字化けを起こすことがあります。

・青空文庫と点字データで注釈対象の記載が異なる時は、青空文庫の表記を原則採用しています。

・本コーパスにおける振り仮名が正確でない場合があります。
自作ツールで正確性のチェックを行っていますが、
例えば「金田一」の振り仮名が「きんんだいち」でも、正しく振り仮名注釈ができたと判断する場合があります。
また、日本語では「川」を「かわ」「がわ」と読んだり、「匹」を「ひき」「ぴき」と読んだりするため、あいまいな文字一致を行っています。
このため「神奈川」が「かなかわ」でも正しく振り仮名注釈ができたと判断しています。
青空文庫と点字の記号については、一致しないことがあります。

## 4. 背景
視覚障碍者の情報障害の改善を目指して本研究を開始しました。
重度視覚障害者は、パソコンで画面読み上げソフトを使用して、漢字交じりの文書を音声で聞いていますが、
しばしば発生する読み上げの誤りに悩まされています。
例えば、「表に出る」を「ひょうにでる」と読み上げられると理解が困難になります。
こうした誤りの訂正には機械学習による自然言語処理が有効と考えられますが、教師データとして
漢字に正しい振り仮名がつけられた大量のデータが必要なため、まずコーパスの作成に取り組みました。

## 5. 謝辞
本コーパスの作成と公開にあたっては、全国視覚障害者情報提供施設協会及び日本点字図書館のご理解とご協力を賜りました。この場を借りて御礼申し上げます。


## 6. 参考文献
佐藤文一, 吉永直樹, and 喜連川優. 
"書誌データ・青空文庫・点字データを用いた振り仮名注釈付き日本語コーパスの構築."
情報処理学会第15回アクセシビリティ研究会 研究報告 2021年3月

佐藤文一, 喜連川優. "事前学習済み BERT の単語埋め込みベクトルによる同形異音語の読み誤りの改善
情報処理学会第12回アクセシビリティ研究会 研究報告 2020年3月

このコーパスを利用して研究等を実施する場合には、上記の参考文献の論文を引用頂けますと幸いです。

## 7. 本件に関する問い合わせ先
lab@ndl.go.jp
何かお気づきの点がありましたら、お気軽にお問い合わせください。
