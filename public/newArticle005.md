---
title: とりあえずBedrockを個人利用としてLambdaで実行するまで
tags:
  - JavaScript
  - AWS
  - 初心者
  - bedrock
  - 生成AI
private: false
updated_at: '2024-08-27T00:05:12+09:00'
id: 2585c50e8c6a8831022c
organization_url_name: null
slide: false
ignorePublish: false
---
## 事前準備
Amazon Bedrockを利用するためには、以下の事前準備が必要です。

- AWSアカウントの作成されていること
- マネジメントコンソールまたはAWS CLIが操作可能であること

https://qiita.com/ryome/items/ff2804be600cd5dcd61a

- Node.jsがインストールされていること

https://qiita.com/ryome/items/f2a0bc2de007da30bbfd

## Amazon Bedrockの有効化
Amazon Bedrockを有効にするための手順を以下に示します。

### 1. Bedrockサービスの有効化
1. **AWS Management Console** にログインします。
2. 検索バー に **Amazon Bedrock** と入力し、サービスを選択します。
![001.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/360d417e-2a30-1192-532f-7003ecdf3b2a.jpeg)
3. **使用開始** をクリックします。
![002.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/b974c34f-cba1-bf36-8017-6e66a5619101.jpeg)
4. 左側のメニューから **ベースモデル** を選択し、 **Titan Embeddings G1 - Text** を選択します。
![003.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/a2190ab3-1c23-bf92-82e1-70d02d935819.jpeg)
5. **モデルアクセスをリクエスト** をクリックします。
![004.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/afbc84c9-28d6-296f-5a37-6ba227988d0b.jpeg)
6. **ベースモデル** の **リクエスト可能** から **モデルアクセスをリクエスト** をクリックします。
![005.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/1f7f2cf2-c9a3-90bc-fb9e-8207c1c70686.jpeg)
![006.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/351a6eed-2b6c-c535-1d72-dd2b04ce599c.jpeg)
7. **使用したいモデル** を選択して **Next** をクリックします。
![007.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/1aa398d3-3f32-35ca-d83e-85f3d82e8592.jpeg)
8. **Submit** をクリックします。
![008.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/78cdccc8-c3d6-9ea2-de3a-2cf0cf09055a.jpeg)
9. **アクセスが付与されました** と表示されれば成功です。
![009.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/25060345-f846-3bba-ea37-dde49f7d52f5.jpeg)
10. 最後に **ベースモデル** から使用したいモデルの **Model ID** を控えておきます。
![010.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/e000687f-8b40-35ad-5995-6a1c8e7041cd.jpeg)

## AWS Lambdaの設定

### 1. Lambda関数の作成
1. **AWS Management Console**で**Lambda**サービスに移動します。
2. **Create function（または「関数の作成」）** をクリックし、以下の設定で新しい関数を作成します：
   - **関数名**: `bedrock-titan-function`
   - **ランタイム**: Node.js 20.x
   - **アーキテクチャ**: x86_64

### 2. IAMロールの設定
Lambda関数がBedrockにアクセスできるように、IAMロールに以下のポリシーを追加します：
- `【リージョン】` と `【アカウント ID】` は置き換えてください。
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": "logs:CreateLogGroup",
			"Resource": "arn:aws:logs:【リージョン】:【アカウント ID】:*"
		},
		{
			"Effect": "Allow",
			"Action": [
				"logs:CreateLogStream",
				"logs:PutLogEvents"
			],
			"Resource": [
				"arn:aws:logs:【リージョン】:【アカウント ID】:log-group:/aws/lambda/bedrock-titan-function:*"
			]
		},
		{
			"Effect": "Allow",
			"Action": "bedrock:*",
			"Resource": "*"
		}
	]
}
```
![011.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/5d9c07d6-d141-cbe9-b51d-740ede644174.jpeg)
![012.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/2cd1c8d5-c726-8b12-12f8-370d50b77a45.jpeg)
![013.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/6e34078d-8845-591a-25d5-5e21859723b1.jpeg)

:::note info
開発時の【リージョン】は、東京リージョン（ `ap-northeast-1` ）を使用しています。
:::

### 3. 必要なモジュールのインストール
ローカル環境で `@aws-sdk/client-bedrock-runtime` をインストールし、デプロイパッケージを作成します。
```bash
mkdir my-lambda-function
cd my-lambda-function
npm init -y
npm install @aws-sdk/client-bedrock-runtime
```

### 4. Lambda関数のコード
`index.js` ファイルを作成します：
- `【リージョン】` は置き換えてください。
```javascript:index.js
import {
  BedrockRuntimeClient,
  InvokeModelCommand,
} from "@aws-sdk/client-bedrock-runtime";

const client = new BedrockRuntimeClient({ region: "【リージョン】" });

