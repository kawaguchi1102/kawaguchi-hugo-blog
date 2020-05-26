---
title: "three.jsのIE対応で詰まったこと"
date: 2020-05-21T22:22:22+09:00
draft: false
---

# three.jsのIE対応で詰まったこと

build時に`--mode develop`でbuildした時はIEでも、しっかり表示されている。  
だが、`--mode production`でbuildした際は、IEだけ画面が表示されない。

consoleを確認すると構文エラーが出ていた。  
追っかけていくと、class構文が使わている箇所がありbabelを通しているはずなのになぜ……？

## 結論

webpack.config.jsの`babel-loader`の`exclude`で`node_modules`を指定してたので、`babel`が通らなかった。 
`exclude` を 以下の様に変更すると、babelを通してくれるようになった。

```
exclude: /node_modules\/(?!(three)\/).*/,
```
