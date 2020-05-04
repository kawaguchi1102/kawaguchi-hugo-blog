---
title: "GW2020 ステイホーム もくもく会 2日目"
date: 2020-05-04T01:22:38+09:00
draft: false
---

# もくもく会 2日目

AWS Cognitoを使用したログインまわりの実装担当

## 使用技術

- AWS Cognito
  - amazon-cognito-identity-js
- React
  - React Router
  - Props

## 今日やったこと

### AWS Cognito

- SignUp 処理の実装
- SignIn 処理の実装
- 認証コード 処理の実装

### React

- Propsの理解
- Reat Router
  - ログイン時、非ログイン時のコンポーネント表示の処理の切り分け
  - React Routerでのリダイレクト処理

### Javascript

- 処理ロジックの逃し方
  - export で処理を切り出す
  - 使いたいコンポーネント内でimportして関数を呼び出す

[src/scripts/func.js]

```js:src/scripts/func.js
export const hoge = () => {
  hugoFunc = () => {}
  ...
}
```

[src/componets/componet.js]

```js:src/componets/componet.js
import { hugo } from '../scripts/func.js'

huge();
```

## 詰まった箇所

### ログイン処理の実装
1. CognitoのJavascript SDK amazon-cognito-identity-js で使用できる関数を理解していなかったのでSignIn実装時のエラー解消に時間を取られた。
2. ReactのPropsの挙動がしっかり理解できていなかったので、ログイン時、非ログイン時のコンポーネント表示処理の切り分けをするのに時間を取られた。

