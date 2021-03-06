---
title: load
layout: ja
---

{% comment %}
##############################################
  THIS FILE IS AUTOMATICALLY GENERATED FROM
  "_po/ja/reference/1.0.3/commands/load/index.po"
  DO NOT EDIT THIS FILE MANUALLY!
##############################################
{% endcomment %}


* TOC
{:toc}

## 概要 {#abstract}

The `load` command adds new records to the specified table.
Column values of existing records are updated by new values, if the table has a primary key and there are existing records with specified keys.

This is compatible to [the `load` command of the Groonga](http://groonga.org/docs/reference/commands/load.html).

## APIの形式 {#api-types}

### HTTP (GET) {#api-types-http-get}

リクエスト先
: `(ドキュメントルート)/d/load`

リクエストメソッド
: `GET`

リクエストのURLパラメータ
: [パラメータの一覧](#parameters)で定義されている物を指定します。

リクエストのbody
: なし。

レスポンスのbody
: [レスポンスメッセージ](#response)。

### HTTP (POST) {#api-types-http-post}

リクエスト先
: `(ドキュメントルート)/d/load`

リクエストメソッド
: `POST`

リクエストのURLパラメータ
: [パラメータの一覧](#parameters)で定義されている物のうち、`values` 以外を指定します。

リクエストのbody
: [パラメータ](#parameters)の `values` 用の値を指定します。

レスポンスのbody
: [レスポンスメッセージ](#response)。

### REST {#api-types-rest}

対応していません。

### Fluentd {#api-types-fluentd}

対応していません。

## パラメータの構文 {#syntax}

    {
      "values"     : <Array of records to be loaded>,
      "table"      : "<Name of the table>",
      "columns"    : "<List of column names for values, separated by ','>",
      "ifexists"   : "<Grn_expr to determine records which should be updated>",
      "input_type" : "<Format type of the values>"
    }

## パラメータの詳細 {#parameters}

`table` 以外のパラメータはすべて省略可能です。

また、バージョン 1.0.3 の時点では以下のパラメータのみが動作します。
これら以外のパラメータは未実装のため無視されます。

 * `values`
 * `table`
 * `columns`

They are compatible to [the parameters of the `load` command of the Groonga](http://groonga.org/docs/reference/commands/load.html#parameters). See the linked document for more details.

HTTP clients can send `values` as an URL parameter with `GET` method, or the request body with `POST` method.
The URL parameter `values` is always ignored it it is sent with `POST` method.
You should send data with `POST` method if there is much data.

## レスポンス {#response}

このコマンドは、レスポンスの `body` としてコマンドの実行結果に関する情報を格納した配列を返却します。

    [
      [
        <Groonga's status code>,
        <Start time>,
        <Elapsed time>
      ],
      [<Number of loaded records>]
    ]

このコマンドはレスポンスの `statusCode` として常に `200` を返します。これは、Groonga互換コマンドのエラー情報はGroongaのそれと同じ形で処理される必要があるためです。

レスポンスの `body` の詳細：

ステータスコード
: コマンドが正常に受け付けられたかどうかを示す整数値です。以下のいずれかの値をとります。
  
   * `0` (`Droonga::GroongaHandler::Status::SUCCESS`) : 正常に処理された。.
   * `-22` (`Droonga::GroongaHandler::Status::INVALID_ARGUMENT`) : 引数が不正である。

開始時刻
: 処理を開始した時刻を示す数値（UNIX秒）。

処理に要した時間
: 処理を開始してから完了までの間にかかった時間を示す数値。

Number of loaded records
: An positive integer meaning the number of added or updated records.
