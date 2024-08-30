---
title: ReactプロジェクトにPrettierとESLintを設定する方法
tags:
  - ''
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---
# new article body

### 1. 必要なパッケージのインストール

```bash
npm install --save-dev eslint prettier eslint-config-prettier eslint-plugin-prettier eslint-plugin-react
```


### 2. ESLintの設定ファイルを作成

プロジェクトのルートディレクトリに `.eslintrc.json` ファイルを作成し、以下の内容を追加します。

```json:.eslintrc.json
{
  "env": {
    "browser": true,
    "es2021": true
  },
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended",
    "prettier"
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
    "prettier"
  ],
  "rules": {
    "prettier/prettier": "error"
  }
}
```


### 3. Prettierの設定ファイルを作成

プロジェクトのルートディレクトリに `.prettierrc` ファイルを作成し、以下の内容を追加します。

```json:.prettierrc
{
  "singleQuote": true,
  "trailingComma": "all",
  "printWidth": 80
}
```


### 4. VSCodeの設定を更新

既にある `.vscode/settings.json` ファイルに以下の設定を追加します。

```json:.vscode/settings.json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },
  "eslint.validate": [
    "javascript",
    "javascriptreact"
  ],
  "prettier.requireConfig": true
}
```