export async function handler(event) {
  const modelId = "amazon.titan-text-express-v1";
  console.log("Received event:", event);

  const userPrompt = event.prompt;
  console.log(userPrompt);

  if (!userPrompt) {
    console.error("No user prompt provided");
    return {
      statusCode: 400,
      body: JSON.stringify({ message: "No user prompt provided" }),
    };
  }

  const payload = {
    inputText: userPrompt,
    textGenerationConfig: {
      maxTokenCount: 4096,
      temperature: 0,
    },
  };

  try {
    const command = new InvokeModelCommand({
      contentType: "application/json",
      body: JSON.stringify(payload),
      modelId,
    });
    const apiResponse = await client.send(command);
    const jsonString = Buffer.from(apiResponse.body).toString("utf8");
    const parsedData = JSON.parse(jsonString);

    console.log("API Response:", parsedData);

    if (parsedData.results && parsedData.results[0].outputText) {
      const text = parsedData.results[0].outputText;
      console.log("Generated text:", text);
      return {
        statusCode: 200,
        body: JSON.stringify({ text }),
      };
    } else {
      console.error("Invalid response structure");
      return {
        statusCode: 500,
        body: JSON.stringify({ message: "Invalid response structure" }),
      };
    }
  } catch (error) {
    console.error("Error calling model:", error);
    return {
      statusCode: 500,
      body: JSON.stringify({ message: "An error occurred" }),
    };
  }
}
```
:::note info
開発時の【リージョン】は、東京リージョン（ `ap-northeast-1` ）を使用しています。
:::

### 5. デプロイパッケージの作成
`index.js` と `node_modules` フォルダを含むZIPファイルを作成します。
```bash
zip -r function.zip .
```

### 6. Lambda関数のアップロード
AWS Management Consoleに戻り、作成したLambda関数に `function.zip` ファイルをアップロードします。

![014.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/9ef7ab38-dc9c-f297-9007-afa643cff51e.jpeg)
![015.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/471c416a-cf45-288e-1f1f-54b50e9acabb.jpeg)

## Lambdaテスト
1. Lambda関数をテストするために、以下のようなテストイベントを使用します：
  - イベント名：`bedrock-test-001`
```json:イベントJSON
{
  "prompt": "AWS Lambdaとは何ですか？"
}
```
![016.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/cf2d7a67-e2ec-0a06-6724-36b24e0bb0d0.jpeg)
2. テストを実行して以下のようなレスポンスとなれば成功。
```json
{
  "statusCode": 200,
  "body": "{\"text\":\"\\nAWS Lambdaは、サーバーを管理したり、コードをデプロイしたり、スケーリングしたりする必要がない、サーバーレスコンピューティングサービスです。 Lambdaは、イベントやトリガーに応じてコードを実行する、イベント駆動型コンピューティングの方法です。 これにより、開発者はコードを記述するだけで、AWSがサーバーを管理し、スケーリングします。\"}"
}
```

## タイムアウトエラー解決方法

### エラー内容
```
{
  "errorType": "Sandbox.Timedout",
  "errorMessage": "RequestId: hogehoge-aaaa-bbbb-cccc-fugafuga Error: Task timed out after 3.00 seconds"
}
```

### 原因
Lambdaのタイムアウト設定が `3秒` であるため

### 解決方法
Lambdaの `設定 > 一般設定 > 編集` からタイムアウトの設定を `1分0秒` に変更すると解決する。

![017.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/449867/a7b0a46f-4a5a-1a23-5210-37a32cb740b6.jpeg)

## Lambdaの処理解説

<details>
  <summary>ソースコード（クリックして展開）</summary>

```javascript:index.js
import {
  BedrockRuntimeClient,
  InvokeModelCommand,
} from "@aws-sdk/client-bedrock-runtime";

const client = new BedrockRuntimeClient({ region: "【リージョン】" });

