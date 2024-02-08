---
title: みんなが欲しそうなDockerテンプレートまとめ
tags:
  - Docker
  - dockerfile
  - docker-compose
private: false
updated_at: '2024-01-11T09:14:04+09:00'
id: ab23eeadf3c2ff6b35bd
organization_url_name: null
slide: false
---

## 事前知識

https://qiita.com/ryome/items/56a3263f347a08bd860f

https://qiita.com/ryome/items/4b6b934b1b2021acfa26

https://qiita.com/ryome/items/35df47a01a7fe3ac70ee

https://qiita.com/ryome/items/3d4de62b560aac31bede

https://qiita.com/ryome/items/6e59dcbe4735d45d58a3

https://qiita.com/ryome/items/c9fded9da60bce0f0b94

## GitHub

今回の記事で紹介したソースコードをGitHubで公開しています。

https://github.com/ryomeblog/docker-summary

## 実行方法

以下コマンドで各項目が実行できます。

```cmd:コマンド
docker-compose up -d
```

## DB関連

### DynamoDB

```yml:docker-compose.yml
version: '3.8'

services:
  dynamodb-local:
    image: amazon/dynamodb-local
    ports:
      - "8000:8000" # ローカルポート8000をコンテナの8000ポートにマッピング
    volumes:
      - dynamodb-data:/home/dynamodblocal/data # データの永続化
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"

  dynamodb-admin:
    image: aaronshaf/dynamodb-admin
    environment:
      DYNAMO_ENDPOINT: http://dynamodb-local:8000
    ports:
      - "8001:8001" # ローカルポート8001をコンテナの8001ポートにマッピング
    depends_on:
      - "dynamodb-local"

volumes:
  dynamodb-data: # データボリュームの定義
```

この`docker-compose.yml`ファイルは、Amazon DynamoDB LocalとDynamoDB Adminという2つのサービスを定義しているDocker Composeの設定ファイルです。

#### 1. dynamodb-local

このサービスは、AmazonのDynamoDB Localを使用しています。

DynamoDB Localは、AWSのDynamoDBのオンプレミスバージョンで、開発やテスト目的でローカル環境でDynamoDBを模倣するために使用されます。

- **イメージ**: `amazon/dynamodb-local` は、DynamoDB Localの公式Dockerイメージです。
- **ポート**: `"8000:8000"` は、ホストマシンの8000ポートをコンテナの8000ポートにマッピングします。これにより、ホストマシンの8000ポートを通じてDynamoDB Localにアクセスできます。
- **ボリューム**: `dynamodb-data:/home/dynamodblocal/data` は、コンテナ内のDynamoDB Localのデータをホストマシン上の名前付きボリューム（`dynamodb-data`）に保存します。これにより、コンテナが停止または削除されてもデータが保持されます。
- **コマンド**: `"-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"` は、DynamoDB Localを起動するためのコマンドです。`-sharedDb` オプションにより、すべてのクライアントが同じデータベースインスタンスを共有し、`-dbPath` オプションでデータの保存場所を指定しています。

#### 2. dynamodb-admin

このサービスは、DynamoDB Localのデータを管理するためのWebベースの管理ツール、DynamoDB Adminを使用しています。

- **イメージ**: `aaronshaf/dynamodb-admin` は、DynamoDB AdminのDockerイメージです。
- **環境変数**: `DYNAMO_ENDPOINT: http://dynamodb-local:8000` は、DynamoDB Adminが接続するDynamoDB Localのエンドポイントを指定します。
- **ポート**: `"8001:8001"` は、ホストマシンの8001ポートをコンテナの8001ポートにマッピングします。これにより、ホストマシンの8001ポートを通じてDynamoDB Adminにアクセスできます。
- **depends_on**: `depends_on: - "dynamodb-local"` は、`dynamodb-admin` サービスが起動する前に`dynamodb-local` サービスが起動していることを保証します。

#### volumes

- **dynamodb-data**: このセクションで、`dynamodb-data`という名前のデータボリュームが定義されています。これは`dynamodb-local`サービスで使用され、データの永続化を可能にします。


### MySQL

```yml:docker-compose.yml
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    volumes:
      - mysql-data:/var/lib/mysql # データの永続化
      - ./my-custom.cnf:/etc/mysql/conf.d/my-custom.cnf # カスタム設定ファイル
    environment:
      MYSQL_DATABASE: mydb
      MYSQL_USER: user
      MYSQL_PASSWORD: password
      MYSQL_ROOT_PASSWORD: rootpassword
    ports:
      - "3306:3306" # ホストマシンのポートをコンテナにマッピング

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    environment:
      PMA_HOST: mysql
      PMA_PORT: 3306
    ports:
      - "8080:80" # ホストマシンのポート8080をコンテナの80ポートにマッピング
    depends_on:
      - mysql

volumes:
  mysql-data: # データボリュームの定義
```

この`docker-compose.yml`ファイルは、MySQLデータベースサーバーとphpMyAdminインターフェースをセットアップするためのDocker Composeの設定です。

#### 1. mysql

このサービスはMySQLデータベースを構築します。

- **イメージ**: `mysql:8.0` は、MySQLのバージョン8.0を使用することを指定しています。
- **ボリューム**:
  - `mysql-data:/var/lib/mysql`: これにより、MySQLのデータはDockerボリューム（`mysql-data`）に保存され、データの永続性が保証されます。コンテナが削除されても、データは保持されます。
  - `./my-custom.cnf:/etc/mysql/conf.d/my-custom.cnf`: これはホストマシンのカスタム設定ファイル（`my-custom.cnf`）をコンテナ内のMySQL設定ディレクトリにマウントします。これにより、デフォルトの設定を上書きできます。
- **環境変数**: `MYSQL_DATABASE`, `MYSQL_USER`, `MYSQL_PASSWORD`, `MYSQL_ROOT_PASSWORD` これらはMySQLの初期設定（データベース名、ユーザー名、ユーザーとrootのパスワード）を定義します。
- **ポート**: `"3306:3306"` はホストマシンの3306ポートをコンテナの3306ポートにマッピングします。これにより、ホストからMySQLサーバーにアクセスできます。

#### 2. phpmyadmin

このサービスは、WebベースのMySQLデータベース管理ツールであるphpMyAdminを提供します。

- **イメージ**: `phpmyadmin/phpmyadmin` はphpMyAdminの公式Dockerイメージです。
- **環境変数**: 
  - `PMA_HOST: mysql` はphpMyAdminが接続するMySQLホスト（この場合は`mysql`サービス）を指定します。
  - `PMA_PORT: 3306` はMySQLのポートを指定します。
- **ポート**: `"8080:80"` はホストマシンの8080ポートをコンテナの80ポート（phpMyAdminのデフォルトポート）にマッピングします。これにより、ホストの8080ポートを通じてphpMyAdminにアクセスできます。
- **depends_on**: `depends_on: - mysql` は、phpMyAdminサービスが起動する前にMySQLサービスが起動していることを保証します。

#### volumes

- **mysql-data**: このセクションで、`mysql-data`という名前のデータボリュームが定義されています。これは`mysql`サービスで使用され、MySQLデータベースのデータの永続化を可能にします。

### PostgreSQL

```yml:docker-compose.yml
version: '3.8'

services:
  postgres:
    image: postgres:14
    volumes:
      - postgres-data:/var/lib/postgresql/data # データの永続化
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql # 初期化スクリプト
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432" # ホストマシンのポートをコンテナにマッピング

  pgadmin:
    image: dpage/pgadmin4
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: root
    ports:
      - "5050:80" # ホストマシンのポート5050をコンテナの80ポートにマッピング
    depends_on:
      - postgres

volumes:
  postgres-data: # データボリュームの定義
```

