---
title: "anyenvの設定"
date: 2020-04-25T16:11:49+09:00
draft: false
---

新しいPCで環境を整える必要があり、色々設定を行ったのでその備考録

## 環境

* mac OS Catalina
* Homebrew 2.2.13

まずはHomebrewで`anyenv`をインストール

```bash
$ brew install anyenv
```

`.bash_profile`に`eval "$(anyenv init -)"`を書き込み、ターミナルを再起動

``` bash
$ echo 'eval "$(anyenv init -)"' >> ~/.bash_profile
$ exec $SHELL -l
```

インストールできるenvの一覧

```bash
$ anyenv install -l
```

pyenvのインストール

```bash
$ anyenv install pyenv
$ exec $SHELL -l
```

```bash
$ pyenv install 3.7.0
$ pyenv global 3.7.0
$ pyenv global
$ anyenv version
```

