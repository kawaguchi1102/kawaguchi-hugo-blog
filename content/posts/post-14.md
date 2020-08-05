---
title: "TypeSctipt & Node.js の環境構築"
date: 2020-08-06T00:56:39+09:00
draft: true
---

## ディレクトリ構成

ディレクトリの構成は以下のようになります。

```
./
├── app
│   ├── app.ts
│   ├── models
│   │   └── index.ts
│   ├── routes
│   │   └── index.ts
│   └── services
│       └── index.ts
├── package.json
├── tsconfig.json
└── yarn.lock
```

## tsconfig.jsonの編集

`tsconfig.json` を以下のように変更します。

```git:tsconfig.json
{
  "compilerOptions": {
-   "target": "es5",
+   "target": "es6",
    "module": "commonjs",
+   "sourcMap": true,
+   "outDir": "./dist",
    ...
- }
+ },
+ "include": [
+   "app/**/*"
+ ],
}
```
## packageのインストール

`ts-node`
tsファイルをコンパイルし、nodeでプログラムを実行してくれる  

`ts-node-dev` のインストール  
ソースコードの変更を検知し自動的にコンパイルを行ってくれるパッケージ  