export async function handler(event) {
  const modelId = "amazon.titan-text-express-v1";
  console.log("Received event:", event);

  const userPrompt = event.prompt;
  console.log(userPrompt);

  if (!userPrompt) {
    console.error("No user prompt provided");
    return {
      statusCode: 400,
      body: JSON.stringify({ message: "No user prompt provided" }),
    };
  }

  const payload = {
    inputText: userPrompt,
    textGenerationConfig: {
      maxTokenCount: 4096,
      temperature: 0,
    },
  };

  try {
    const command = new InvokeModelCommand({
      contentType: "application/json",
      body: JSON.stringify(payload),
      modelId,
    });
    const apiResponse = await client.send(command);
    const jsonString = Buffer.from(apiResponse.body).toString("utf8");
    const parsedData = JSON.parse(jsonString);

    console.log("API Response:", parsedData);

    if (parsedData.results && parsedData.results[0].outputText) {
      const text = parsedData.results[0].outputText;
      console.log("Generated text:", text);
      return {
        statusCode: 200,
        body: JSON.stringify({ text }),
      };
    } else {
      console.error("Invalid response structure");
      return {
        statusCode: 500,
        body: JSON.stringify({ message: "Invalid response structure" }),
      };
    }
  } catch (error) {
    console.error("Error calling model:", error);
    return {
      statusCode: 500,
      body: JSON.stringify({ message: "An error occurred" }),
    };
  }
}
```

</details>

### 1. 必要なモジュールのインポート
```javascript
import {
  BedrockRuntimeClient,
  InvokeModelCommand,
} from "@aws-sdk/client-bedrock-runtime";
```
この部分では、AWS SDKのBedrock RuntimeクライアントとInvokeModelCommandをインポートしています。
これにより、Amazon Bedrockのモデルを呼び出すためのクライアントとコマンドを使用できるようになります。

### 2. BedrockRuntimeClientの初期化
```javascript
const client = new BedrockRuntimeClient({ region: "【リージョン】" });
```
ここでは、BedrockRuntimeClientを初期化しています。
`region`には、使用するAWSリージョンを指定します。
:::note info
今回は、東京リージョン（ `ap-northeast-1` ）を使用することを想定しています。
:::

### 3. Lambdaハンドラー関数の定義
```javascript
export async function handler(event) {
  const modelId = "amazon.titan-text-express-v1";
  console.log("Received event:", event);
```
この部分では、Lambda関数のエントリーポイントである `handler` 関数を定義しています。
`event` オブジェクトには、Lambda関数が呼び出されたときの入力データが含まれます。
`modelId` には、使用するモデルのIDを指定しています。

:::note info
ここで指定する `modelId`  は [1. bedrockサービスの有効化](#1-bedrockサービスの有効化) で控えた `Model ID` です。
:::

### 4. ユーザー入力の取得と検証
```javascript
  const userPrompt = event.prompt;
  console.log(userPrompt);

  if (!userPrompt) {
    console.error("No user prompt provided");
    return {
      statusCode: 400,
      body: JSON.stringify({ message: "No user prompt provided" }),
    };
  }
```
ここでは、`event` オブジェクトからユーザーの入力（ `prompt` ）を取得し、ログに出力しています。
もし、 `prompt` が提供されていない場合は、エラーメッセージを返します。

### 5. リクエストペイロードの作成
```javascript
  const payload = {
    inputText: userPrompt,
    textGenerationConfig: {
      maxTokenCount: 4096,
      temperature: 0,
    },
  };
```
この部分では、モデルに送信するリクエストペイロードを作成しています。
`inputText` にはユーザーの入力を設定し、`textGenerationConfig` にはテキスト生成の設定を指定しています。
`maxTokenCount` は生成するトークンの最大数、`temperature` は生成の多様性を制御するパラメータです。

### 6. モデル呼び出しコマンドの作成と送信
```javascript
  try {
    const command = new InvokeModelCommand({
      contentType: "application/json",
      body: JSON.stringify(payload),
      modelId,
    });
    const apiResponse = await client.send(command);
    const jsonString = Buffer.from(apiResponse.body).toString("utf8");
    const parsedData = JSON.parse(jsonString);
```
ここでは、`InvokeModelCommand` を使用してモデル呼び出しコマンドを作成し、`client.send`メソッドでコマンドを送信しています。
APIのレスポンスを受け取り、レスポンスボディをJSON形式に変換しています。

:::note info
`InvokeModelCommand` の詳細は以下
https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/client/bedrock-runtime/command/InvokeModelCommand/
:::

### 7. レスポンスの処理
```javascript
    console.log("API Response:", parsedData);

    if (parsedData.results && parsedData.results[0].outputText) {
      const text = parsedData.results[0].outputText;
      console.log("Generated text:", text);
      return {
        statusCode: 200,
        body: JSON.stringify({ text }),
      };
    } else {
      console.error("Invalid response structure");
      return {
        statusCode: 500,
        body: JSON.stringify({ message: "Invalid response structure" }),
      };
    }
  } catch (error) {
    console.error("Error calling model:", error);
    return {
      statusCode: 500,
      body: JSON.stringify({ message: "An error occurred" }),
    };
  }
}
```
この部分では、APIレスポンスをログに出力し、レスポンスの構造を検証しています。
`results` 配列に `outputText` が含まれている場合は、そのテキストを返します。
そうでない場合は、エラーメッセージを返します。
エラーが発生した場合は、エラーメッセージをログに出力し、500ステータスコードを返します。

## GitHub

GitHubにコードを公開しています。
https://github.com/ryomeblog/lambda-bedrock-titan-test
