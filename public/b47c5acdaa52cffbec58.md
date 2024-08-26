---
title: 【初心者でもできる‼】Windows10にEclipseをインストールする方法（Java環境構築）
tags:
  - Java
  - Eclipse
private: false
updated_at: '2020-04-22T20:53:18+09:00'
id: b47c5acdaa52cffbec58
organization_url_name: null
slide: false
ignorePublish: false
---
## 1.Eclipseとは
- Eclipseとは、コンピュータプログラミングにおいて使用される `統合開発環境（IDE）` である。
- Eclipseは、 `オープンソースのソフトウェア` であり、 Eclipse Public Licenseの条件に基づいてリリースされている。
- Eclipseは、主に `Javaアプリケーションの開発` に使用される。

※ `4.Eclipseの日本語化` を先に読むことをお勧めします。

## 2.Eclipseのダウンロード
![01.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/15c5abe5-1aa2-ac65-42e3-c8baf83b74ee.png)
1. [Eclipse Downloads | The Eclipse Foundation](https://www.eclipse.org/downloads/) へアクセスする。
2. `Download 64 bit` ボタンをクリックする。
![02.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/865cf207-0234-cfab-2700-caa525c86dea.png)
3. `Download` ボタンをクリックする。　　　　
![03.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/18118f51-8aaf-bb5c-b665-cf9883286285.png)
4. `eclipse-inst-win64` がダウンロードされていれば成功。

## 3.Eclipseのインストール
　　
![03.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/18118f51-8aaf-bb5c-b665-cf9883286285.png)
1. `eclipse-inst-win64` を起動する。
![04.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/4e8eadeb-7c14-6f3f-e5cc-5d092f70a6a3.png)
2. `Eclipse IDE for Enterprise Java Developers` を選択する。
![05.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/d8fee659-ee09-0a23-209b-fc11b7d0d7fa.png)
3. `INSTALL` ボタンをクリックする。
![06.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/838c7d37-848d-7f91-7289-ac3d09521753.png)
4. `Accept Now` ボタンをクリックする。
![07.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/1e8b0229-793f-7d2a-a36e-77449c85fe7c.png)
5. `チェックボックス` をクリックし、 `Accept selected` ボタンをクリックする。
![08.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/9e361e1d-243d-f8f1-d15e-581cf47234e4.png)
6. `LAUNCH` ボタンをクリックする。
![09.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/292fbc69-6e2c-5d36-b622-a4b022d0145f.png)
7. `Browse` ボタンをクリックし、保存場所を選択して `Launch` ボタンをクリックする。
![10.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/b908d049-9033-3a9e-e082-5f1149692ee6.png)
8. `Browse` ボタンをクリックし、保存場所を選択して `Launch` ボタンをクリックする。
![11.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/c071a8ae-addd-9884-8644-a51091ad795a.png)
9. `Eclipse` が起動すれば成功。

## 4.Eclipseの日本語化
- Eclipseには、「 `Pleiades` 」というプラグインがある。
- Pleiadesでは、 Eclipse本体も含まれているため、 `上記の設定は不要` である。

![13.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/db537e80-b73f-47f6-6689-4e3b33ee0319.png)
1. [Eclipse 日本語化 | MergeDoc Project](https://mergedoc.osdn.jp/) へアクセスする。
2. `Eclipse 2020` ボタンをクリックする。
![14.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/b6fc378d-2496-e6b8-8e33-c06acf517763.png)
3. Java列の `Download` ボタンをクリックする。
![15.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/04a9a731-7f87-d0fd-aa1f-1127c6817d3f.png)
4.[https://ftp.jaist.ac.jp/pub/mergedoc/pleiades/2020/pleiades-2020-03-java-win-64bit-jre_20200322.zip](https://ftp.jaist.ac.jp/pub/mergedoc/pleiades/2020/pleiades-2020-03-java-win-64bit-jre_20200322.zip) をクリックする。
![16.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/1244d7cc-09c3-e30d-4020-952226e2fc9c.png)
5. ダウンロードしたzipファイルを `解凍` する。（7-Zip推進）
![17.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/7c5d1f35-2b88-5f93-076c-78fcaa6d8390.png)
6.  `解凍` したフォルダを開く。（ pleiades-2020-03-java-win-64bit-jre_20200322 / pleiades / eclipse / ）
![22.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/9373d372-ea7c-9398-9b67-977198608840.png)
7. `pleiades-2020-03-java-win-64bit-jre_20200322 / pleiades / eclipse /` 内の `eclipse.exe` をクリックする。
![23.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/13e7309c-1fe3-2d3a-fd2b-b08b38c88a06.png)
8. `参照` ボタンをクリックし、保存場所を選択して `起動` ボタンをクリックする。
![24.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/7f484987-3f31-526b-5015-38cc911f3e5d.png)
9. `Eclipse` が起動すれば成功。


## 5.Eclipseの簡単な使い方

![24.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/e0077f2c-c49a-77e5-f88d-055ca8e71b9e.png)
1. `Eclipse` を起動する。
![25.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/134d8560-7c8e-9bc7-0c51-97913fce85b6.png)
2. `[ファイル(F)]→[新規(N)]→[パッケージ]` を選択する。
![26.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/013ee30f-fa44-f2b6-399e-ffb2f1255830.png)
3. 名前に `Hello` と入力し、 `完了` ボタンをクリックする。
![27.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/23bb801b-77c0-b04c-facc-3414f75b3584.png)
4. パッケージを右クリックし、 `[新規(N)]→[クラス]` を選択する。
![28.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/8895b5a4-a0b2-2ce1-973b-9693a7c5685c.png)
5. パッケージと名前に `Hello` と入力し、 `public static void main(String[] args)` にチェックをつけ、 `完了` ボタンをクリックする。
![29.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/5866219f-692e-f098-1146-41f65df7eab2.png)

```java:Hello.java
package Hello;
public class Hello {
	public static void main(String[] args) {
		// TODO 自動生成されたメソッド・スタブ
		System.out.println("Hello World!!");
	}
}

```
6. 上記のソースコードを入力し、 `実行` ボタンをクリックする。

![30.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/fff73f32-f256-80a1-19b8-9a71ae266a95.png)
7. `[実行]→[Javaアプリケーション]` を選択する。

![31.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/ba31f1ab-a1c1-9e4e-ab6c-8af4a9a97af8.png)
8. コンソールに `Hello World!!` と表示されれば成功。

## 6.関連
- [【最新】UbuntuでJavaの環境を構築する方法](https://qiita.com/ryome/items/37c53e9638a9c6ea146a)
- [【初心者でもできる‼】Windows10にJava環境を作る方法（JDK14.0.1）](https://qiita.com/ryome/items/30135570954e36196821)

