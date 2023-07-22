---
title: 【実践‼】Spring Boot で Hello World を表示する
tags:
  - Java
  - Maven
  - SpringBoot
private: false
updated_at: '2022-05-01T01:15:29+09:00'
id: 8d7f71ebedeeb2526cee
organization_url_name: null
slide: false
---
## 1.事前知識
- [【初心者でもできる‼】Windows10にEclipseをインストールする方法（Java環境構築）](https://qiita.com/ryome/items/b47c5acdaa52cffbec58)

事前知識として、上記リンクの内容が必要です。

## 2.事前準備

![01.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/594d8a72-235d-b356-a3b2-dbfbfd0159c9.png)
1. Eclipseを起動する。
![02.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/45df6d6a-46e0-f2bf-1f4c-017db75f9727.png)
2. `[ウィンドウ(W)]→[パースペクティブを開く]→[その他]` を選択する。
![03.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/beb161c8-9a03-17d6-d351-b531cdcb54d1.png)
3. `JavaEE`  を選択して、 `開く` ボタンをクリックする。
![04.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/a26bd90e-5101-b752-9653-8adb7d9ba902.png)
4. `[ヘルプ(H)]→[Eclipse マーケットプレース(M)...]` を選択する。　　　
![05.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/7b6c37a3-64f9-4c59-0f4e-0ffeb2f4031d.png)
5. `Spring Tools 4 (aka Spring Tool Suite 4) 4.7.0.RELEASE` を `インストール` する。



## 3.Spring Boot プロジェクトの作成

```bash:フォルダ構成
Hello
└─ src
     └─ main
          ├─ java
          │   └─ com
          │        └─ example
          │             └─ demo
          │                  ├─ HelloController.java
          │                  └─ HelloApplication.java
          └─ resources
               ├─ application.properties
               │  
               ├─ static
               └─ templates
                    └─ index.html
```

```java:HelloController.java
package com.example.demo;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
public class HelloController {
    @RequestMapping(value = "/", method = RequestMethod.GET)
    public String index(Model model) {
        model.addAttribute("message", "Hello Springboot");
        return "index";
    }
}
```

```html:index.html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
  <head>
    <title>Hello</title>
    <meta charset="utf-8" />
  </head>
  <body>
    <h1>Hello World</h1>
    <p>
      <span th:text="${message}"></span>!!!
    </p>
  </body>
</html>
```

![06.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/4f942089-ee64-25b8-259f-3ff278f66d79.png)
1. `[ファイル(F)]→[新規(N)]→[Spring スターター・プロジェクト]` を選択する。
![07.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/88e04f36-d045-580e-c11f-2fff6b76e711.png)
2. 名前に `Hello` と入力し、 `次へ(N) >` ボタンをクリックする。
![08.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/171d7ab4-828e-75da-3f0d-4cca72875357.png)
3. `[テンプレート・エンジン]→[Thymeleaf]` と `[Web]→[Spring Web]` を選択し、`完了` ボタンをクリックする。　　　
![09.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/33c6251f-9bfe-3fd9-f22a-2e90e0ffccaf.png)
4. `com.example.demo` を右クリックして `[新規(N)]→[クラス]` 選択する。
![10.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/ea5ab1d1-6943-3ff0-167b-02a27ecfa49e.png)
5. 名前に `HelloController` と入力し、 `完了(F)` ボタンをクリックする。
![11.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/e35bf21c-d005-b637-3d58-2eacb924568b.png)
6. `templates` を右クリックして `[新規(N)]→[その他]` 選択する。
![12.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/0569ec8c-8853-6e8a-47a9-807ad42dca33.png)
7. `[Web]→[HTML ファイル]` を選択し、`次へ` ボタンをクリックする。　
![13.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/620d70d5-fbc2-8101-4722-38cb073aceef.png)
8. `ファイル名(M)` に `index.html` と入力し、 `完了(F)` ボタンをクリックする。　
![14.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/d45c64d2-dd02-60d5-b8e5-59a5614f5063.png)
9. `Hello [boot]` を右クリックして `[実行(R)]→[5 Maven install]` 選択する。
※ `HelloController.java` と `index.html` は[上記に記載](#3spring-boot-プロジェクトの作成)。
![15.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/481ed588-609d-49a2-3750-988a813e5d9b.png)

```
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  4.125 s
[INFO] Finished at: 2020-07-05T21:50:22+09:00
[INFO] ------------------------------------------------------------------------
```
10.`コンソール` に 上記の文が表示されれば成功。　
![16.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/5c02609e-8d72-ecb6-811f-7852a8cbc368.png)
11.`Hello [boot]` を右クリックして `[実行(R)]→[9 Spring Boot アプリケーション]` 選択する。
![17.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/0855bf86-3955-eba8-2ffa-8ab14e689a73.png)
12. [localhost:8080](http://localhost:8080/) にアクセスし、画像のように表示されれば成功。

## 4.GitHub
GitHubにソースコードを公開しています。

https://github.com/ryomeblog/qiita/tree/master/%E3%80%90%E5%AE%9F%E8%B7%B5%E2%80%BC%E3%80%91SpringBoot%E3%81%A7HelloWorld%E3%82%92%E8%A1%A8%E7%A4%BA%E3%81%99%E3%82%8B


## 5.関連
- [【覚えておくと便利!!!】Eclipseにおけるコンストラクタとgetter/setterの簡易作成](https://qiita.com/ryome/items/6c487ccc0c39847cd55f)
- [【覚えておくと便利!!!】Eclipseにおける継承クラスの簡易作成](https://qiita.com/ryome/items/4fcf62b4bc60fb6a4d07)
- [【覚えておくと便利!!!】MySQLの文字コード変更](https://qiita.com/ryome/items/b1f0f7055d2d614fb605)
- [【初心者でもできる‼】Javadocの書き方](https://qiita.com/ryome/items/4f2d5928c8aaf195f407)
- [【わかりやすく解説‼】Javaのオーバーロードの使い方](https://qiita.com/ryome/items/f1ca8cc7538f4c6d26c0)
- [【わかりやすく解説‼】Javaのカプセル化の使い方](https://qiita.com/ryome/items/fc44dfad297b35bf7559)
- [【わかりやすく解説‼】Javaの継承の使い方【オーバーライドの解説あり】](https://qiita.com/ryome/items/97b82c5519e39d3f21c1)
- [【わかりやすく解説‼】Javaにおける参照型の型変換](https://qiita.com/ryome/items/2453949b978bf166e204)
- [【わかりやすく解説‼】Javaのポリモーフィズムの使い方](https://qiita.com/ryome/items/a22457797988c548ec62)
- [【わかりやすく解説‼】ArrayListの使い方【Java】](https://qiita.com/ryome/items/cc01a7b6914e5b8b1579)
- [【実践‼】JFrameの導入（画面作成まで解説）](https://qiita.com/ryome/items/a45e0db4f7456e62d8d8)
- [【実践‼】Javaデータベース連携（Connector/J 8.0.20）](https://qiita.com/ryome/items/c78534200ae725ad571b)
- [【実践‼】SQLステートメントの実行](https://qiita.com/ryome/items/44ef641ea53595130f55)
- [Javaプログラミングの全て](https://qiita.com/ryome/items/d1ec1824431dbd182b91)
