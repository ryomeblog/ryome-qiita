---
title: Qiita CLI導入方法
tags:
  - Qiita
  - CLI
  - QiitaCLI
private: false
updated_at: '2023-07-22T23:33:50+09:00'
id: 8f73d4d93c366a6d4b3e
organization_url_name: null
slide: false
---
# はじめに
本記事は以下のリポジトリ内容を実際にやってみてQiita CLIを導入するまでのメモとなります。

https://github.com/increments/qiita-cli

# 1. 事前準備
- Node.jsのインストール
    - [【Windows/Mac/Linux（Ubuntu）】Node.jsインストール方法](https://qiita.com/ryome/items/eec08b28aff294e8c3d6)

# 2. 開発環境

**使用技術とバージョン**

| 技術 | バージョン |
| --- | --- |
| Node.js | v18.12.1 |

※ バージョン18.0.0 以上が必要。

# 3. Qiita CLI インストール

- コマンド
```sh:コマンド
npm install @qiita/qiita-cli --save-dev
npx qiita version
```

- 実行例
```sh:実行例
>npm install @qiita/qiita-cli --save-dev

added 151 packages in 8s

43 packages are looking for funding
  run `npm fund` for details

>npx qiita version
0.6.0
```

:::note info
以下のコマンドは、npm（Node Package Manager）を使用して@qiita/qiita-cliというパッケージをインストールし、バージョンの確認をしています。

```sh:コマンド
npm install @qiita/qiita-cli --save-dev
npx qiita version
```

※ `--save-dev` オプションは、このパッケージを開発依存性としてプロジェクトに追加することを意味します。

:::

# 4. Qiita CLI セットアップ

- コマンド
```sh:コマンド
npx qiita init
```

- 実行例
```sh:実行例
>npx qiita init
設定ファイルを生成します。

  Creating C:\【path】\.github\workflows\publish.yml
     Created!

  Creating C:\【path】\.gitignore
     Created!

  Creating C:\【path】\qiita.config.json
     Created!

Success! ✨

次のステップ:

  1. トークンを作成してログインをしてください。
    npx qiita login

  2. 記事のプレビューができるようになります。
    npx qiita preview
```

:::note info
以下のコマンドは、Qiita CLI の初期セットアップを行います。

```sh:コマンド
npx qiita init
```

初期セットアップ後は以下のようなフォルダ構成になります。
```
C:.
│  .gitignore
│  qiita.config.json
│
└─.github
    └─workflows
            publish.yml
```

:::

# 5. Qiita CLI 使い方

## qiita login

- コマンド
```sh:コマンド
npx qiita login
```

- 実行例
```sh:実行例
>npx qiita login

以下のURLにアクセスしてトークンを発行してください。（「read_qiita」と「write_qiita」にチェックを入れてください）
  https://qiita.com/settings/tokens/new?read_qiita=1&write_qiita=1&description=qiita-cli

発行したトークンを入力: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
Hi ryome!

ログインが完了しました 🎉
以下のコマンドを使って執筆を始めましょう！

🚀 コンテンツをブラウザでプレビューする
  npx qiita preview

🚀 新しい記事を追加する
  npx qiita new (記事のファイルのベース名)

🚀 記事を投稿、更新する
  npx qiita publish (記事のファイルのベース名)

💁 コマンドのヘルプを確認する
  npx qiita help
```

:::note info
以下のコマンドで、Qiita のアカウントと紐付けし、記事の取得や投稿、更新が行えるようになります。

```sh:コマンド
npx qiita login
```

※ 途中で以下のように発行したトークンを入力を求められるので、リンクからトークンを発行し入力します。

```
以下のURLにアクセスしてトークンを発行してください。（「read_qiita」と「write_qiita」にチェックを入れてください）
  https://qiita.com/settings/tokens/new?read_qiita=1&write_qiita=1&description=qiita-cli

発行したトークンを入力: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

![0001.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/7918f97a-7b4d-6259-1fad-996aa1695b12.png)

※ `スコープ` は、 `read_qiita` と `write_qiita` にチェックを付けます。
:::

## npx qiita preview

- コマンド
```sh:コマンド
npx qiita preview
```

- 実行例
```sh:実行例
>npx qiita preview
Preview: http://localhost:8888
```

:::note info
以下のコマンドで、Qiita Preview(プレビュー画面)にアクセスすることが可能になります。

```sh:コマンド
npx qiita preview
```

![0002.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/561032d0-3afe-50ee-ae64-454886dcf909.png)

※ プレビュー画面では、 `新規記事作成` や `画像アップロード` 、 `記事の確認` などができます。
※ プレビュー画面のデフォルトのURLは `http://localhost:8888`
:::

# GitHub

https://github.com/ryomeblog/ryome-qiita

# 参考文献

https://github.com/increments/qiita-cli
