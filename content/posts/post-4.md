---
title: "GW2020 ステイホーム もくもく会 3日目"
date: 2020-05-05T02:41:16+09:00
draft: false
---

# もくもく会 3日目

GWも折り返し、昨日に引きCognitoを使用した、ユーザーまわりの更新処理を担当
ログイン系画面のレイアウトのコーディングも行った。

## 使用技術

- JavaScript
  - Promise
- React
  - React Router
  - reactstrap
  - useEffect
- Cognito

## 今日の対応

- ログイン、未ログインのルーティング調整
- 画面レイアウトの調整
  - サインアップ
  - サインイン
  - 検証コード入力画面

- Cognito
  - ユーザー取得処理
  - ユーザー名、パスワードの変更処理

## 詰まったところ

### JavaScript
ロジック側の処理の切り出しになれておらずあたふたした。

### React
ライフサイクルやReactの特性がわかっておらず、
`useEffect`と`state`に振り回された。

### Cognito

ユーザー情報取得の際、`getUserAttributes`関数を使用すれば取得できると思っていたが、
`getSession`関数のcallback内でしか`getUserAttributes`関数は使用できない。

```js
cognitoUser.getSession((err, session) => {
  cognitoUser.getUserAttributes((err, result) => {
    ...
  });
});
```

## 所感

`React Router`はv3とv4で path の match方法が変わった。
ログイン時と未ログイン時のRoterコンポーネントの切り分けがめんどくさい。

Cognitoの使ったフロント側の実装も、この3日間で行った
ユーザー新規登録、ログイン処理、ユーザー情報変更処理で実装まわりも少しなれてきた。
Cognitoを使用しておどろいたことは、ユーザー情報の変更など、結構自由がきくこと。


レイアウトのマークアップはHTMLのコーディングでも、お世話になっていた
Bootstrapベースのreactstrapを使用したので、特に詰まることなくコーディングできました。
