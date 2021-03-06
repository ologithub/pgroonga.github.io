---
title: "&~?演算子"
upper_level: ../
---

# `&~?`演算子

1.2.1で追加。

## 概要

`&~?`演算子は類似文書検索を実行します。

## 構文

```sql
column &~? document
```

`column`は検索対象のカラムです。型は`text`型、`text[]`型、`varchar`型のどれかです。

`document`は類似文書検索で使う文書です。`column`が`text`型または`text[]`型の場合は型は`text`型です。`column`が`varchar`型の場合は型は`text`型です。

類似文書検索は`document`のコンテンツに似たレコードを探します。もし、`document`のコンテンツが短かった場合、類似文書検索はそれほど似ていないレコードも返してしまうかもしれません。

## 演算子クラス

この演算子を使うには次のどれかの演算子クラスを指定する必要があります。

  * `pgroonga.text_full_text_search_ops_v2`：`text`用

  * `pgroonga.text_array_full_text_search_ops_v2`：`text[]`用

  * `pgroonga.varchar_full_text_search_ops_v2`：`varchar`用

## 使い方

例に使うサンプルスキーマとデータは次の通りです。

```sql
CREATE TABLE memos (
  id integer,
  content text
);

CREATE INDEX pgroonga_content_index ON memos
  USING pgroonga (content pgroonga.text_full_text_search_ops_v2);
```

```sql
INSERT INTO memos VALUES (1, 'PostgreSQLはリレーショナル・データベース管理システムです。');
INSERT INTO memos VALUES (2, 'Groongaは日本語対応の高速な全文検索エンジンです。');
INSERT INTO memos VALUES (3, 'PGroongaはインデックスとしてGroongaを使うためのPostgreSQLの拡張機能です。');
INSERT INTO memos VALUES (4, 'groongaコマンドがあります。');
```

`&~?`演算子を使うと指定した文書と似たレコードを検索できます。

```sql
SELECT * FROM memos WHERE content &~? 'MroongaはGroongaを使うMySQLの拡張機能です。';
--  id |                                  content                                  
-- ----+---------------------------------------------------------------------------
--   3 | PGroongaはインデックスとしてGroongaを使うためのPostgreSQLの拡張機能です。
-- (1 row)
```

## シーケンシャルスキャン

シーケンシャルスキャンでは類似文書検索を使うことはできません。シーケンシャルスキャンで類似文書検索を使うと次のエラーが返ります。

```sql
SELECT * FROM memos WHERE content &~? 'MroongaはGroongaを使うMySQLの拡張機能です。';
-- ERROR:  pgroonga: operator &~? is available only in index scan
```
