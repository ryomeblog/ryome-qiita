---
title: React NativeプロジェクトにPrettierとESLintを設定するメモ
tags:
  - JavaScript
  - 初心者
  - TypeScript
  - React
  - react-native
private: false
updated_at: '2025-02-01T11:07:19+09:00'
id: 8a048b07636f5ebafe20
organization_url_name: null
slide: false
ignorePublish: false
---
# React NativeプロジェクトにPrettierとESLintを設定するメモ

## 前提条件

- Node.jsとnpmがインストールされていること
- React Nativeプロジェクトが作成済みであること
- Visual Studio Code（VS Code）がインストールされていること

## 1. プロジェクトの作成

まだプロジェクトを作成していない場合は、以下の手順で作成します：

1. **Expo CLIのインストール**:
   ```bash
   npm install -g expo-cli
   ```

2. **新しいプロジェクトの作成**:
   ```bash
   expo init my-react-native-app
   cd my-react-native-app
   ```

## 2. VS Code拡張機能のインストール

1. VS Codeを開き、拡張機能（Extensions）タブを開きます（`Ctrl+Shift+X` または `⌘+Shift+X`）
2. 以下の拡張機能をインストールします：
   - ESLint (`dbaeumer.vscode-eslint`)
   - Prettier - Code formatter (`esbenp.prettier-vscode`)

これらの拡張機能により、VS Code上でESLintとPrettierの機能が利用可能になります。

## 3. ESLintとPrettierのインストール

必要なパッケージをインストールします：

```bash
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-plugin-react eslint-plugin-react-native prettier eslint-config-prettier eslint-plugin-prettier
```

各パッケージの詳細な説明：
- `eslint`: JavaScriptのコード品質チェックツール
  - 構文エラーの検出
  - コーディング規約の遵守チェック
  - アンチパターンの検出
- `@typescript-eslint/parser`: TypeScriptのパーサー
  - TypeScriptコードの構文解析
  - ESLintがTypeScriptを理解できるようにする
- `@typescript-eslint/eslint-plugin`: TypeScript用のESLintルール
  - TypeScript特有のベストプラクティス
  - 型チェックに関するルール
- `eslint-plugin-react`: React用のESLintルール
  - Reactのベストプラクティス
  - JSX構文のチェック
  - Hooksのルールチェック
- `eslint-plugin-react-native`: React Native用のESLintルール
  - React Native特有のルール
  - パフォーマンスに関する警告
- `prettier`: コードフォーマッター
  - 一貫性のあるコードスタイル
  - インデント、スペース、改行の自動調整
- `eslint-config-prettier`: ESLintとPrettierの競合を防ぐ設定
  - コードスタイルに関するESLintルールの無効化
  - Prettierとの衝突回避
- `eslint-plugin-prettier`: PrettierをESLintのルールとして実行
  - PrettierのフォーマットをESLintエラーとして報告
  - ESLintの自動修正機能でPrettierを実行

## 4. ESLintの詳細設定

プロジェクトのルートディレクトリに`.eslintrc.js`ファイルを作成します：

```javascript
module.exports = {
  root: true, // プロジェクトのルートであることを指定
  extends: [
    'eslint:recommended', // ESLintの推奨ルール
    'plugin:@typescript-eslint/recommended', // TypeScript推奨ルール
    'plugin:react/recommended', // React推奨ルール
    'plugin:react-native/all', // React Native全ルール
    'plugin:prettier/recommended', // Prettier連携
  ],
  parser: '@typescript-eslint/parser', // TypeScriptパーサーの指定
  plugins: [
    '@typescript-eslint', // TypeScriptプラグイン
    'react', // Reactプラグイン
    'react-native', // React Nativeプラグイン
    'prettier', // Prettierプラグイン
  ],
  parserOptions: {
    ecmaFeatures: {
      jsx: true, // JSXのサポート
    },
    ecmaVersion: 2021, // ECMAScriptバージョン
    sourceType: 'module', // ESモジュールを使用
  },
  env: {
    'react-native/react-native': true, // React Native環境
  },
  rules: {
    // TypeScript関連
    '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }], // 未使用変数の警告（_で始まる引数は無視）
    '@typescript-eslint/explicit-function-return-type': 'off', // 関数の戻り値の型指定を必須にしない
    '@typescript-eslint/no-explicit-any': 'warn', // any型の使用を警告

    // React関連
    'react/prop-types': 'off', // TypeScriptを使用する場合は不要
    'react/react-in-jsx-scope': 'off', // React 17以降は不要
    'react/display-name': 'off', // displayName不要
    'react/jsx-curly-brace-presence': ['error', { props: 'never', children: 'never' }], // JSX中の不要な波括弧を禁止

    // React Native関連
    'react-native/no-inline-styles': 'warn', // インラインスタイルを警告
    'react-native/no-raw-text': ['error', { skip: ['Text'] }], // Text以外のコンポーネントでの生テキスト使用を禁止
    'react-native/no-unused-styles': 'error', // 未使用のスタイルを禁止
    'react-native/split-platform-components': 'warn', // プラットフォーム固有コンポーネントの分割を推奨

    // Prettier関連
    'prettier/prettier': ['error', {}, { usePrettierrc: true }], // Prettierのルールを適用

    // その他
    'no-console': 'warn', // console.*の使用を警告
    'no-debugger': 'error', // debuggerの使用を禁止
  },
  settings: {
    react: {
      version: 'detect', // Reactバージョンの自動検出
    },
  },
};
```

