---
title: nvmのインストール方法【Node Version Manager】
tags:
  - Node.js
  - npm
  - nvm
private: false
updated_at: '2023-04-05T23:44:48+09:00'
id: f2a0bc2de007da30bbfd
organization_url_name: null
slide: false
---
# nvmのインストール方法
1.Node Version Manager（nvm）をダウンロードする。
  - https://github.com/coreybutler/nvm-windows/releases
    ![01.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/e54ff160-1148-0f33-84ce-3d91a937530b.png)
2.`nvm-setup.exe` を起動する。
![02.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/10333520-758f-f790-6640-787478746c65.png)
3.`I accept the agreement` を選択し、 `Next` を押下する。
![03.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/dde7e18a-da31-6578-82a2-088ef5eeaf53.png)
4.`Next` を押下する。
![04.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/c337a0b3-b005-134c-e132-67c3de8549b8.png)
5.`Next` を押下する。
![05.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/5a61ca97-95bf-d27a-8d36-b82f50614072.png)
6.`Install` を押下する。
![06.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/717e4ca8-2cdf-0ea7-23dd-8001aee9b947.png)
7.`Finish` を押下する。

# node.jsインストール
1.`nvm version` コマンドでバージョンを確認する。
```
>nvm version
1.1.10
```
2.`nvm list available` コマンドでインストール可能なnode.jsバージョンを確認する。
```
>nvm list available

|   CURRENT    |     LTS      |  OLD STABLE  | OLD UNSTABLE |
|--------------|--------------|--------------|--------------|
|    19.8.1    |   18.15.0    |   0.12.18    |   0.11.16    |
|    19.8.0    |   18.14.2    |   0.12.17    |   0.11.15    |
|    19.7.0    |   18.14.1    |   0.12.16    |   0.11.14    |
|    19.6.1    |   18.14.0    |   0.12.15    |   0.11.13    |
|    19.6.0    |   18.13.0    |   0.12.14    |   0.11.12    |
|    19.5.0    |   18.12.1    |   0.12.13    |   0.11.11    |
|    19.4.0    |   18.12.0    |   0.12.12    |   0.11.10    |
|    19.3.0    |   16.20.0    |   0.12.11    |    0.11.9    |
|    19.2.0    |   16.19.1    |   0.12.10    |    0.11.8    |
|    19.1.0    |   16.19.0    |    0.12.9    |    0.11.7    |
|    19.0.1    |   16.18.1    |    0.12.8    |    0.11.6    |
|    19.0.0    |   16.18.0    |    0.12.7    |    0.11.5    |
|   18.11.0    |   16.17.1    |    0.12.6    |    0.11.4    |
|   18.10.0    |   16.17.0    |    0.12.5    |    0.11.3    |
|    18.9.1    |   16.16.0    |    0.12.4    |    0.11.2    |
|    18.9.0    |   16.15.1    |    0.12.3    |    0.11.1    |
|    18.8.0    |   16.15.0    |    0.12.2    |    0.11.0    |
|    18.7.0    |   16.14.2    |    0.12.1    |    0.9.12    |
|    18.6.0    |   16.14.1    |    0.12.0    |    0.9.11    |
|    18.5.0    |   16.14.0    |   0.10.48    |    0.9.10    |

This is a partial list. For a complete list, visit https://nodejs.org/en/download/releases
```

3.`nvm install 【バージョン】` コマンドでnode.jsをインストールする。
```
>nvm install 18.15.0
Downloading node.js version 18.15.0 (64-bit)...
Extracting node and npm...
Complete
npm v9.5.0 installed successfully.


Installation complete. If you want to use this version, type

nvm use 18.15.0
```

4.`nvm list` コマンドでインストールしたnode.jsバージョンを確認する。
```
>nvm list

    18.15.0
```

5.`nvm use 【バージョン】` で使用するnode.jsバージョンを選択する。
```
>nvm use 18.15.0
Now using node v18.15.0 (64-bit)
```

6.node.jsのバージョンを確認する。
```
>node -v
v18.15.0

>npm -v
9.5.0
```