この`docker-compose.yml`ファイルは、PostgreSQLデータベースとpgAdminというWebベースの管理ツールをセットアップするためのDocker Composeの設定です。

#### 1. postgres

このサービスはPostgreSQLデータベースを構築します。

- **イメージ**: `postgres:14` は、PostgreSQLのバージョン14の公式Dockerイメージを指定しています。
- **ボリューム**:
  - `postgres-data:/var/lib/postgresql/data`: これにより、PostgreSQLのデータがDockerボリューム（`postgres-data`）に保存され、データの永続性が保証されます。コンテナが削除されても、データは保持されます。
  - `./init.sql:/docker-entrypoint-initdb.d/init.sql`: ホストマシンの`init.sql`スクリプトをコンテナ内のPostgreSQL初期化ディレクトリにマウントします。コンテナ起動時にこのスクリプトが実行され、データベースの初期設定やスキーマの作成が行われます。
- **環境変数**: `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD` はそれぞれPostgreSQLのデータベース名、ユーザー名、ユーザーパスワードを指定します。
- **ポート**: `"5432:5432"` はホストマシンの5432ポートをコンテナの5432ポートにマッピングします。これにより、ホストからPostgreSQLサーバーにアクセスできます。

#### 2. pgadmin

このサービスは、WebベースのPostgreSQLデータベース管理ツールであるpgAdminを提供します。

- **イメージ**: `dpage/pgadmin4` はpgAdminの公式Dockerイメージです。
- **環境変数**: 
  - `PGADMIN_DEFAULT_EMAIL`: pgAdminのデフォルトログインメールアドレスを設定します。
  - `PGADMIN_DEFAULT_PASSWORD`: pgAdminのデフォルトログインパスワードを設定します。
- **ポート**: `"5050:80"` はホストマシンの5050ポートをコンテナの80ポート（pgAdminのデフォルトポート）にマッピングします。これにより、ホストの5050ポートを通じてpgAdminにアクセスできます。
- **depends_on**: `depends_on: - postgres` は、pgAdminサービスが起動する前にPostgreSQLサービスが起動していることを保証します。

#### volumes

- **postgres-data**: このセクションで、`postgres-data`という名前のデータボリュームが定義されています。これは`postgres`サービスで使用され、PostgreSQLデータベースのデータの永続化を可能にします。

## OS関連

### Debian

```Dockerfile:Dockerfile
# Debianイメージをベースとする
FROM debian:12.2-slim

# 必要なパッケージのインストール
RUN apt-get update && apt-get install -y \
    bash \
    curl \
    git \
    vim \
    zip \
    unzip \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# 作業ディレクトリの設定
WORKDIR /app

# コンテナ起動時に実行されるコマンド
CMD ["bash"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  dev:
    build: .
    volumes:
      - .:/app
    tty: true
```

このDocker構成は、カスタムDebianベースの開発環境をセットアップするために使用されます。

`Dockerfile`はイメージのビルド指示を含み、`docker-compose.yml`ファイルはそのイメージを使用してコンテナを起動するための設定を定義します。

#### Dockerfile 解説

この`Dockerfile`は、Debianベースのカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM debian:12.2-slim` この行は、軽量版のDebian 12.2をベースイメージとして使用します。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` この命令は、bash, curl, git, vim, zip/unzip, およびビルドツール（`build-essential`）など、開発に必要な一連のパッケージをインストールします。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` これは、インストールプロセスで生成されたキャッシュを削除し、イメージのサイズを削減します。
- **作業ディレクトリ**: `WORKDIR /app` この命令は、イメージ内の作業ディレクトリを`/app`に設定します。
- **デフォルトコマンド**: `CMD ["bash"]` コンテナが起動されたときに、bashシェルを起動するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上述の`Dockerfile`を使用して開発環境コンテナをセットアップします。

- **バージョン**: `version: '3.8'` は、使用するComposeファイルのバージョンを指定します。
- **サービス**: 
  - `dev`: このセクションは`dev`という名前のサービスを定義します。
  - `build: .` これはDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `volumes: - .:/app` この命令は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できるようになります。
  - `tty: true` この設定は、コンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるようにします。これにより、コンテナ内でインタラクティブな作業が可能になります。

### Ubuntu

```Dockerfile:Dockerfile
# Ubuntuイメージをベースにする
FROM ubuntu:23.04

# 必要なパッケージのインストール
RUN apt-get update && apt-get install -y \
    bash \
    curl \
    git \
    vim \
    zip \
    unzip \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# 作業ディレクトリの設定
WORKDIR /app

