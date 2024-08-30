---
title: React NativeプロジェクトにPrettierとESLintを設定する方法
tags:
  - 'React'
  - 'react-native'
  - 'JavaScript'
  - 'TypeScript'
  - '初心者'
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---
# React NativeプロジェクトにPrettierとESLintを設定する方法

## 1. React Nativeプロジェクトの作成方法

まず、React Native CLIを使用して新しいプロジェクトを作成します。以下のコマンドを実行してください。

```bash
npx react-native init MyNewProject
cd MyNewProject
```

## 2. VSCodeの設定ファイルの作成

次に、VSCode（Visual Studio Code）を開き、プロジェクトディレクトリを開きます。VSCodeはReact Native開発に非常に便利なエディタです。

## 3. VSCodeの拡張機能のPrettierとESLintインストール

VSCodeの拡張機能マーケットプレイスから以下の拡張機能をインストールします。

- **Prettier - Code formatter**
- **ESLint**

これらの拡張機能は、コードのフォーマットと静的解析を行うために必要です。

## 4. PrettierとESLintのインストール

プロジェクトディレクトリで以下のコマンドを実行して、PrettierとESLintをインストールします。

```bash
npm install --save-dev prettier eslint
```

さらに、ESLintのReact Native用プラグインもインストールします。

```bash
npm install --save-dev eslint-plugin-react eslint-plugin-react-native
```

## 5. PrettierとESLintの設定ファイル作成

プロジェクトのルートディレクトリに以下の設定ファイルを作成します。

### `.prettierrc`

```json
{
  "singleQuote": true,
  "trailingComma": "all"
}
```

### `.eslintrc.json`

```json
{
  "env": {
    "browser": true,
    "es2021": true
  },
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:react-native/all"
  ],
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": true
    },
    "ecmaVersion": 12,
    "sourceType": "module"
  },
  "plugins": [
    "react",
    "react-native"
  ],
  "rules": {
    "react/react-in-jsx-scope": "off"
  }
}
```

