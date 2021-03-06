---
title: Droongaプラグイン開発チュートリアル
layout: ja
---

{% comment %}
##############################################
  THIS FILE IS AUTOMATICALLY GENERATED FROM
  "_po/ja/tutorial/1.0.6/plugin-development/index.po"
  DO NOT EDIT THIS FILE MANUALLY!
##############################################
{% endcomment %}


* TOC
{:toc}

## チュートリアルのゴール

Droongaプラグインの作り方を理解します。
[基本的な使い方のチュートリアル][basic tutorial]を完了している必要があります。


## プラグインとは

プラグインはDroongaの中でもっとも重要なコンセプトの一つです。
プラグインがDroongaを柔軟なものにしています。

多くの現実的なデータ処理タスクでは、問題に応じたデータの取り扱いが必要です。これを汎用の方法で解決するのは簡単ではありせん。

 * 外部のシステムと連携するために、入力のリクエスト形式を変更する必要があるかもしれません。外部のシステムが理解できるような形式で出力するために、出力を加工する必要があるかもしれません。
 * Droongaが標準で提供する機能よりもさらに複雑なデータ操作を、ストレージに直接アクセスしながら効率よく行う必要があるかもしれません。
 * Droongaのデータ分散と回収ロジックをコントロールすることで、Droongaの分散性能を活かした高度なアプリケーションを構築する必要があるかもしれません。

このような場合にプラグインを利用することができます。

## Droongaエンジンにおけるプラガブルな操作

Droonga Engineにはプラガブルなフェーズが大きく分けて2つあり、そのうちの1つは3つのサブフェーズから構成されます。プラグインの観点からすると、都合1つから4つの操作に処理を追加することができます。

アダプション・フェーズ
: このフェーズでは、プラグインは入力のリクエストと出力のレスポンスを加工できます。

プロセッシング・フェーズ
: このフェーズでは、プラグインはそれぞれのボリューム上で入力のリクエストを逐次処理します。

プロセッシング・フェーズには3つのプラガブルなサブフェーズがあります:

ハンドリング・フェーズ
: このフェーズでは、プラグインは低レベルのデータ処理、たとえばデータベース操作などを行うことができます。

プランニング・フェーズ
: このフェーズでは、プラグインは入力のリクエストを複数のステップに分割できます。

コレクション・フェーズ
: このフェーズでは、プラグインはステップから得られた結果をマージして一つの結果を生成できます。

上記の説明は、Droongaシステムの設計に基いているので、少々わかりづらいかもしれません。
ここでは、プラガブルな操作という観点から離れて、プラグインで何をしたいのかという観点から見てみましょう。

既に存在するコマンドを元にして新たなコマンドを作成する
: たとえば、複雑な`search`コマンドをラップして、手軽に使えるコマンドを作りたいかもしれません。
  リクエストとレスポンスに対する*アダプション*がそれを可能にします。

新しいコマンドを追加してストレージを操作する
: たとえば、ストレージに保存されているデータを自在に操作したいかもしれません。
  リクエストに対する*ハンドリング*がそれを可能にします。

複雑なタスクを実現するコマンドを追加する
: たとえば、標準の`search`コマンドのような強力なコマンドを実装したいかもしれません。リクエストに対する*プランニング*と*コレクション*がそれを可能にします。

このチュートリアルでは、最初に*アダプション*を扱います。
これはもっともプラグインの基本的なユースケースなので、Droongaにおけるプラグイン開発の基礎を理解する助けになるはずです。
その後、他のケースも上述の順で説明します。
このチュートリアルに従うと、プラグインの書き方を理解できるようになります。
自分独自の要求を満たすプラグインを作成するための第一歩となることでしょう。

## プラグインを開発するには

詳細は以下のサブチュートリアルを参照してください:

 1. [リクエストとレスポンスを加工し、既存のコマンドに基づいた新たなコマンドを作成する][adapter]。
 2. [全てのボリューム上でリクエストを処理し、ストレージを操作する新たなコマンドを追加する][handler]。
 3. 特定のボリューム上だけでリクエストを処理し、より効率的にストレージを操作するコマンドを追加する (準備中)
 4. リクエストの分散とレスポンスの回収を行い、新たな複雑なコマンドを追加する (準備中)


  [basic tutorial]: ../basic/
  [overview]: ../../overview/
  [adapter]: ./adapter/
  [handler]: ./handler/
  [distribute-collect]: ./distribute-collect/
