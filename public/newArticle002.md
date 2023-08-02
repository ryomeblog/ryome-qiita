---
title: Llama2をDockerで実行する
tags:
  - 'LLaMA'
  - 'AI'
  - 'ChatGPT'
  - 'Python'
  - 'LLM'
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
---
# はじめに
Llama2が発表されたことで話題となりましたが、なかなか簡単に解説してくれる記事がなかったため、本記事を作成しました。
誰かの参考になれば幸いです。

以下は、Llama2のおさらいです。

- Llama2は、MetaとMicrosoftが提携して商用利用と研究の両方を目的とした次世代の大規模言語モデルです。
- Llama2は、自然言語処理のさまざまなタスクに対応できるように設計されており、テキスト生成、要約、質問応答、機械翻訳などの能力を持っています。
- Llama2は、人間の言語を理解し、適切な返答を生成することができます。
- Llama2は、無料で公開され、商用利用も可能です。

Llama2のライセンスについて詳しくは以下をご参照ください。

https://ai.meta.com/llama/license/


# 1. 事前準備
https://qiita.com/ryome/items/240f36923f5cb989da27

https://qiita.com/ryome/items/4b6b934b1b2021acfa26

https://qiita.com/ryome/items/6e59dcbe4735d45d58a3

https://qiita.com/ryome/items/3d4de62b560aac31bede


# 2. 開発環境

| 環境               | バージョン |
| ------------------ | ---------- |
| Docker             | 20.10.17   |
| Ubuntu（WSL2）     | 20.04 LTS  |

# 3. Dockerfile

このDockerfileは、Llama2を実行するための環境を構築します。

```Dockerfile:Dockerfile
# Ubuntuの最新版をベースにする
FROM ubuntu:latest

# 必要なパッケージのインストール
RUN apt-get update && apt-get install -y \
    wget \
    software-properties-common \
    gcc-11 \
    g++-11

# Python3.10を追加
RUN add-apt-repository ppa:deadsnakes/ppa
RUN apt-get update
RUN apt-get install -y python3.10 python3-pip

# llama-cpp-pythonをインストール
RUN pip install llama-cpp-python

# ワークディレクトリを設定
WORKDIR /usr/src/app

# ファイルをダウンロード
RUN wget https://huggingface.co/TheBloke/Llama-2-7B-Chat-GGML/resolve/main/llama-2-7b-chat.ggmlv3.q8_0.bin

# Pythonファイルをコピー
COPY . .
```

- `FROM ubuntu:latest`

    これはベースイメージとして最新の Ubuntu を使用することを意味します。

- `RUN apt-get update && apt-get install -y \ wget \ software-properties-common \ gcc-11 \ g++-11`

    これは Docker イメージ内でパッケージを更新し、その後 `wget`（ファイルをダウンロードするためのツール）、`software-properties-common`（ソフトウェアのプロパティを管理するためのツール）、`gcc-11` および `g++-11`（C/C++ コンパイラ）をインストールする命令です。

- `RUN add-apt-repository ppa:deadsnakes/ppa`
  
    `deadsnakes/ppa` リポジトリをシステムのリポジトリリストに追加します。

- `RUN apt-get update`
  
    パッケージリストを更新します。

- `RUN apt-get install -y python3.10 python3-pip`

    Python 3.10 と pip（Python パッケージ管理ツール）をインストールします。

- `RUN pip install llama-cpp-python`

    `llama-cpp-python` パッケージをインストールします。このパッケージは Python から C++ の llama ライブラリを使用するために使用します。

- `WORKDIR /usr/src/app`

    この命令は `/usr/src/app` を作業ディレクトリ（次の命令が実行されるディレクトリ）として設定します。

- `RUN wget https://huggingface.co/TheBloke/Llama-2-7B-Chat-GGML/resolve/main/llama-2-7b-chat.ggmlv3.q8_0.bin`

    `wget` コマンドを使用して、指定した URL からファイルをダウンロードします。このファイルは  `llama-cpp-python` パッケージで使用します。

- `COPY . .`

    この命令は Dockerfile があるディレクトリ（ソース）のすべてのファイルとディレクトリを、作業ディレクトリ（先ほど設定した `/usr/src/app`）にコピーします。

# 4. Pythonファイル

この Python スクリプトは、AI モデル `Llama` を使ってテキストの生成を行います。

具体的な処理は以下の通りです。

```python:sample.py
from llama_cpp import Llama

initial_prompt = "View Hello World in html."

llm = Llama(model_path="./llama-2-7b-chat.ggmlv3.q8_0.bin")

result = llm(
    initial_prompt,
    max_tokens=1000,
    echo=True,
)

print(result['choices'][0]['text'])
```

- `from llama_cpp import Llama`

    `llama_cpp` パッケージから `Llama` クラスをインポートします。`Llama` クラスは、AI モデルの呼び出しを簡単に行えるように抽象化されたものです。

- `initial_prompt = "View Hello World in html."`

    初期プロンプトとして `"View Hello World in html."` を設定します。プロンプトはモデルに対する入力で、モデルはこれに基づいてテキストを生成します。

- `llm = Llama(model_path="./llama-2-7b-chat.ggmlv3.q8_0.bin")`

    `Llama` クラスのインスタンスを作成し、AI モデルのファイルパスを指定します。このスクリプトでは、カレントディレクトリにある `llama-2-7b-chat.ggmlv3.q8_0.bin` ファイルをモデルとして使用します。

- `result = llm(initial_prompt, max_tokens=1000, echo=True)`

    モデルに初期プロンプトを与え、最大で 1000 トークンのテキストを生成させます。`echo=True` の部分は、入力したプロンプトも出力に含めるように指定しています。

- `print(result['choices'][0]['text'])`

    モデルから得られた結果を出力します。`Llama` モデルは複数の結果を生成することが可能で、それらは `choices` キーの下に格納されます。ここでは最初の結果（`[0]`）のテキスト部分を出力しています。


# 5. Llama2を起動する

以下のコマンドを実行し、Llama2を起動します。

- コマンド
```shell:コマンド
docker build -t my_docker_image .
docker run -it --name my_docker_container my_docker_image
python3 sample.py
```

- 実行例
```shell:出力
$ docker build -t my_docker_image .
Sending build context to Docker daemon  3.584kB
Step 1/9 : FROM ubuntu:latest
...省略
$ docker run -it --name my_docker_container my_docker_image
root@8111597c4423:/usr/src/app# python3 sample.py
llama.cpp: loading model from ./llama-2-7b-chat.ggmlv3.q8_0.bin
llama_model_load_internal: format     = ggjt v3 (latest)
...省略

Hello World! This is my first HTML document.

<html>
 <head>
  <title>Hello World</title>
 </head>
 <body>
  <h1>Hello World!</h1>
 </body>
</html>

Here is the basic structure of an HTML file:

* The `<!DOCTYPE html>` declaration specifies the document type as HTML.
* The `<html>` element is the root element of the document, which contains all the other elements.
* The `<head>` element contains metadata about the document, such as the title and links to external stylesheets or scripts.
* The `<body>` element contains the content of the document that is visible in the browser window.
* The `<h1>` element is a heading element that displays the text "Hello World!" in a larger font size than other headings.
Note: This is just a basic example, and there are many more elements and attributes that can be used in an HTML document to create complex web pages.
```

# 補足

Llama2に出力されたHTMLは以下のようになっています。

![01.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/8cce6cd1-3af4-73bb-0d53-e1c503dee4c4.png)

# 参考文献

https://github.com/oobabooga/text-generation-webui/issues/1534

https://github.com/abetlen/llama-cpp-python

https://github.com/facebookresearch/llama

