---
title: WindowsでCUDA Toolkit・cuDNNを導入してollamaでdeepseekを動かす方法
tags:
  - Windows
  - CUDA
  - NVIDIA
  - AI
  - 生成AI
private: false
updated_at: '2025-02-01T11:08:11+09:00'
id: 9b838fa726ec55e47b9c
organization_url_name: null
slide: false
ignorePublish: false
---

# WindowsでCUDA Toolkit・cuDNNを導入してollamaでdeepseekを動かす方法

この記事では、Windows環境でCUDA ToolkitとcuDNNを導入し、ollamaを使用してdeepseekモデルを動かすまでの手順を説明します。

## 前提条件

- Windows 10/11
- NVIDIA GPU搭載のPC
- 管理者権限を持つアカウント

## 1. NVIDIA ドライバーの確認

まず、最新のNVIDIAドライバーがインストールされていることを確認します。

1. Windowsキー + Xを押し、「デバイスマネージャー」を選択
2. 「ディスプレイアダプター」を展開し、NVIDIAグラフィックスカードが正しく認識されていることを確認
3. 必要に応じて[NVIDIAドライバーダウンロードページ](https://www.nvidia.co.jp/Download/index.aspx?lang=jp)から最新ドライバーをインストール

## 2. CUDA Toolkitのインストール

1. [CUDA Toolkitダウンロードページ](https://developer.nvidia.com/cuda-downloads?target_os=Windows&target_arch=x86_64&target_version=11&target_type=exe_local)にアクセス
2. 以下の選択肢を選んでダウンロード：
   - Operating System: Windows
   - Architecture: x86_64
   - Version: Windows 11（またはお使いのWindowsバージョン）
   - Installer Type: exe(local)
![001.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/b96f1af6-d1a9-f2be-fe99-37cd3961bf6f.png)
![002.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/6036db70-8073-3b4b-dc30-a175a4e56a96.png)
3. ダウンロードしたインストーラーを実行
![003.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/115bea10-5a47-b526-9f80-73b394687b86.png)
![004.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/8ca4d708-7840-0eeb-6d76-a08be9363644.png)
4. インストールオプションで「Express（推奨）」を選択
![005.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/aaa1399a-6abd-2bb3-6efd-65af4241b4b4.png)
![006.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/eb378c63-40cc-5b61-e12d-d94a8c8649e8.png)
5. インストールが完了するまで待機
![007.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/fd98d675-2dc9-015c-60d3-cdef42764869.png)
![008.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/5ade0ec5-44f5-43ae-15cb-c5d4ff524c46.png)

インストール完了後、環境変数が自動的に設定されます。

## 3. cuDNNのインストール

1. [cuDNNダウンロードページ](https://developer.nvidia.com/cudnn-downloads?target_os=Windows&target_arch=x86_64&target_version=10&target_type=exe_local)にアクセス
2. インストールしたCUDA Toolkitのバージョンに対応するcuDNNをダウンロード
![009.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/a7a6776f-092a-b1e4-e621-5de8f18d2718.png)
3. ダウンロードしたインストーラーを実行
![010.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/1c006178-e821-dc1e-ba04-ea32a6a532ef.png)
4. ライセンス契約に同意し、インストールを完了
![011.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/4c41feaf-5b1b-e1a1-b1fa-19408f1bf451.png)
![012.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/8373661e-fd1f-cc24-40e8-d7b76cd4679a.png)
![013.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/7d4ce41d-610a-781c-782b-1bc66cb480ed.png)

## 4. ollamaのインストール

1. [ollama公式サイト](https://ollama.ai/)からWindowsインストーラーをダウンロード
![014.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/902e5d54-225c-b931-1d1e-37a96d9a9e11.png)
2. ダウンロードしたインストーラーを実行
![015.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/1ade6a6c-5000-2206-fdad-e4a01e78fb2d.png)
3. インストールウィザードの指示に従ってインストール
![016.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/d9932c4e-d13f-c676-31db-5f8f64406b73.png)
4. インストール完了後、Windows PowerShellまたはコマンドプロンプトを開く

## 5. deepseekモデルのダウンロードと実行

1. PowerShellまたはコマンドプロンプトで以下のコマンドを実行：

```powershell
ollama run deepseek-r1:14b-qwen-distill-q4_K_M
```

2. モデルのダウンロードが開始されます（約9GB）：

```
pulling manifest
pulling 6e9f90f02bb3... 100% ▕████████████████████████████████████████▏ 9.0 GB
pulling 369ca498f347... 100% ▕████████████████████████████████████████▏  387 B
pulling 6e4c38e1172f... 100% ▕████████████████████████████████████████▏ 1.1 KB
pulling f4d24e9138dd... 100% ▕████████████████████████████████████████▏  148 B
pulling 3c24b0c80794... 100% ▕████████████████████████████████████████▏  488 B
verifying sha256 digest
writing manifest
success
```

3. ダウンロードが完了すると、対話が可能になります

## 動作確認

以下のような質問を入力して、モデルの動作を確認できます：

- プロンプト
```
c10k問題の解決法を教えて
```

- 回答
```
>>> c10k問題の解決法を教えて
<think>

</think>

"C10K" は、Web インターフェイスやネットワーク アプリケーションでありがちな「同期処理」に関する問題です。具体的には、1 腰のポート番号しか開けられないため、大量の接続要求（例: 10,000 件）に対応できない状況を指します。     
  

### 問題の背景
- **同期処理**：アプリケーションが I/O (入出力) 操作中にブロックされてしまう状態。例えば、ソケット通信やファイル アクセス時。
- **ポート番号の制限**：OS は有限なポート番号（0-65535）しか開けません。大量の接続要求がある場合、ポート番号が不足し、処理できない状況が発生します。

### 解決法
以下に、C10K 問題を解決する一般的な方法を説明します。

---

### 1. **非同期プログラミング（Asynchronous Programming）**
- **メリット**：I/O 操作中にアプリケーションがブロックしないようにし、大量の接続に対応できる。
- **具体例**：
  - ** epoll（Linux）**: 高速なイベント通知机制を用いて、非同期処理を実現。
  - ** select() / poll()**: 複数のソケットの状態を監視し、I/O 完了時に処理を行う。
  - **reactor 橈式（Reactor Pattern）**: ソケットイベント駆動型の非同期プログラミング モデル。

---

### 2. **_threads (マルチスレッド)**
- **メリット**：それぞれの接続を独立したスレッドで処理し、ポート番号の制限を回避。
- **具体例**：
  - ** pthread（Linux）**: 多線程プログラミング。
  - **std::thread（C++）**: C++11 以降で標準化されたマルチスレッドサポート。

---

### 3. **プロトコル切り替え**
- **メリット**：ポート番号の制限を回避する代わりに、違う通信方式を使う。
- **具体例**：
  - ** UDP**: ポート番号の消費が少ない（1つのソケットで複数の送信先に対応可能）。
  - ** WebSocket**: 高速な双方向通信を実現し、ポート番号の制限を最小限にする。

---

### 4. **リングバッファやメッセージクエュー**
- **メリット**：大量の接続要求を効率的に処理するための機構。
- **具体例**：
  - ** epoll + ring buffer**: 高速なイベント駆動とリングバッファでデータ受け渡しを行う。
  - **ZeroMQ**: 分散メッセージング クラステクノロジー。

---

### 5. **OS レベルの最適化**
- **メリット**：OS の設定を調整して、ポート番号の利用率を高める。
- **具体例**：
  - ** tcp_tw_reuse**: TIME_WAIT ステート中のソケットを再利用可能にする。
  - ** SOMAXCONN**: listen() 関数の待ち受けキューの長さを増やす。

---

### 6. **マイクロサービス化**
- **メリット**：アプリケーションを分散させ、ポート番号の負担を軽減。
- **具体例**：
  - ** Docker + Kubernetes**: 容器技術でアプリケーションをスケールアウト。
  - **API Gateway**: 外部から届くリクエストを分散処理するゲートウェイを導入。

---

### 7. **ポート番号の共有**
- **メリット**：同一ポート番号を使用しながら、接続を管理する。
- **具体例**：
  - **Nginx**: 負载均衡機能で大量の接続を分散。
  - **HAProxy**: 高可用性と負载均衡を実現するプロキシサーバ。

---

### 8. **メッセージ キューング システム**
- **メリット**：接続要求をバッチ処理し、ポート番号の使用を最小限にする。
- **具体例**：
  - ** RabbitMQ**: メッセージ キューング クラステクノロジー。
  - **Kafka**: 高速なメッセージ トラフfic 处理。

---

### 9. **モバイル/分散型 アrchitecture**
- **メリット**：アプリケーションを分散させ、ポート番号の負担を軽減。
- **具体例**：
  - **Lambda 関数（AWS）**: 雲計算サービスで処理をスケールアウト。
  - **grpc**: 高速な RPC プロトコルを用いて分散システムを構築。

---

### 10. **ポート番号の再利用**
- **メリット**：同じポート番号を複数回使用し、接続数を増やす。
- **具体例**：
  - ** TCP Fast Open**: 新しい接続で既存のポート番号を_REUSE。
  - **Nagle 算法**: 小さなパケットを結合し、通信効率を向上。
```

## 参考リンク

- [NVIDIA CUDA Toolkit Documentation](https://docs.nvidia.com/cuda/)
- [NVIDIA cuDNN Documentation](https://docs.nvidia.com/deeplearning/cudnn/)
- [ollama Official Documentation](https://ollama.ai/docs)