## 5. Prettierの詳細設定

プロジェクトのルートディレクトリに`.prettierrc.js`ファイルを作成します：

```javascript
module.exports = {
  // 基本設定
  printWidth: 100, // 1行の最大文字数
  tabWidth: 2, // インデントのスペース数
  useTabs: false, // スペースの代わりにタブを使用しない
  semi: true, // 文末にセミコロンを付ける
  singleQuote: true, // シングルクォートを使用
  quoteProps: 'as-needed', // オブジェクトのプロパティ名のクォートは必要な場合のみ
  jsxSingleQuote: false, // JSXではダブルクォートを使用
  
  // 末尾のカンマ設定
  trailingComma: 'all', // 可能な限り末尾にカンマを付ける
  
  // スペース設定
  bracketSpacing: true, // オブジェクトリテラルの括弧の間にスペースを入れる
  arrowParens: 'avoid', // アロー関数の引数が1つの場合は括弧を省略
  
  // JSX設定
  jsxBracketSameLine: false, // JSXの閉じ括弧を新しい行に配置
  
  // その他
  endOfLine: 'lf', // 改行コードをLFに統一
  embeddedLanguageFormatting: 'auto', // 埋め込みコードのフォーマットを自動で処理
};
```

## 6. VS Codeの自動フォーマット設定

1. プロジェクトのルートに`.vscode`ディレクトリを作成：
   ```bash
   mkdir .vscode
   ```

2. `.vscode/settings.json`ファイルを作成し、以下の設定を追加：

```json
{
  // フォーマット設定
  "editor.formatOnSave": true, // 保存時に自動フォーマット
  "editor.formatOnPaste": true, // ペースト時に自動フォーマット
  "editor.formatOnType": true, // タイプ時に自動フォーマット
  "editor.defaultFormatter": "esbenp.prettier-vscode", // デフォルトのフォーマッターをPrettierに設定
  
  // ESLint設定
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true, // 保存時にESLintの自動修正を実行
    "source.organizeImports": true // import文を自動整理
  },
  
  // ファイルタイプごとの設定
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescriptreact]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  
  // ESLint検証対象
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact"
  ]
}
```

## 7. スクリプトの追加

`package.json`のscriptsセクションに以下を追加します：

```json
{
  "scripts": {
    ...
    "lint": "eslint . --ext .js,.jsx,.ts,.tsx", // コード検証
    "lint:fix": "eslint . --ext .js,.jsx,.ts,.tsx --fix", // コード自動修正
    "format": "prettier --write \"**/*.{js,jsx,ts,tsx,json,md}\"", // 全ファイルのフォーマット
    "format:check": "prettier --check \"**/*.{js,jsx,ts,tsx,json,md}\"" // フォーマットチェックのみ
  }
}
```

## 8. 動作確認

1. 適当なコードを追加して整形されることを確認します：

```typescript
// App.tsx（フォーマット前）
import React from 'react';
import {View,Text,StyleSheet} from 'react-native'

const App = () => {
return(
  <View style={styles.container}>
      <Text style={{color: 'blue',fontSize:20}}>Hello, React Native!</Text>
  </View>
)
}

const styles = StyleSheet.create({
container: {
  flex: 1,
    justifyContent: 'center',
  alignItems: 'center',
},
})

export default App
```

2. ファイルを保存すると、自動的に以下のようにフォーマットされます：

```typescript
// App.tsx（フォーマット後）
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

const App = () => {
  return (
    <View style={styles.container}>
      <Text style={styles.text}>Hello, React Native!</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  text: {
    color: 'blue',
    fontSize: 20,
  },
});

export default App;
```

ESLintは以下のような問題を検出します：
- インラインスタイルの使用（警告）
- 未使用の変数
- TypeScriptの型の問題
- Reactのベストプラクティス違反

## まとめ

ESLintとPrettierを設定することで、以下のメリットが得られます：

- 保存時に自動的にコードが整形される
- コードの一貫性が保たれる
- バグの早期発見
- コーディング規約の自動適用
- チーム開発での品質管理が容易に
- TypeScriptの型チェックの強化

これらのツールを使用することで、より高品質なReact Nativeアプリケーションの開発が可能になります。

## トラブルシューティング

1. フォーマットが動作しない場合：
   - VS Code拡張機能が正しくインストールされているか確認
   - ワークスペース設定が正しく行われているか確認
   - VS Codeの再起動を試す

2. ESLintエラーが表示されない場合：
   - ESLint拡張機能が有効になっているか確認
   - `.eslintrc.js`の設定を確認
   - `node_modules`フォルダを削除して`npm install`を再実行

