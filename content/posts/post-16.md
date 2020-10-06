---
title: "GitHub + CircleCI + S3 でデプロイ設定"
date: 2020-10-02T13:13:20+09:00
draft: ture
---

Reactで開発した、サイトをCircleCI経由でデプロイする。  

構成は以下の通り

![img](/posts/img/post-16-profile.png)

CloudFrontでCDNに乗せるまでの流れ

1. IAMユーザーの作成
2. S3バケットの作成
3. Route53にドメインの設定
4. CloudFrontのDistributionを作成
5. IAMポリシーの作成、適用
6. GitHubリポジトリの作成
7. CircleCIの設定


## 使用技術

- インフラ
  - AWS
    - S3
    - CloudFront
    - Route53
    - IAM
    - Certificate Manager
  - CricelCI
- クライアントサイド
  - React

## IAMユーザーの作成

`ユーザーを追加` から新規でIAMユーザーを作成。  
「アクセスの種類」は「プログラムによるアクセス」チェックする。  
「アクセス許可の設定」は後で行うため一旦スキップ。

## S3バケットの作成

パブリックアクセスの設定は一旦`false`  
パケットホスティングの有効化  

### S3のバケットポリシー編集

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::xxxxxx:user/xxxxxxxxxxxxxx"    //  作成したIAMユーザーを設定
            },
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::xxxxxxxxxxxx",    // 作成したS3バケットを設定
                "arn:aws:s3:::xxxxxxxxxxxx/*"   // 作成したS3バケットを設定
            ]
        },
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::xxxxxxxxxxxx/*"    // 作成したS3バケットを設定
        }
    ]
}
```

## Route53にドメインの設定

今回は`MuuMuuDomain`で取得したドメインをRoute53に登録しサブドメインを登録し使用します。  

ホストゾーンのページより`ホストゾーンの作成`から取得したドメインを登録します。  
`NS` と `SOA` レコードが生成される。  

`Rotue53` で生成された `NSレコード` をドメインを取得したネームサーバーに登録します。  
`MuuMuuDomain` でドメイン取得を行ったので、`MuuMuuDomain`コントロールパネルより  
「ネームサーバ設定変更」を選択し「GMOペパポ以外のネームサーバを使用する」にチェックし、  
ネームサーバ1〜4に、`Route53` で生成された `NSレコード` を入力します。  

![img](/posts/img/post-16-muumuu-domain.png)

### AWS Certificate Managerで証明書登録

`AWS Certificate Manager` に `Route53` に登録したドメインの証明書を取得します。  

「証明書のリクエスト」から「パブリック証明書のリクエスト」を選択します。  
「検証方法の選択」では「DNSの検証」を選択  
「検証」の画面で「Route53でのレコードの作成」を選択します。  

![img](/posts/img/post-16-acm-1.png)

「状況」が「発行済み」になっていれば完了です。  

![img](/posts/img/post-16-acm-2.png)

## CloudFrontのDistributionを作成

「Origin Domain Name」では、今回作成した、S3バケットを選択する。

![img](/posts/img/post-16-cloudfront-1.png)

「Cache and origin request settings」の設定は、「Use legacy cache settings」にチェックを入れる。  
また「Object Caching」の設定を「Customize」にチェックを入れる。  
上記の設定を行うことで、`CloudFront` のキャッシュの保持する時間を制御することができます。  
今回はキャッシュを保持したくないので、すべての値を0にします。  

![img](/posts/img/post-16-cloudfront-2.png)

「Alternate Domain Names」では、 `Rotue53` で登録した、ドメイン名を入力します。  
「SSL Certificate」では「Custom SSL Certificate (example.com)」を選択し、inputをクリックすると`AWS Certificate Manager` で登録したドメインが表示されます。  

![img](/posts/img/post-16-cloudfront-3.png)

以上の設定を行い、「Create Distribution」をクリックします。  

## IAMポリシーの作成、適用

下記内容で新規ポリシーを作成  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:DeleteObject",
                "s3:ListBucket",
                "cloudfront:CreateInvalidation"
            ],
            "Resource": [
                "arn:aws:s3:::xxxxxxxxxxxxx",                                   // S3バケット名を設定
                "arn:aws:s3:::xxxxxxxxxxxxx/*",                                 // S3バケット名を設定
                "arn:aws:cloudfront::xxxxx:distribution/xxxxxxxxx"    // アカウントIDとdistribution IDを設定  例:arn:aws:cloudfront::{アカウントID}:distribution/{distribution ID}
            ]
        }
    ]
}
```

先程作成したIAMユーザーに上記で作成したポリシーを適用する。  

以上でAWS上での設定は完了です。  

## GitHubリポジトリの作成

自分の `GitHub` アカウントにログインし、「New repository」を選択  

![img](/posts/img/post-16-github-1.png)

「Repository name」にプロジェクト名を入力  
リポジトリの公開設定は「Public」「Private」どちらでもよい  
上記設定を行い「Create repository」をクリック  

![img](/posts/img/post-16-github-2.png)

ローカルのプロジェクト直下で `git` の初期化を行う  

```
$ git init
```

次に `git` の管理に含めないファイル、ディレクトリを指定するため `.gitignore` を作成  


```
$ touch .gitignore
```

`.gitignore` ファイルに以下を記載  

```
node_modules
.DS_Store
```

リモートリポジトリの「master」branchまでpush  

```
$ git add .
$ git commit -m "first commit"
$ git remote add origin git@github.com:{project name}.git
$ git push -u origin master
```

## CircleCIの設定

「Use Exising Config」をクリック  
「Download config.yml」をクリックし、`config.yml` をダウンロード  
ディレクトリ直下に `.circleci` ディレクトリを作成し`config.yml` を配置  

`config.yml` を以下のように設定する。  

```
version: 2.1

executors:
  default:
    docker:
      - image: circleci/ruby:2.4.2-jessie-node
    # ディレクトリ名を指定
    working_directory: ~/xxxxxxxxxxxx
  deploy:
    docker:
      - image: circleci/python:3.6.5


commands:
  node_install:
    steps:
      - run:
          name: "Node.js & npm update"
          command: |
            curl -sSL "https://nodejs.org/dist/v12.2.0/node-v12.2.0-linux-x64.tar.xz" | sudo tar --strip-components=2 -xJ -C /usr/local/bin/ node-v12.2.0-linux-x64/bin/node
            curl https://www.npmjs.com/install.sh | sudo bash
  yarn_install:
    steps:
      - restore_cache:
          name: Restore yarn cache
          keys:
            - v1-npm-deps-{{ .Branch }}-{{ checksum "yarn.lock" }}
      - run:
          name: yarn install
          command: yarn install
      - save_cache:
          name: Save yarn cache
          key: v1-npm-deps-{{ .Branch }}-{{ checksum "yarn.lock" }}
          paths:
            - node_modules
  yarn_build:
    steps:
      - run: yarn build
  install_awscli:
    steps:
      - run: sudo pip install awscli

jobs:
  # ビルド
  build-master:
    executor: default
    steps:
      - checkout
      - node_install
      - yarn_install
      - yarn_build
      - persist_to_workspace:
          <<: *default-persist

  # デプロイ
  deploy-master:
    executor: deploy
    steps:
      - attach_workspace:
          at: .
      - install_awscli
      - run:
          name: ls check
          command: ls -la
      - run:
          name: Deploy to S3
          # S3 bucket を指定
          command: aws s3 sync ./dist/ {S3 bucket} --delete --exact-timestamps
      - run:
          name: Update cloudFront
          # CloudFront の Distribution id を指定
          command: aws cloudfront create-invalidation --distribution-id {CloudFront Distribution id} --paths "/*"


workflows:
  deploy:
    jobs:
      # masterブランチ
      - build-master:
          filters: &master-filters
            branches:
              only: master
      - deploy-master:
          requires:
            - build-master
          filters:
            <<: *master-filters
```

### 環境変数の設定

`IAM` の `ACCESS KEY ID` と `SECRET ACCESS KEY` を設定します。  
該当のプロジェクトから「Project Settings」を選択  

![img](/posts/img/post-16-circleci-1.png)

「Environment Variables」から「Add Variable」を選択し、`ACCESS KEY ID` と `SECRET ACCESS KEY` を設定します。

![img](/posts/img/post-16-circleci-2.png)

以上で設定は完了になります。  
あとは `GitHub` の「master」branchに変更点が反映されれば、 `CircleCI` 経由でビルドが実行されます。