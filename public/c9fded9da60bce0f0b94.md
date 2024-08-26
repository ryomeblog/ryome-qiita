---
title: Docker Compose作成方法
tags:
  - Docker
  - docker-compose
private: false
updated_at: '2022-09-14T00:48:06+09:00'
id: c9fded9da60bce0f0b94
organization_url_name: null
slide: false
ignorePublish: false
---
# 1. 事前知識
- [Dockerファイル構築]()
- [Dockerコマンド一覧]()

事前知識として、上記リンクの内容が必要です。

# 2. Docker Compose作成方法

- Docker Composeインストールコマンド

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

- Docker Composeバージョン確認

```
docker-compose --version
```

- Docker Compose起動
    - `docker-compose.yml` ファイルがある場所でコマンドを打たなければならない
```
docker-compose up -d 
```

- Docker Compose状況確認

```
docker-compose ps 
```

- Docker Compose停止

```
docker-compose down 
```

---

### docker-compose.yml の基本書式

- `docker-compose.yml` の書き方

```
version: "バージョン値"
services:
  コンテナ名:
    build: Dockerfileのパス 
    ports:
      - "ホスト側のポート:コンテナのポート"
  コンテナ名:
    image: "イメージ名"
    ports:
      - "ホスト側のポート:コンテナのポート"
```

- `docker-compose.yml` の例

```
version: "3.8"              ── 1 
services:                   ── 2 
  myweb:                    ── 3
    build: .                ── 4
    ports:                  ── 5
      - "8080:80" 
  apache:                   ── 3
    image: "httpd:latest"   ── 6
    ports:                  ── 5
      - "80:80" 
```

**【説明】** 
1.  version ········· docker-compose.yml のバージョン。 
2.  services ········ 起動するサービス群。以下ネストした要素でコンテナを定義 
3.  コンテナ名 ···· 任意の名前（例：  mysample、busybox） 
4.  build ············· Dockerfile からビルドする場合、ビルドコンテキストのディレクトリを指定 
5.  ports ············· ポート転送を指定 
6.  image ··········· イメージからコンテナ生成する場合、イメージ（リポジトリ、タグ）を指定 

- よく使うコマンド

| 項目           | 意味                                                                                                    |
| -------------- | ------------------------------------------------------------------------------------------------------- |
| image          | 利用するイメージ                                                                                        |
| ports          | ポートのマッピングの設定                                                                                |
| build          | docker-compose.ymlファイルを実行し、ビルドされるときのpath                                              |
| volumes        | マウントするボリュームを指定する                                                                        |
| environment    | 環境変数を指定                                                                                          |
| restart        | コンテナの起動に失敗したときの再起動ポリシーの設定 (always:どんな終了ステータスでもいつも再起動する等） |
| networks       | 接続するネットワークを指定                                                                              |
| logging        | ログ出力先を指定する                                                                                    |
| dns            | DNSサーバーを指定する                                                                                   |
| container_name | コンテナ名の指定                                                                                        |

---