# コンテナ起動時に実行されるコマンド
CMD ["bash"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  dev:
    build: .
    volumes:
      - .:/app
    tty: true
```

この構成は、カスタムUbuntuベースの開発環境をセットアップするためのDocker設定を示しています。

`Dockerfile`はイメージのビルド手順を含み、`docker-compose.yml`ファイルはそのイメージを使用してコンテナを起動するための設定を定義します。

#### Dockerfile 解説

この`Dockerfile`は、Ubuntuベースのカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM ubuntu:23.04` は、Ubuntu 23.04をベースイメージとして使用します。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` は、bash, curl, git, vim, zip/unzip, およびビルドツール（`build-essential`）など、開発に必要な一連のパッケージをインストールします。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` でインストールプロセス中に生成されたキャッシュを削除し、イメージのサイズを小さくします。
- **作業ディレクトリ**: `WORKDIR /app` でイメージ内の作業ディレクトリを`/app`に設定します。
- **デフォルトコマンド**: `CMD ["bash"]` はコンテナが起動した際にbashシェルを起動するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用して開発環境コンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `dev`: このセクションは`dev`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

## Webサーバ関連

### Apache

```yml:docker-compose.yml
version: '3.8'

services:
  apache:
    image: httpd:latest
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/local/apache2/htdocs
    command: ["httpd-foreground"]
```

このDocker構成は、`httpd`（Apache HTTPサーバー）を使用してWebサーバー環境をセットアップするためのものです。

#### docker-compose.yml 解説

`docker-compose.yml`ファイルは、Apache Webサーバーのコンテナを設定します。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `apache`: このセクションは`apache`という名前のサービスを定義します。
  - `image: httpd:latest` は、Apache HTTPサーバーの最新の公式Dockerイメージを使用することを指定します。
  - `ports: - "80:80"` は、ホストマシンの80ポートをコンテナの80ポートにマッピングします。これにより、ホストマシンのポート80からApacheサーバーにアクセスできます。
  - `volumes: - ./html:/usr/local/apache2/htdocs` は、ホストマシンの現在のディレクトリ内の`html`フォルダをコンテナ内のApacheのドキュメントルート（`/usr/local/apache2/htdocs`）にマウントします。これにより、ホストマシン上の`html`フォルダ内のファイルがWebサーバーを通じて提供されます。
  - `command: ["httpd-foreground"]` は、コンテナが起動するときに実行されるコマンドを指定します。このコマンドはApacheをフォアグラウンドで実行し、コンテナが動作し続けるようにします。

### Nginx

```yml:docker-compose.yml
version: '3.8'

services:
  nginx:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
    command: ["nginx", "-g", "daemon off;"]
```

このDocker構成は、`nginx`（Nginx HTTPサーバー）を使用してWebサーバー環境をセットアップするためのものです。

#### docker-compose.yml 解説

`docker-compose.yml`ファイルは、Nginx Webサーバーのコンテナを設定します。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `nginx`: このセクションは`nginx`という名前のサービスを定義します。
  - `image: nginx:latest` は、Nginxの最新の公式Dockerイメージを使用することを指定します。
  - `ports: - "80:80"` は、ホストマシンの80ポートをコンテナの80ポートにマッピングします。これにより、ホストマシンのポート80からNginxサーバーにアクセスできます。
  - `volumes: - ./html:/usr/share/nginx/html` は、ホストマシンの現在のディレクトリ内の`html`フォルダをコンテナ内のNginxのドキュメントルート（`/usr/share/nginx/html`）にマウントします。これにより、ホストマシン上の`html`フォルダ内のファイルがWebサーバーを通じて提供されます。
  - `command: ["nginx", "-g", "daemon off;"]` は、コンテナが起動するときに実行されるコマンドを指定します。このコマンドはNginxをフォアグラウンドで実行し、コンテナが動作し続けるようにします。

## フレームワーク

### FastAPI

```Dockerfile:Dockerfile
# Pythonイメージをベースにする
FROM python:3.10

# 必要なツールのインストール
RUN apt-get update && apt-get install -y \
    bash \
    git \
    vim \
    && rm -rf /var/lib/apt/lists/*

# 作業ディレクトリの設定
WORKDIR /app

# FastAPIとUvicorn（ASGIサーバー）のインストール
RUN pip install fastapi uvicorn

# ホストマシンからのファイルコピー用ディレクトリ
VOLUME [ "/app" ]

# コンテナ起動時に実行されるコマンド
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--reload"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  python-dev:
    build: .
    volumes:
      - .:/app # 現在のディレクトリをコンテナの/appにマウント
    tty: true
```

この構成は、Pythonをベースにした開発環境をセットアップし、FastAPIを使用してWebアプリケーションを開発・実行するためのものです。

#### Dockerfile 解説

この`Dockerfile`は、FastAPIを使用するPython開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM python:3.10` は、Python 3.10をベースイメージとして使用します。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` は、bash, git, vimなどの基本的な開発ツールをインストールします。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` は、インストールプロセス中に生成されたキャッシュを削除し、イメージのサイズを小さくします。
- **作業ディレクトリ**: `WORKDIR /app` はイメージ内の作業ディレクトリを`/app`に設定します。
- **Pythonパッケージのインストール**: `RUN pip install fastapi uvicorn` は、FastAPIとUvicorn（ASGIサーバー）をインストールします。
- **ボリューム**: `VOLUME [ "/app" ]` は、ホストマシンからのファイルをコンテナ内の`/app`ディレクトリにコピーするためのボリュームを設定します。
- **デフォルトコマンド**: `CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--reload"]` はコンテナが起動した際に、Uvicornを使用してFastAPIアプリケーションを実行するように設定します。`--reload`フラグは開発中のコードの変更が自動的に反映されるようにします。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してPython開発環境のコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `python-dev`: このセクションは`python-dev`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

### Flask

```Dockerfile:Dockerfile
# Pythonイメージをベースにする
FROM python:3.10

# 必要なツールのインストール
RUN apt-get update && apt-get install -y \
    bash \
    git \
    vim \
    && rm -rf /var/lib/apt/lists/*

# 作業ディレクトリの設定
WORKDIR /app

# Flaskのインストール
RUN pip install flask

# ホストマシンからのファイルコピー用ディレクトリ
VOLUME [ "/app" ]

# コンテナ起動時に実行されるコマンド
# Flaskアプリケーションのエントリーポイントを指定
CMD ["flask", "run", "--host=0.0.0.0"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  flask-dev:
    build: .
    ports:
      - "5000:5000" # Flaskのデフォルトポートをホストとマッピング
    volumes:
      - .:/app # 現在のディレクトリをコンテナの/appにマウント
    tty: true
```

この構成は、PythonをベースにしたFlask開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、Flaskを使用するPython開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM python:3.10` は、Python 3.10をベースイメージとして使用します。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` は、bash, git, vimなどの基本的な開発ツールをインストールします。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` は、インストールプロセス中に生成されたキャッシュを削除し、イメージのサイズを小さくします。
- **作業ディレクトリ**: `WORKDIR /app` はイメージ内の作業ディレクトリを`/app`に設定します。
- **Pythonパッケージのインストール**: `RUN pip install flask` は、WebフレームワークであるFlaskをインストールします。
- **ボリューム**: `VOLUME [ "/app" ]` は、ホストマシンからのファイルをコンテナ内の`/app`ディレクトリにコピーするためのボリュームを設定します。
- **デフォルトコマンド**: `CMD ["flask", "run", "--host=0.0.0.0"]` はコンテナが起動した際に、Flaskアプリケーションを実行するように設定します。`--host=0.0.0.0`はアプリケーションをコンテナ外からアクセス可能にします。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してFlask開発環境のコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `flask-dev`: このセクションは`flask-dev`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `ports: - "5000:5000"` は、ホストマシンの5000ポートをコンテナの5000ポートにマッピングします。これにより、ホストからFlaskアプリケーションにアクセスできます。
  - `volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

### GraphQL

```Dockerfile:Dockerfile
FROM node:20

WORKDIR /app

COPY package.json /app/

RUN npm install

COPY . /app/

CMD ["npm", "start"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  graphql-server:
    build: .
    ports:
      - "4000:4000"  # ホストマシンのポートをコンテナのポートにマッピング
    volumes:
      - .:/app  # ホストとコンテナ間でフォルダを共有
    tty: true
```

この構成は、Node.jsをベースにしたGraphQLサーバーの開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、Node.jsベースのアプリケーションのカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM node:20` は、Node.jsのバージョン20をベースイメージとして使用します。
- **作業ディレクトリ**: `WORKDIR /app` はイメージ内の作業ディレクトリを`/app`に設定します。
- **ファイルのコピー**:
  - `COPY package.json /app/` は、ホストマシンの`package.json`ファイルをコンテナ内の`/app`ディレクトリにコピーします。
  - `COPY . /app/` はホストマシンの現在のディレクトリのすべてのファイルをコンテナの`/app`ディレクトリにコピーします。
- **依存関係のインストール**: `RUN npm install` は、`package.json`で指定された依存関係をインストールします。
- **デフォルトコマンド**: `CMD ["npm", "start"]` はコンテナが起動した際に、`npm start`コマンドを実行するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してGraphQLサーバーのコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `graphql-server`: このセクションは`graphql-server`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `ports: - "4000:4000"` は、ホストマシンの4000ポートをコンテナの4000ポートにマッピングします。これにより、ホストからGraphQLサーバーにアクセスできます。
  - `volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

### Spring(Maven)

```Dockerfile:Dockerfile
# OpenJDKを含む基本的なJavaイメージをベースにする
FROM openjdk:11

# Mavenをインストール
RUN apt-get update && apt-get install -y \
    maven \
    bash \
    curl \
    git \
    vim \
    && rm -rf /var/lib/apt/lists/*

# 作業ディレクトリの設定
WORKDIR /app

# コンテナ起動時に実行されるコマンド
CMD ["bash"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  spring-dev:
    build: .
    ports:
      - "8080:8080"
    volumes:
      - .:/app
    tty: true
```

このDocker構成は、Java（特にSpring Frameworkを想定）の開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、Javaベースの開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM openjdk:11` は、OpenJDK 11を含む基本的なJavaイメージをベースとして使用します。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` は、Maven（Javaのビルドツール）、bash、curl、git、vimなどの開発に必要なツールをインストールします。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` は、インストールプロセス中に生成されたキャッシュを削除し、イメージのサイズを小さくします。
- **作業ディレクトリ**: `WORKDIR /app` はイメージ内の作業ディレクトリを`/app`に設定します。
- **デフォルトコマンド**: `CMD ["bash"]` はコンテナが起動した際にbashシェルを起動するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してJava開発環境のコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `spring-dev`: このセクションは`spring-dev`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `ports: - "8080:8080"` は、ホストマシンの8080ポートをコンテナの8080ポートにマッピングします。これにより、ホストからSpringアプリケーションにアクセスできます。
  - `volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

## プログラミング言語関連

### Go

```Dockerfile:Dockerfile
# Go言語のイメージをベースにする
FROM golang:1.16

# 必要なツールのインストール
RUN apt-get update && apt-get install -y \
    bash \
    git \
    vim \
    && rm -rf /var/lib/apt/lists/*

# 作業ディレクトリの設定
WORKDIR /app

# ホストマシンからのファイルコピー用ディレクトリ
VOLUME [ "/app" ]

# コンテナ起動時に実行されるコマンド
CMD ["bash"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  go-app:
    build: .
    volumes:
      - .:/app # 現在のディレクトリをコンテナの/appにマウント
    tty: true
```

このDocker構成は、Go言語（Golang）の開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、Go言語ベースの開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM golang:1.16` は、Go言語のバージョン1.16を含む基本的なイメージをベースとして使用します。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` は、bash、git、vimなどの開発に必要な基本的なツールをインストールします。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` は、インストールプロセス中に生成されたキャッシュを削除し、イメージのサイズを小さくします。
- **作業ディレクトリ**: `WORKDIR /app` はイメージ内の作業ディレクトリを`/app`に設定します。
- **ボリューム**: `VOLUME [ "/app" ]` は、ホストマシンからのファイルをコンテナ内の`/app`ディレクトリにコピーするためのボリュームを設定します。
- **デフォルトコマンド**: `CMD ["bash"]` はコンテナが起動した際にbashシェルを起動するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してGo言語開発環境のコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `go-app`: このセクションは`go-app`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

### Java

```Dockerfile:Dockerfile
# OpenJDKを含む基本的なJavaイメージをベースにする
FROM openjdk:11

# 必要なツールのインストール
RUN apt-get update && apt-get install -y \
    bash \
    curl \
    git \
    vim \
    && rm -rf /var/lib/apt/lists/*

# 作業ディレクトリの設定
WORKDIR /app

# コンテナ起動時に実行されるコマンド
CMD ["bash"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  java-dev:
    build: .
    volumes:
      - .:/app
    tty: true
```

このDocker構成は、Javaを使用した開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、Javaベースの開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM openjdk:11` は、OpenJDK 11を含む基本的なJavaイメージをベースとして使用します。これにより、Javaアプリケーションをコンパイルし実行するための環境が提供されます。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` は、bash、curl、git、vimなど、基本的な開発ツールをインストールします。これらはコードの編集やバージョン管理に便利です。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` は、インストールプロセス中に生成されたキャッシュを削除し、イメージのサイズを小さくします。
- **作業ディレクトリ**: `WORKDIR /app` はイメージ内の作業ディレクトリを`/app`に設定します。これはJavaアプリケーションのソースコードやビルドファイルを格納する場所となります。
- **デフォルトコマンド**: `CMD ["bash"]` はコンテナが起動した際にbashシェルを起動するように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してJava開発環境のコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `java-dev`: このセクションは`java-dev`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

### Kotlin

```Dockerfile:Dockerfile
# 基本イメージとしてOpenJDKを使用
FROM openjdk:11

# Kotlinのバージョンを指定
ENV KOTLIN_VERSION=1.6.10

# Kotlinコンパイラのダウンロードとインストール
RUN curl -sSL "https://github.com/JetBrains/kotlin/releases/download/v$KOTLIN_VERSION/kotlin-compiler-$KOTLIN_VERSION.zip" -o kotlin-compiler.zip \
    && unzip kotlin-compiler.zip -d /usr/share \
    && rm kotlin-compiler.zip

# 環境変数の設定
ENV PATH=$PATH:/usr/share/kotlinc/bin

# 作業ディレクトリの設定
WORKDIR /app

# ホストマシンとのフォルダ共有のためにボリュームを指定
VOLUME ["/app"]

# Bashの起動
CMD ["bash"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  kotlin-dev:
    build: .
    volumes:
      - .:/app
    tty: true
```

このDocker構成は、Kotlin言語の開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、Kotlinベースの開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM openjdk:11` は、OpenJDK 11を含む基本的なJavaイメージをベースとして使用します。KotlinはJVM（Java Virtual Machine）上で動作するため、Javaの実行環境が必要です。
- **環境変数**: `ENV KOTLIN_VERSION=1.6.10` で使用するKotlinのバージョンを指定します。
- **Kotlinコンパイラのインストール**:
  - `RUN curl -sSL [...] -o kotlin-compiler.zip` は、指定されたKotlinバージョンのコンパイラをダウンロードします。
  - `unzip kotlin-compiler.zip -d /usr/share` でダウンロードしたzipファイルを解凍し、`/usr/share`にインストールします。
  - `rm kotlin-compiler.zip` でzipファイルを削除し、容量を節約します。
- **環境変数の設定**: `ENV PATH=$PATH:/usr/share/kotlinc/bin` でKotlinコンパイラのパスを環境変数`PATH`に追加します。
- **作業ディレクトリ**: `WORKDIR /app` はイメージ内の作業ディレクトリを`/app`に設定します。
- **ボリューム**: `VOLUME ["/app"]` は、ホストマシンからのファイルをコンテナ内の`/app`ディレクトリにコピーするためのボリュームを設定します。
- **デフォルトコマンド**: `CMD ["bash"]` はコンテナが起動した際にbashシェルを起動するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してKotlin開発環境のコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `kotlin-dev`: このセクションは`kotlin-dev`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

### Mojo

```Dockerfile:Dockerfile
# Ubuntuイメージをベースにする
FROM ubuntu:23.04

# 必要なパッケージのインストール
RUN apt-get update && apt-get install -y \
    bash \
    git \
    vim \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Mojo言語のインストール
RUN curl https://get.modular.com | sh - && \
    modular auth mut_～ && \
    modular install mojo
# mut～はhttps://developer.modular.com/downloadで確認します。

# 作業ディレクトリの設定
WORKDIR /app

# コンテナ起動時に実行されるコマンド
CMD ["bash"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  mojo-dev:
    build: .
    volumes:
      - .:/app # 現在のディレクトリをコンテナの/appにマウント
    tty: true
```

このDocker構成は、UbuntuベースでMojo言語の開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、UbuntuベースのMojo言語開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM ubuntu:23.04` は、Ubuntu 23.04をベースイメージとして使用します。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` は、bash、git、vim、curlなどの基本的な開発ツールをインストールします。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` でインストールプロセス中に生成されたキャッシュを削除し、イメージのサイズを小さくします。
- **Mojo言語のインストール**:
  - `RUN curl https://get.modular.com | sh -` はModularをダウンロードし、インストールスクリプトを実行します。
  - `modular auth mut_～` ではModularの認証を行います。`mut_～`はModularのダウンロードページから入手可能なトークンです。
  - `modular install mojo` でMojo言語をインストールします。
- **作業ディレクトリ**: `WORKDIR /app` はイメージ内の作業ディレクトリを`/app`に設定します。
- **デフォルトコマンド**: `CMD ["bash"]` はコンテナが起動した際にbashシェルを起動するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してMojo言語開発環境のコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `mojo-dev`: このセクションは`mojo-dev`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

### Node.js

```Dockerfile:Dockerfile
# Node.jsバージョン20のイメージをベースにする
FROM node:20

# 必要なツールのインストール
RUN apt-get update && apt-get install -y \
    bash \
    git \
    vim \
    && rm -rf /var/lib/apt/lists/*

# 作業ディレクトリの設定
WORKDIR /app

# ホストマシンからのファイルコピー用ディレクトリ
VOLUME [ "/app" ]

# コンテナ起動時に実行されるコマンド
CMD ["bash"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  nodejs-dev:
    build: .
    volumes:
      - .:/app # 現在のディレクトリをコンテナの/appにマウント
    tty: true
```

このDocker構成は、Node.jsの開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、Node.jsベースの開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM node:20` は、Node.jsのバージョン20を含む基本的なイメージをベースとして使用します。このイメージにはNode.jsとnpmがプリインストールされており、Node.jsアプリケーションの開発に必要な環境が提供されます。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` は、bash、git、vimなどの基本的な開発ツールをインストールします。これらはコードの編集やバージョン管理に便利です。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` でインストールプロセス中に生成されたキャッシュを削除し、イメージのサイズを小さくします。
- **作業ディレクトリ**: `WORKDIR /app` はイメージ内の作業ディレクトリを`/app`に設定します。これはNode.jsアプリケーションのソースコードやビルドファイルを格納する場所となります。
- **ボリューム**: `VOLUME [ "/app" ]` は、ホストマシンからのファイルをコンテナ内の`/app`ディレクトリにコピーするためのボリュームを設定します。
- **デフォルトコマンド**: `CMD ["bash"]` はコンテナが起動した際にbashシェルを起動するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してNode.js開発環境のコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `nodejs-dev`: このセクションは`nodejs-dev`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

### PHP

```Dockerfile:Dockerfile
# PHPイメージをベースにする
FROM php:8.0-apache

# 必要なパッケージのインストール
RUN apt-get update && apt-get install -y \
    git \
    vim \
    && rm -rf /var/lib/apt/lists/*

# Composerのインストール
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# 作業ディレクトリの設定
WORKDIR /var/www/html

# Apacheの設定
COPY .docker/apache.conf /etc/apache2/sites-available/000-default.conf

# コンテナ起動時に実行されるコマンド
CMD ["apache2-foreground"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  php-app:
    build: .
    volumes:
      - .:/var/www/html # 現在のディレクトリをコンテナの/var/www/htmlにマウント
    ports:
      - "80:80" # コンテナの80ポートをホストの80ポートにマッピング
    tty: true
```

このDocker構成は、PHPとApacheを使用したWebアプリケーションの開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、PHPとApacheをベースにした開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM php:8.0-apache` は、PHP 8.0を含むApacheサーバーをベースイメージとして使用します。これにより、PHPアプリケーションの実行環境が提供されます。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` は、git、vimなどの開発ツールをインストールします。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` でインストールプロセス中に生成されたキャッシュを削除し、イメージのサイズを小さくします。
- **Composerのインストール**: `COPY --from=composer:latest /usr/bin/composer /usr/bin/composer` は、最新のComposer（PHPの依存関係管理ツール）を別のコンテナからコピーし、インストールします。
- **作業ディレクトリ**: `WORKDIR /var/www/html` は、Apacheサーバーのデフォルトのドキュメントルートを作業ディレクトリとして設定します。
- **Apache設定のコピー**: `COPY .docker/apache.conf /etc/apache2/sites-available/000-default.conf` はカスタムApache設定をコンテナにコピーします。
- **デフォルトコマンド**: `CMD ["apache2-foreground"]` はコンテナが起動した際にApacheをフォアグラウンドで実行するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してPHPアプリケーションのコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `php-app`: このセクションは`php-app`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `volumes: - .:/var/www/html` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/var/www/html`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `ports: - "80:80"` は、ホストマシンの80ポートをコンテナの80ポートにマッピングします。これにより、ホストのWebブラウザからコンテナ内のPHPアプリケーションにアクセスできます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。

### Python

```Dockerfile:Dockerfile
# Pythonイメージをベースにする
FROM python:3.10

# 必要なツールのインストール
RUN apt-get update && apt-get install -y \
    bash \
    git \
    vim \
    && rm -rf /var/lib/apt/lists/*

# 作業ディレクトリの設定
WORKDIR /app

# 依存関係ファイルのコピー（存在する場合）
COPY requirements.txt /app/

# Python依存関係のインストール
RUN pip install --no-cache-dir -r requirements.txt

# コンテナ起動時に実行されるコマンド
CMD ["bash"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  python-dev:
    build: .
    volumes:
      - .:/app # 現在のディレクトリをコンテナの/appにマウント
    tty: true
```

このDocker構成は、Python 3.10をベースにした開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、Pythonベースの開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM python:3.10` は、Python 3.10をベースイメージとして使用します。これにはPythonとpipがプリインストールされており、Pythonアプリケーションの開発に必要な環境が提供されます。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` は、bash、git、vimなどの基本的な開発ツールをインストールします。これらはコードの編集やバージョン管理に便利です。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` でインストールプロセス中に生成されたキャッシュを削除し、イメージのサイズを小さくします。
- **作業ディレクトリ**: `WORKDIR /app` はイメージ内の作業ディレクトリを`/app`に設定します。これはPythonアプリケーションのソースコードやビルドファイルを格納する場所となります。
- **依存関係ファイルのコピー**: `COPY requirements.txt /app/` はホストマシンの`requirements.txt`ファイルをコンテナ内の`/app`ディレクトリにコピーします。このファイルにはPythonの依存関係が記載されています。
- **Python依存関係のインストール**: `RUN pip install --no-cache-dir -r requirements.txt` は`requirements.txt`で指定された依存関係をインストールします。
- **デフォルトコマンド**: `CMD ["bash"]` はコンテナが起動した際にbashシェルを起動するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してPython開発環境のコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `python-dev`: このセクションは`python-dev`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

### Rust

```Dockerfile:Dockerfile
# Rustの公式イメージを使用
FROM rust:latest

# 作業ディレクトリの設定
WORKDIR /usr/src/myapp

# ホストマシンとのフォルダ共有のためにボリュームを指定
VOLUME ["/usr/src/myapp"]

# Bashの起動
CMD ["bash"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  rust-dev:
    build: .
    volumes:
      - .:/usr/src/myapp
    tty: true
```

このDocker構成は、Rustプログラミング言語の開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、Rust言語ベースの開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM rust:latest` は、Rustの最新公式イメージをベースとして使用します。このイメージにはRust言語とCargo（Rustのパッケージマネージャーとビルドツール）がプリインストールされています。
- **作業ディレクトリ**: `WORKDIR /usr/src/myapp` はイメージ内の作業ディレクトリを`/usr/src/myapp`に設定します。これはRustプロジェクトのソースコードやビルドファイルを格納する場所となります。
- **ボリューム**: `VOLUME ["/usr/src/myapp"]` は、ホストマシンからのファイルをコンテナ内の`/usr/src/myapp`ディレクトリにコピーするためのボリュームを設定します。
- **デフォルトコマンド**: `CMD ["bash"]` はコンテナが起動した際にbashシェルを起動するように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してRust開発環境のコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `rust-dev`: このセクションは`rust-dev`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `volumes: - .:/usr/src/myapp` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/usr/src/myapp`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。

### Swift

```yml:docker-compose.yml
version: '3.8'

services:
  swift:
    image: swift:latest
    volumes:
      - .:/app
    working_dir: /app
    tty: true
    command: /bin/bash
```

このDocker構成は、Swift言語の開発環境をセットアップするためのものです。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、Swift開発環境のコンテナを設定します。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `swift`: このセクションは`swift`という名前のサービスを定義します。
  - `image: swift:latest` は、Swiftの最新の公式Dockerイメージを使用することを指定します。これにはSwift言語と必要なツールがプリインストールされています。
  - `volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `working_dir: /app` は、コンテナ内での作業ディレクトリを`/app`に設定します。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。これにより、コンテナ内でインタラクティブな作業が可能になります。
  - `command: /bin/bash` は、コンテナが起動するときに実行されるコマンドを指定します。この設定はbashシェルを起動し、コンテナ内での作業を容易にします。

## フロントエンド関連

### Angular

```Dockerfile:Dockerfile
# Node.jsイメージをベースにする
FROM node:20

# 必要なツールのインストール
RUN apt-get update && apt-get install -y \
    bash \
    git \
    vim \
    && rm -rf /var/lib/apt/lists/*

# 作業ディレクトリの設定
WORKDIR /app

# パッケージ依存関係のコピー
COPY package.json /app/
COPY package-lock.json /app/

# 依存関係のインストール
RUN npm install

# アプリケーションのファイルをコピー
COPY . /app/

# Angularアプリケーションを起動するコマンド
CMD ["npm", "start"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  angular-app:
    build: .
    ports:
      - "4200:4200" # Angularアプリのデフォルトポートをホストとマッピング
    volumes:
      - .:/app # 現在のディレクトリをコンテナの/appにマウント
    tty: true
```

このDocker構成は、Node.jsとAngularを使用したWebアプリケーション開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、Angularアプリケーションの開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM node:20` は、Node.jsのバージョン20を含む基本的なイメージをベースとして使用します。Node.jsはAngularアプリケーションの開発に必要な環境を提供します。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` は、bash、git、vimなどの開発ツールをインストールします。これらはコードの編集やバージョン管理に便利です。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` でインストールプロセス中に生成されたキャッシュを削除し、イメージのサイズを小さくします。
- **作業ディレクトリ**: `WORKDIR /app` はイメージ内の作業ディレクトリを`/app`に設定します。これはアプリケーションのソースコードやビルドファイルを格納する場所となります。
- **依存関係ファイルのコピー**: `COPY package.json /app/` と `COPY package-lock.json /app/` はホストマシンの依存関係ファイルをコンテナ内の`/app`ディレクトリにコピーします。
- **依存関係のインストール**: `RUN npm install` は`package.json`と`package-lock.json`で指定された依存関係をインストールします。
- **アプリケーションのファイルのコピー**: `COPY . /app/` はホストマシンの現在のディレクトリのすべてのファイルをコンテナの`/app`ディレクトリにコピーします。
- **デフォルトコマンド**: `CMD ["npm", "start"]` はコンテナが起動した際にAngularアプリケーションを起動するコマンドを実行するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してAngularアプリケーションのコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `angular-app`: このセクションは`angular-app`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `ports: - "4200:4200"` は、ホストマシンの4200ポートをコンテナの4200ポートにマッピングします。これにより、ホストのWebブラウザからコンテナ内のAngularアプリケーションにアクセスできます。
  - `volumes: -

 .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。

### React

```Dockerfile:Dockerfile
# Node.jsイメージをベースにする
FROM node:20.9

# 必要なツールのインストール
RUN apt-get update && apt-get install -y \
    bash \
    git \
    vim \
    && rm -rf /var/lib/apt/lists/*

# 作業ディレクトリの設定
WORKDIR /app

# パッケージ依存関係のコピー
COPY package.json /app/
COPY package-lock.json /app/

# 依存関係のインストール
RUN npm install

# アプリケーションのファイルをコピー
COPY . /app/

# Reactアプリケーションを起動するコマンド
CMD ["npm", "start"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  react-app:
    build: .
    ports:
      - "3000:3000" # Reactアプリのデフォルトポートをホストとマッピング
    volumes:
      - .:/app # 現在のディレクトリをコンテナの/appにマウント
    tty: true
```

このDocker構成は、Node.jsとReactを使用したWebアプリケーション開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、Reactアプリケーションの開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM node:20.9` は、Node.jsのバージョン20.9を含む基本的なイメージをベースとして使用します。Node.jsはReactアプリケーションの開発に必要な環境を提供します。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` は、bash、git、vimなどの開発ツールをインストールします。これらはコードの編集やバージョン管理に便利です。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` でインストールプロセス中に生成されたキャッシュを削除し、イメージのサイズを小さくします。
- **作業ディレクトリ**: `WORKDIR /app` はイメージ内の作業ディレクトリを`/app`に設定します。これはアプリケーションのソースコードやビルドファイルを格納する場所となります。
- **依存関係ファイルのコピー**: `COPY package.json /app/` と `COPY package-lock.json /app/` はホストマシンの依存関係ファイルをコンテナ内の`/app`ディレクトリにコピーします。
- **依存関係のインストール**: `RUN npm install` は`package.json`と`package-lock.json`で指定された依存関係をインストールします。
- **アプリケーションのファイルのコピー**: `COPY . /app/` はホストマシンの現在のディレクトリのすべてのファイルをコンテナの`/app`ディレクトリにコピーします。
- **デフォルトコマンド**: `CMD ["npm", "start"]` はコンテナが起動した際にReactアプリケーションを起動するコマンドを実行するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してReactアプリケーションのコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `react-app`: このセクションは`react-app`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `ports: - "3000:3000"` は、ホストマシンの3000ポートをコンテナの3000ポートにマッピングします。これにより、ホストのWebブラウザからコンテナ内のReactアプリケーションにアクセスできます。
  - `

volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。

### Vue

```Dockerfile:Dockerfile
# Node.jsイメージをベースにする
FROM node:20

# 必要なツールのインストール
RUN apt-get update && apt-get install -y \
    bash \
    git \
    vim \
    && rm -rf /var/lib/apt/lists/*

# 作業ディレクトリの設定
WORKDIR /app

# パッケージ依存関係のコピー
COPY package.json /app/
# yarnを使用している場合
# COPY yarn.lock /app/

# 依存関係のインストール
RUN npm install
# yarnを使用している場合
# RUN yarn install

# アプリケーションのファイルをコピー
COPY . /app/

# Vueアプリケーションを起動するコマンド
CMD ["npm", "run", "serve"]
# yarnを使用している場合
# CMD ["yarn", "serve"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  vue-app:
    build: .
    ports:
      - "8080:8080" # Vueアプリのデフォルトポートをホストとマッピング
    volumes:
      - .:/app # 現在のディレクトリをコンテナの/appにマウント
    tty: true
```

このDocker構成は、Node.jsとVue.jsを使用したWebアプリケーション開発環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、Vue.jsアプリケーションの開発環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM node:20` は、Node.jsのバージョン20を含む基本的なイメージをベースとして使用します。Node.jsはVue.jsアプリケーションの開発に必要な環境を提供します。
- **パッケージのインストール**: `RUN apt-get update && apt-get install -y [...]` は、bash、git、vimなどの開発ツールをインストールします。これらはコードの編集やバージョン管理に便利です。
- **キャッシュのクリア**: `&& rm -rf /var/lib/apt/lists/*` でインストールプロセス中に生成されたキャッシュを削除し、イメージのサイズを小さくします。
- **作業ディレクトリ**: `WORKDIR /app` はイメージ内の作業ディレクトリを`/app`に設定します。これはアプリケーションのソースコードやビルドファイルを格納する場所となります。
- **依存関係ファイルのコピー**: `COPY package.json /app/` はホストマシンの`package.json`ファイルをコンテナ内の`/app`ディレクトリにコピーします。yarnを使用している場合は、`yarn.lock`ファイルもコピーします。
- **依存関係のインストール**: `RUN npm install` または `RUN yarn install` は、依存関係をインストールします。
- **アプリケーションのファイルのコピー**: `COPY . /app/` はホストマシンの現在のディレクトリのすべてのファイルをコンテナの`/app`ディレクトリにコピーします。
- **デフォルトコマンド**: `CMD ["npm", "run", "serve"]` または `CMD ["yarn", "serve"]` はコンテナが起動した際にVueアプリケーションを起動するコマンドを実行するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してVue.jsアプリケーションのコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `vue-app`: このセクションは`vue-app`という名前のサービスを定義します。
  - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
  - `ports: - "8080:8080"` は、ホストマシンの8080ポートをコンテナの8080ポートにマッピングします。これにより、ホストのWebブラウザからコンテナ内の

Vue.jsアプリケーションにアクセスできます。
  - `volumes: - .:/app` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/app`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
  - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。

## サービス関連

### GitBucket

```yml:docker-compose.yml
version: '3.8'

services:
  gitbucket:
    image: gitbucket/gitbucket:latest
    ports:
      - "8080:8080"
      - "29418:29418" # Git SSH port
    volumes:
      - ./gitbucket:/gitbucket
    environment:
      - GITBUCKET_HOME=/gitbucket
    depends_on:
      - db

  db:
    image: mysql:5.7
    volumes:
      - ./db_data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=rootpassword
      - MYSQL_DATABASE=gitbucket
      - MYSQL_USER=gitbucket
      - MYSQL_PASSWORD=password
```

このDocker構成は、GitBucketとMySQLを使用したソースコード管理とコラボレーションプラットフォームをセットアップするためのものです。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、GitBucketとMySQLのコンテナを設定します。

- **バージョン**: `version: '3.8'` は使用するDocker Composeファイルのバージョンを指定します。

- **サービス**:
  - `gitbucket`: 
    - `image: gitbucket/gitbucket:latest` は、GitBucketの最新版公式Dockerイメージを使用することを指定します。GitBucketはGitベースのソースコード管理ツールです。
    - `ports: - "8080:8080" - "29418:29418"` は、ホストマシンの8080ポートと29418ポートをそれぞれコンテナの同じポートにマッピングします。8080はGitBucketのWebインターフェース用、29418はGit SSH用です。
    - `volumes: - ./gitbucket:/gitbucket` は、ホストマシンの`./gitbucket`ディレクトリをコンテナの`/gitbucket`ディレクトリにマウントします。これにより、GitBucketのデータが永続化されます。
    - `environment: - GITBUCKET_HOME=/gitbucket` は、GitBucketのデータ保存先をコンテナ内の`/gitbucket`に設定します。
    - `depends_on: - db` は、`gitbucket`サービスが`db`サービスに依存していることを示し、`db`サービスが先に起動することを保証します。

  - `db`:
    - `image: mysql:5.7` は、MySQLのバージョン5.7の公式Dockerイメージを使用することを指定します。
    - `volumes: - ./db_data:/var/lib/mysql` は、ホストマシンの`./db_data`ディレクトリをコンテナの`/var/lib/mysql`ディレクトリにマウントします。これにより、MySQLデータベースのデータが永続化されます。
    - `environment` セクションはMySQLデータベースのルートユーザーパスワード、データベース名、ユーザー名、ユーザーパスワードを設定します。

### Gitea

```yml:docker-compose.yml
version: '3.8'

services:
  gitbucket:
    image: gitbucket/gitbucket:latest
    ports:
      - "8080:8080"
      - "29418:29418" # Git SSH port
    volumes:
      - ./gitbucket:/gitbucket
    environment:
      - GITBUCKET_HOME=/gitbucket
    depends_on:
      - db

  db:
    image: mysql:5.7
    volumes:
      - ./db_data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=rootpassword
      - MYSQL_DATABASE=gitbucket
      - MYSQL_USER=gitbucket
      - MYSQL_PASSWORD=password
```

このDocker構成は、GitBucketとMySQLを使用したソースコード管理とコラボレーションプラットフォームをセットアップするためのものです。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、GitBucketとMySQLのコンテナを設定します。

- **バージョン**: `version: '3.8'` は使用するDocker Composeファイルのバージョンを指定します。

- **サービス**:
  - `gitbucket`: 
    - `image: gitbucket/gitbucket:latest` は、GitBucketの最新版公式Dockerイメージを使用することを指定します。GitBucketはGitベースのソースコード管理ツールです。
    - `ports: - "8080:8080" - "29418:29418"` は、ホストマシンの8080ポートと29418ポートをそれぞれコンテナの同じポートにマッピングします。8080はGitBucketのWebインターフェース用、29418はGit SSH用です。
    - `volumes: - ./gitbucket:/gitbucket` は、ホストマシンの`./gitbucket`ディレクトリをコンテナの`/gitbucket`ディレクトリにマウントします。これにより、GitBucketのデータが永続化されます。
    - `environment: - GITBUCKET_HOME=/gitbucket` は、GitBucketのデータ保存先をコンテナ内の`/gitbucket`に設定します。
    - `depends_on: - db` は、`gitbucket`サービスが`db`サービスに依存していることを示し、`db`サービスが先に起動することを保証します。

  - `db`:
    - `image: mysql:5.7` は、MySQLのバージョン5.7の公式Dockerイメージを使用することを指定します。
    - `volumes: - ./db_data:/var/lib/mysql` は、ホストマシンの`./db_data`ディレクトリをコンテナの`/var/lib/mysql`ディレクトリにマウントします。これにより、MySQLデータベースのデータが永続化されます。
    - `environment` セクションはMySQLデータベースのルートユーザーパスワード、データベース名、ユーザー名、ユーザーパスワードを設定します。

### JMater

```Dockerfile:Dockerfile
# Javaの公式イメージをベースに使用
FROM openjdk:8

# JMeterのバージョンを指定
ENV JMETER_VERSION 5.4.1

# JMeterのダウンロードとインストール
RUN wget https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-${JMETER_VERSION}.tgz \
    && tar -xzf apache-jmeter-${JMETER_VERSION}.tgz -C /opt \
    && rm apache-jmeter-${JMETER_VERSION}.tgz

# 環境変数の設定
ENV JMETER_HOME /opt/apache-jmeter-${JMETER_VERSION}
ENV PATH $JMETER_HOME/bin:$PATH

# 作業ディレクトリの設定
WORKDIR /jmeter

# ホストマシンとのフォルダ共有のためにボリュームを指定
VOLUME ["/jmeter"]

# Bashの起動
CMD ["bash"]
```

```yml:docker-compose.yml
version: '3.8'

services:
  jmeter:
    build: .
    volumes:
      - .:/jmeter
    tty: true
```

このDocker構成は、Apache JMeterを使用したパフォーマンステストおよび負荷テストの環境をセットアップするためのものです。

#### Dockerfile 解説

この`Dockerfile`は、JMeterのテスト環境のカスタムイメージを作成するための指示を含んでいます。

- **ベースイメージ**: `FROM openjdk:8` は、Java 8を含む基本的なイメージをベースとして使用します。JMeterはJavaで動作するため、Javaの実行環境が必要です。
- **JMeterバージョンの指定**: `ENV JMETER_VERSION 5.4.1` で使用するJMeterのバージョンを指定します。
- **JMeterのダウンロードとインストール**:
  - `RUN wget [...]` は、指定されたJMeterバージョンのバイナリをダウンロードします。
  - `tar -xzf [...] -C /opt` でダウンロードしたtarファイルを解凍し、`/opt`にインストールします。
  - `rm apache-jmeter-${JMETER_VERSION}.tgz` でダウンロードしたtarファイルを削除し、容量を節約します。
- **環境変数の設定**: `ENV JMETER_HOME /opt/apache-jmeter-${JMETER_VERSION}` と `ENV PATH $JMETER_HOME/bin:$PATH` でJMeterのホームディレクトリと実行パスを環境変数に設定します。
- **作業ディレクトリ**: `WORKDIR /jmeter` はイメージ内の作業ディレクトリを`/jmeter`に設定します。
- **ボリューム**: `VOLUME ["/jmeter"]` は、ホストマシンからのファイルをコンテナ内の`/jmeter`ディレクトリにコピーするためのボリュームを設定します。
- **デフォルトコマンド**: `CMD ["bash"]` はコンテナが起動した際にbashシェルを起動するように設定します。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、上記の`Dockerfile`を使用してJMeterのコンテナをセットアップします。

- **バージョン**: `version: '3.8'` は使用するComposeファイルのバージョンを指定します。
- **サービス**:
  - `jmeter`: 
    - `build: .` はDocker Composeに、現在のディレクトリにある`Dockerfile`を使用してイメージをビルドするように指示します。
    - `volumes: - .:/jmeter` は、ホストマシンの現在のディレクトリ（`.`）をコンテナの`/jmeter`ディレクトリにマウントします。これにより、ホスト上のファイルがコンテナ内で直接編集できます。
    - `tty: true` はコンテナがバックグラウンドで実行されるのではなく、ターミナルに接続された状態で実行されるように設定します。

### Mattermost

```yml:docker-compose.yml
version: '3.8'

services:
  mattermost:
    image: mattermost/mattermost-team:latest
    ports:
      - "8065:8065"
    volumes:
      - ./config:/mattermost/config
    depends_on:
      - db

  db:
    image: mysql:5.7
    volumes:
      - ./db_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: mattermost
      MYSQL_USER: mattermost
      MYSQL_PASSWORD: password
```

このDocker構成は、Mattermost（チームコミュニケーションツール）とMySQLデータベースを使用してコラボレーションプラットフォームをセットアップするためのものです。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、MattermostとMySQLのコンテナを設定します。

- **バージョン**: `version: '3.8'` は使用するDocker Composeファイルのバージョンを指定します。

- **サービス**:
  - `mattermost`: 
    - `image: mattermost/mattermost-team:latest` は、Mattermostチームエディションの最新版公式Dockerイメージを使用することを指定します。Mattermostはオープンソースのチームメッセージングソリューションです。
    - `ports: - "8065:8065"` は、ホストマシンの8065ポートをコンテナの8065ポートにマッピングします。これにより、ホストからMattermostサーバーにアクセスできます。
    - `volumes: - ./config:/mattermost/config` は、ホストマシンの`./config`ディレクトリをコンテナの`/mattermost/config`ディレクトリにマウントします。これにより、Mattermostの設定が永続化されます。
    - `depends_on: - db` は、`mattermost`サービスが`db`サービスに依存していることを示し、`db`サービスが先に起動することを保証します。

  - `db`:
    - `image: mysql:5.7` は、MySQLのバージョン5.7の公式Dockerイメージを使用することを指定します。
    - `volumes: - ./db_data:/var/lib/mysql` は、ホストマシンの`./db_data`ディレクトリをコンテナの`/var/lib/mysql`ディレクトリにマウントします。これにより、MySQLデータベースのデータが永続化されます。
    - `environment` セクションはMySQLデータベースのルートユーザーパスワード、データベース名、ユーザー名、ユーザーパスワードを設定します。


### Redmine

```yml:docker-compose.yml
version: '3.8'

services:
  redmine:
    image: redmine:latest
    ports:
      - "3000:3000"
    volumes:
      - ./redmine_data:/usr/src/redmine/files
    environment:
      REDMINE_DB_MYSQL: db
      REDMINE_DB_DATABASE: redmine
      REDMINE_DB_USERNAME: redmine
      REDMINE_DB_PASSWORD: password
    depends_on:
      - db

  db:
    image: mysql:8.0
    volumes:
      - ./db_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: redmine
      MYSQL_USER: redmine
      MYSQL_PASSWORD: password
```

このDocker構成は、Redmine（プロジェクト管理ツール）とMySQLデータベースを使用してプロジェクト管理プラットフォームをセットアップするためのものです。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、RedmineとMySQLのコンテナを設定します。

- **バージョン**: `version: '3.8'` は使用するDocker Composeファイルのバージョンを指定します。

- **サービス**:
  - `redmine`: 
    - `image: redmine:latest` は、Redmineの最新版公式Dockerイメージを使用することを指定します。Redmineはオープンソースのプロジェクト管理ツールです。
    - `ports: - "3000:3000"` は、ホストマシンの3000ポートをコンテナの3000ポートにマッピングします。これにより、ホストからRedmineサーバーにアクセスできます。
    - `volumes: - ./redmine_data:/usr/src/redmine/files` は、ホストマシンの`./redmine_data`ディレクトリをコンテナの`/usr/src/redmine/files`ディレクトリにマウントします。これにより、Redmineのファイルが永続化されます。
    - `environment` セクションは、Redmineが使用するMySQLデータベースの接続情報を設定します。
    - `depends_on: - db` は、`redmine`サービスが`db`サービスに依存していることを示し、`db`サービスが先に起動することを保証します。

  - `db`:
    - `image: mysql:8.0` は、MySQLのバージョン8.0の公式Dockerイメージを使用することを指定します。
    - `volumes: - ./db_data:/var/lib/mysql` は、ホストマシンの`./db_data`ディレクトリをコンテナの`/var/lib/mysql`ディレクトリにマウントします。これにより、MySQLデータベースのデータが永続化されます。
    - `environment` セクションはMySQLデータベースのルートユーザーパスワード、データベース名、ユーザー名、ユーザーパスワードを設定します。

### WordPress

```yml:docker-compose.yml
version: '3.8'

services:
  wordpress:
    image: wordpress:latest
    ports:
      - "80:80"
    volumes:
      - ./wordpress:/var/www/html
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: password
      WORDPRESS_DB_NAME: wordpress
    depends_on:
      - db

  db:
    image: mysql:8.0
    volumes:
      - ./db_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: password
```

このDocker構成は、WordPressとMySQLデータベースを使用してコンテンツ管理システムをセットアップするためのものです。

#### docker-compose.yml 解説

この`docker-compose.yml`ファイルは、WordPressとMySQLのコンテナを設定します。

- **バージョン**: `version: '3.8'` は使用するDocker Composeファイルのバージョンを指定します。

- **サービス**:
  - `wordpress`: 
    - `image: wordpress:latest` は、WordPressの最新版公式Dockerイメージを使用することを指定します。WordPressは人気のあるオープンソースのコンテンツ管理システムです。
    - `ports: - "80:80"` は、ホストマシンの80ポートをコンテナの80ポートにマッピングします。これにより、ホストのWebブラウザからWordPressサイトにアクセスできます。
    - `volumes: - ./wordpress:/var/www/html` は、ホストマシンの`./wordpress`ディレクトリをコンテナの`/var/www/html`ディレクトリにマウントします。これにより、WordPressのデータが永続化されます。
    - `environment` セクションは、WordPressが使用するMySQLデータベースの接続情報を設定します。
    - `depends_on: - db` は、`wordpress`サービスが`db`サービスに依存していることを示し、`db`サービスが先に起動することを保証します。

  - `db`:
    - `image: mysql:8.0` は、MySQLのバージョン8.0の公式Dockerイメージを使用することを指定します。
    - `volumes: - ./db_data:/var/lib/mysql` は、ホストマシンの`./db_data`ディレクトリをコンテナの`/var/lib/mysql`ディレクトリにマウントします。これにより、MySQLデータベースのデータが永続化されます。
    - `environment` セクションはMySQLデータベースのルートユーザーパスワード、データベース名、ユーザー名、ユーザーパスワードを設定します。
