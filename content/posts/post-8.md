---
title: "babel"
date: 2020-05-19T22:30:10+09:00
draft: true
---

# ReactとBabel 7

## Babel 7

persetには`@babel/preset-env`を利用する。 

```bash
$ yarn add -D @babel/preset-env
```

### @babel/polyfillが非推奨(Babel 7.4.0)

> As of Babel 7.4.0, this package has been deprecated in favor of directly including core-js/stable (to polyfill ECMAScript features) and regenerator-runtime/runtime (needed to use transpiled generator functions):

`@babel/polyfill`の代わりに、`core-js`と`regenerator-runtime/runtime`を利用する。

```js
import 'core-js/stable';
import 'regenerator-runtime/runtime';
```

`core-js`は`@babel/polyfill`も利用している`polyfill` 
利用が推奨されているのは`v3`で`@babel/polyfill`が利用しているのは`v2` 



