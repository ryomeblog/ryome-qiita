---
title: Docker汎用コマンド集
tags:
  - Docker
private: false
updated_at: '2022-04-20T10:54:08+09:00'
id: 12d8fb5f64acfbdd22e9
organization_url_name: null
slide: false
ignorePublish: false
---
# Docker汎用コマンド集

- Apacheコンテナを作成、実行

```
docker container run --name myapache -d -it -p 8000:80 -v /tmp:/usr/local/apache2/htdocs httpd
```

--- 

- コンテナ内にファイルをコピー

```
sudo docker cp コンテナID:コピー先パス コピー元パス
sudo docker cp コピー元パス コンテナID:コピー先パス
```

--- 

- コンテナにボリュームをマウント

```
docker run -it --mount source=ボリューム名,target=マウント先パス イメージ名:Tag名
```

--- 

- コンテナをすべて削除

```
docker rm -f $(docker ps -a -q)
```

--- 

- イメージをすべて削除

```
docker rmi -f $(docker images -q)
```

--- 

- ボリュームをすべて削除

```
docker volume rm $(docker volume ls -q)
```

--- 

- 起動中のコンテナにコマンドを実行する 

```
docker exec -it コンテナ名 /bin/bash
```
