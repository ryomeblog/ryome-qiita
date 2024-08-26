---
title: Ubuntu 24.04にDockerをインストールする方法
tags:
  - Ubuntu
  - Docker
  - docker-compose
private: false
updated_at: '2024-08-23T00:55:48+09:00'
id: 29b3241d32cf63849f63
organization_url_name: null
slide: false
ignorePublish: false
---
# 事前知識

- [【2019年最新】VirtualBoxでUbuntuの仮想環境を作る方法](https://qiita.com/ryome/items/56e8ee3d5e27bf514e60)
- [wsl2のインストール方法【windows】](https://qiita.com/ryome/items/240f36923f5cb989da27)
- [UbuntuにDockerをインストールする手順](https://qiita.com/ryome/items/4b6b934b1b2021acfa26)

## Dockerのインストール手順

1. **パッケージリストの更新**
    ```bash
    sudo apt-get update
    ```
    - **説明**: このコマンドは、Ubuntuのパッケージリストを最新の状態に更新します。

2. **依存パッケージのインストール**
    ```bash
    sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
    ```
    - **説明**: Dockerのインストールに必要な依存パッケージ（HTTPS経由でのパッケージ取得、証明書の管理、GPGキーの管理、追加のソフトウェアプロパティの管理）をインストールします。

3. **Dockerの公式GPGキーを追加**
    ```bash
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```
    - **説明**: Dockerの公式GPGキーをシステムに追加します。

4. **Dockerリポジトリを追加**
    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```
    - **説明**: Dockerの公式リポジトリをシステムに追加します。

5. **Docker Engineのインストール**
    ```bash
    sudo apt-get update
    sudo apt install docker-ce docker-ce-cli containerd.io
    ```
    - **説明**: Docker Engine、Docker CLI、およびContainerdをインストールします。

6. **インストール確認**
    ```bash
    docker --version
    ```
    - **説明**: Dockerが正しくインストールされたかを確認します。このコマンドを実行すると、インストールされたDockerのバージョンが表示されます。

## Docker Composeのインストール手順

1. **Docker Composeプラグインのインストール**
    ```bash
    sudo apt install docker-compose-plugin
    ```
    - **説明**: Docker Composeは、複数のコンテナを定義して実行するためのツールです。このコマンドでDocker Composeプラグインをインストールします。

2. **インストール確認**
    ```bash
    docker compose version
    ```
    - **説明**: Docker Composeが正しくインストールされたかを確認します。このコマンドを実行すると、インストールされたDocker Composeのバージョンが表示されます。

## Dockerコマンド実行時の「permission denied」エラーの対処法

Dockerをインストールした後、`docker ps`コマンドを実行すると以下のようなエラーメッセージが表示されることがあります。

```
permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.46/containers/json": dial unix /var/run/docker.sock: connect: permission denied
```

このエラーは、現在のユーザーがDockerデーモンソケットへのアクセス権限を持っていないために発生します。

以下の手順でこの問題を解決できます。

#### 対処法

1. **現在のユーザーを`docker`グループに追加**
    ```bash
    sudo usermod -aG docker $USER
    ```
    - **説明**: このコマンドは、現在のユーザーを`docker`グループに追加します。これにより、ユーザーがDockerデーモンソケットにアクセスできるようになります。

2. **変更を反映するためにログアウトして再ログイン**
    ```bash
    newgrp docker
    ```
    - **説明**: `newgrp docker`コマンドを実行することで、現在のセッションに新しいグループ設定を適用します。これにより、再ログインせずに変更を反映できます。

3. **再度`docker ps`コマンドを実行して確認**
    ```bash
    docker ps
    ```
    - **説明**: このコマンドを実行して、Dockerデーモンにアクセスできるかを確認します。正常に動作していれば、現在実行中のコンテナのリストが表示されます。

#### 実行例

以下は、上記の手順を実行した際の例です。

```bash
$ sudo usermod -aG docker $USER
$ newgrp docker
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

## WSL2のUbuntuでDockerコマンド実行時にエラーが出た場合の対処法

WSL2のUbuntu環境でDockerを使用していると、以下のようなエラーメッセージが表示されることがあります。

```
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
```

このエラーは、Dockerデーモンが起動していないか、現在のユーザーがDockerデーモンにアクセスする権限を持っていない場合に発生します。

以下の手順でこの問題を解決できます。

#### 対処法

1. **Dockerデーモンの再起動**

    - **コマンド**
        ```bash
        sudo service docker stop  # Docker停止
        sudo service docker start # Docker開始
        ```

    - **実行結果**
        ```bash
        $ sudo service docker stop
         * Docker already stopped - file /var/run/docker-ssd.pid not found.
        $ sudo service docker start
         * Starting Docker: docker
        $ docker ps
        CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
        ```

## 関連記事

- [Docker-composeでSpringBoot×PostgreSQL環境構築](https://qiita.com/ryome/items/6a4bfa37af8adc702395)
- [Docker-compose基礎](https://qiita.com/ryome/items/35df47a01a7fe3ac70ee)
- [UbuntuにDocker-composeをインストールする手順](https://qiita.com/ryome/items/56a3263f347a08bd860f)
- [Dockerfile基礎](https://qiita.com/ryome/items/3d4de62b560aac31bede)
- [Docker基礎](https://qiita.com/ryome/items/6e59dcbe4735d45d58a3)
- [UbuntuにDockerをインストールする手順](https://qiita.com/ryome/items/4b6b934b1b2021acfa26)
- [Docker Compose作成方法](https://qiita.com/ryome/items/c9fded9da60bce0f0b94)
- [Dockerファイル構築](https://qiita.com/ryome/items/873938ca6734adec1c11)
- [Docker汎用コマンド集](https://qiita.com/ryome/items/12d8fb5f64acfbdd22e9)
- [Dockerコマンド一覧](https://qiita.com/ryome/items/54d23052babda2d88bf6)
- [みんなが欲しそうなDockerテンプレートまとめ](https://qiita.com/ryome/items/ab23eeadf3c2ff6b35bd)

## GitHub

https://github.com/ryomeblog
