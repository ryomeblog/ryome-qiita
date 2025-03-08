# Playwrightで自動テストを始めよう: インストールからレポート活用まで

## Playwrightのインストール方法

### 必要な環境

- Node.js (推奨バージョン: 14以上)
- npm または yarn

### インストール手順

1. プロジェクトディレクトリを作成し初期化します:

   ```
   mkdir my-playwright-project
   cd my-playwright-project
   npm init -y
   ```

2. Playwrightをインストールします:

   ```
   npm install -D @playwright/test
   ```

3. Playwrightのブラウザバイナリなどの初期設定を行います:

   ```
   npx playwright install
   ```

これで、Playwrightのインストールは完了です。

---

## テストコマンドの詳細とユースケース

Playwrightは多くのコマンドを提供しており、それぞれのコマンドが異なるユースケースに対応しています。以下は主要なコマンドとそれぞれの詳細な説明です。

- **テストの実行:**

  ```
  npx playwright test
  ```

  - 説明: プロジェクト内にある全てのテストファイルを検出し、一括で実行します。
  - ユースケース: CIパイプラインで自動テストを実行する際や、開発中に変更されたテストをまとめて検証したい場合に有用です。

- **特定ファイルのテスト実行:**

  ```
  npx playwright test tests/example.spec.js
  ```

  - 説明: 特定のテストファイルのみを実行します。
  - ユースケース: テストファイルごとのデバッグや個別機能の検証に利用されます。

- **ヘッドレスモード vs ヘッドフルモード:**

  - ヘッドレスモード（デフォルト）:
    ```
    npx playwright test
    ```
    - 説明: ブラウザをバックグラウンドで実行し、UIを表示せずにテストを高速に実行します。
  
  - ヘッドフルモード:
    ```
    npx playwright test --headed
    ```
    - 説明: 実際のブラウザインターフェイスを表示しながらテストを実行。デバッグ時に画面上で動作確認ができます。
    - ユースケース: テスト中の視覚的な挙動やアニメーションなどを確認したい場合に使用します。

- **テスト結果のHTMLレポート表示:**

  ```
  npx playwright show-report
  ```

  - 説明: テスト実行後に生成されたHTML形式のレポートをブラウザで表示。テスト結果の詳細なログやスクリーンショットとともに確認可能です。
  - ユースケース: テスト結果の共有、レビューや失敗原因の解析に役立ちます。

- **ブラウザインスタンスの手動起動:**

  ```
  npx playwright open https://example.com
  ```

  - 説明: 指定したURLでブラウザを起動し、手動操作を行うためのセッションを開始します。
  - ユースケース: テスト前の環境準備や、特定の動作を確認する際に利用されます。

---

## Playwright設定ファイルの解説

Playwrightでは、`playwright.config.js`（または`playwright.config.ts`）という設定ファイルでテスト実行に関する各種設定を行います。以下は主要な項目とその意味です。

### 例: playwright.config.js

```javascript
// playwright.config.js
module.exports = {
  testDir: './tests',         // テストケースが格納されているディレクトリ
  timeout: 30000,             // 各テストの最大実行時間（ミリ秒）
  expect: {
    timeout: 5000           // expect()に与えられる最大待機時間
  },
  fullyParallel: true,       // テストを並列実行するかどうか
  retries: 1,                // 失敗したテストの再試行回数
  reporter: 'html',         // テスト結果のレポート形式。例: 'html', 'list', 'junit'
  use: {
    baseURL: 'https://example.com',   // テスト実行時の基本URL。テスト中に相対パスを利用する場合に有用
    headless: true,                   // デフォルトはヘッドレスモード
    viewport: { width: 1280, height: 720 }, // テストで使用するブラウザの画面サイズ
    actionTimeout: 10000,             // 個々のアクションの最大待機時間
    ignoreHTTPSErrors: true,          // HTTPSエラーを無視する設定
    video: 'on-first-retry',          // テスト失敗時のみ動画を記録するオプション
    trace: 'retain-on-failure'        // トレースの取得設定。失敗したテストに対してトレースを保存する
  },
  projects: [
    {
      name: 'Chromium',
      use: { browserName: 'chromium' }
    },
    {
      name: 'Firefox',
      use: { browserName: 'firefox' }
    },
    {
      name: 'WebKit',
      use: { browserName: 'webkit' }
    }
  ]
};
```

### 各項目の意味

- **testDir:** テストファイルが配置されているディレクトリを指定します。例えば、作成したテストケースを`./tests`フォルダにまとめる場合です。
- **timeout:** 各テストケースが指定した時間内に完了しなかった場合、テストを中断・失敗と判定します。
- **fullyParallel:** 複数のテストを同時に実行することで、テストスイート全体の実行時間短縮を図ります。
- **retries:** 一時的な環境問題等でテストが失敗した場合に、再試行する回数です。
- **reporter:** テスト結果の出力形式を設定します。HTMLレポートなど、必要なフォーマットに合わせて選択します。
- **use:** テスト実行時に共通で使用するオプションを設定します。ここでは、基本URL、ヘッドレスモード、ブラウザウィンドウのサイズ、各種タイムアウト、動画記録、トレース取得などの設定が行われています。
- **projects:** 複数のブラウザ環境で同一のテストを実行する場合の設定です。これにより、Chromium, Firefox, WebKitそれぞれでテストを行うことができます。

---

## トレース機能の詳細

Playwrightのトレース機能は、テスト実行中の詳細な情報（ネットワーク、スクリーンショット、DOMの状態など）を記録し、後からテスト失敗の原因を解析できる強力なツールです。

### トレースの取得と保存

- テスト実行中にトレースを取得するには、設定ファイルの`use.trace`オプションを利用します。例えば、`'retain-on-failure'`を指定することで、失敗したテストのみトレースが保存されます。

- 手動でトレースを有効にする場合、各テストにおいて以下のように記述します:

  ```javascript
  test('example test with tracing', async ({ page, context }) => {
    await context.tracing.start({ screenshots: true, snapshots: true });
    // テスト操作…
    await page.goto('https://example.com');
    // …
    await context.tracing.stop({ path: 'trace.zip' });
  });
  ```

- 取得したトレースは、以下のコマンドで表示できます:

  ```
  npx playwright show-trace trace.zip
  ```

### ユースケース

- **失敗したテストのデバッグ:** テストが失敗した場合、どのステップで異常が発生したのか、どのような状態だったのかを遡って確認することが可能です。
- **通信・パフォーマンス解析:** ネットワーク通信やDOMの変更など、テスト実行中の詳細情報を分析することで、アプリケーションの問題箇所を特定できます。

---

## 画面からテストを生成する方法

Playwrightは、ユーザーのブラウザ操作を記録し、自動的にテストコードを生成するツール「Codegen」を提供しています。

### Codegenの使い方

1. コマンドラインで以下のコマンドを実行します:

   ```
   npx playwright codegen https://example.com
   ```

2. 指定したURLのページが起動し、ブラウザで行った操作（クリック、入力など）がリアルタイムで記録され、対応するテストコードが生成されます。

3. 生成されたコードをコピーして、テストファイル（例: `tests/example.spec.js`）に貼り付け、必要に応じて編集します。

### ユースケース

- **初期テストの作成:** 手動操作をベースに基本的なテストスクリプトを素早く生成でき、そこからカスタマイズ・拡張が可能です。
- **複雑なユーザーフローの記録:** デバッグ時にユーザーが実際に操作するフローを正確に記録し、実際の動作を再現するテストとして利用できます。

