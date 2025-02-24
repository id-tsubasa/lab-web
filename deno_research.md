# Denoの外部モジュールについての実証分析

---

JavaScriptやTypeScriptの実行環境であるDenoの特長として、実装したい機能に合った外部モジュールをインポートして、システム構築を行う事が挙げられる。様々な開発が行われる昨今、どの言語においても使用するライブラリのバージョン管理が大事になってくるが、私たちは最近エコシステムが成長してきているDenoに着目し、Denoにおけるモジュールの*依存関係*に焦点を当てて現状を分析調査している。

## 分析データの収集

---

7月8日時点でdeno.landに4811個のライブラリが公開されており、それら全ての元ファイルにあるimport文を取り出すことで、インポートされている外部モジュールの情報を得た。\
1．Denoで公開されているモジュールをダウンロード\
2．import文をimportしているモジュールとimportされているモジュールに分けてcsvファイルに書き出す\
以下の様なcsvファイルを作成した

| importするファイル                                       | importされるファイル                                      |
| -------------------------------------------------- | -------------------------------------------------- |
| https://deno.land/x/jsonlines@v1.2.1/src/parser.ts | https://deno.land/std@0.138.0/streams/delimiter.ts |
| https://deno.land/x/jsonlines@v1.2.1/src/parser.ts | ./utils.ts                                         |
| ...                                                | ...                                                |

### 調査内容1

---

`-Denoの外部モジュールの組み合わせについての実証分析-`\
多くの開発では複数のモジュールを組み合わせた開発が行われている。その際にモジュールが最新のものである事が好ましいが、どちらかのバージョンが変更されると、もう一方のモジュールが対応しないエラーが生じてくるケースがある。そこで、Denoにおける外部モジュールの組み合わせとバージョン定義の差異について実態を現在調査している。

### 調査内容2

---

`現在公開されている約4000のモジュールがインポートする外部モジュールについて分析`

外部モジュールのバージョン管理を怠る(バージョンが定義されていない、バージョンが古い)と、\
・新しい事象に対応できない\
・エラーに対応できない　という問題が発生する。\
これらの事象を減らすために分析を行った

#### **データの分析手法**

1.importされているモジュールの中でhttpsから始まるもの（外部モジュール）に絞り込む\
2.1で絞り込んだ外部モジュールの中でバージョン定義されているかを「x.x.x」（xは0～9の数字）が文字列に含まれるかで絞る\
3.それぞれの外部モジュールで、「バージョン定義されている数」、「バージョン定義されていない数」、「importされている数の合計」、\
「バージョン定義されている割合」、「バージョンの分布」の項目に分けて表にした\
4.3で作成した表の各項目を分かりやすくするためにグラフ化した

| importされたもの                              | バージョン定義されている | バージョン定義されていない数 | importされた合計数 | バージョン定義されている割合 | バージョンの分布                                                                                                      |
| ---------------------------------------- | ------------ | -------------- | ------------ | -------------- | ------------------------------------------------------------------------------------------------------------- |
| https://deno.land/std/testing/asserts.ts | 2582         | 602            | 3184         | 81.09          | [('0.94.0', 1), ('0.54.0', 1), ('0.34.0', 1), ('0.72.0', 1), ('0.52.0', 2), ('0.55.0', 2), ('0.50.0', 2),...] |
| https://deno.land/std/path/mod.ts        | 984          | 142            | 1126         | 87.39          | [('0.67.0', 1), ('0.68.0', 1), ('0.111.0', 1), ('0.96.0', 1), ('0.109.0', 1), ('0.60.0', 1),...]              |
| ...                                      | ...          | ...            | ...          | ...            | ...                                                                                                           |

## 関連

- [Dependency hell](https://en.wikipedia.org/wiki/Dependency_hell) (Wikipedia)

## 発表

- [FOSE2022](https://fose.jssst.or.jp/fose2022/)でポスター発表します。

[ホームに戻る](./README.md)
