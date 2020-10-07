---
title: "PostgreSQLでPOST処理をした際、「duplicate key value violates unique constraint ~~」エラーの表示"
date: 2020-09-12T17:10:06+09:00
draft: ture
---

Laravelで作成したアプリケーションでフォームからsubmit(POST処理)を行ったところ、  
`duplicate key value violates unique constraint ~~`とエラー画面が表示された。  

## 原因

tableのレコード数とシーケンス番号がづれていたため、  
エラーが発生していた。  

## 解決方法

### PostgreSQLにログイン

```bash
# 1. tableのレコード数の確認
$ SELECT MAX(id) FROM table_name;
 max
-----
  15
(1 row)

# 2. シーケンス番号の確認
$ SELECT nextval('table_name_id_seq');
 nextval
---------
      11
(1 row)

# 3. シーケンス番号をレコード数に合わせる
$ SELECT setval('table_name_id_seq', (SELECT MAX(id) FROM table_name));
 setval
--------
     15
(1 row)
```
## なぜシーケンス番号がずれたのか

sqlから INSERT文 を実行した際、下記コードの様なsqlを実行していた。

```
INSERT INTO table_name (id, hoge, fuga) VALUES
    (1, 'HOGE1', 'FUGA1'),
    (2, 'HOGE2', 'FUGA2'),
    (3, 'HOGE3', 'FUGA3');
```

INSERT文 を一行で実行していたため、シーケンス番号として1番しか採番されなかった。  
