---
title: mp4ファイルをm3u8ファイルに変換する
tags:
  - JavaScript
  - Node.js
  - ffmpeg
  - mp4
  - 個人開発
private: false
updated_at: '2023-03-29T07:10:25+09:00'
id: 4dbbd7da80cc95a1eea8
organization_url_name: null
slide: false
ignorePublish: false
---
## 事前準備
以下の事前準備が必要です。

- [【Windows/Mac/Linux（Ubuntu）】Node.jsインストール方法](https://qiita.com/ryome/items/eec08b28aff294e8c3d6)

## フォルダ構成

```
.
├─index.js
├─【任意】.mp4
├─package-lock.json
├─package.json
└─node_modules
    └─...省略
```

## コード
```js:index.js
const ffmpeg = require('fluent-ffmpeg');
const ffmpegStatic = require('ffmpeg-static');

// MP4ファイルのパス
const inputPath = process.argv[2];

// 出力M3U8ファイルのパス
const outputPath = 'output.m3u8';

// ffmpegのパスを強制的に設定
ffmpeg.setFfmpegPath(ffmpegStatic);

// MP4からM3U8への変換処理
ffmpeg(inputPath)
  .outputOptions([
    '-codec:v libx264',
    '-codec:a aac',
    '-hls_time 10',
    '-hls_list_size 0',
    '-f hls'
  ])
  .output(outputPath)
  .on('end', () => {
    console.log('変換が完了しました。');
  })
  .on('error', (err) => {
    console.error('変換エラー: ', err.message);
  })
  .run();
```

```json:package.json
{
  "name": "m3u8",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "ffmpeg-static": "^5.1.0",
    "fluent-ffmpeg": "^2.1.2"
  }
}
```

## 使い方

1. `MP4` ファイルを配置する
2. `npm install` コマンドを実行する
3. `node index.js 【任意】.mp4` コマンドを実行する
4. 以下のように `output.m3u8` と `output0.ts ...etc` が作成されていれば成功

```
.
├─index.js
├─input.mp4
├─output.m3u8
├─output0.ts
├─output1.ts
├─output2.ts
├─output3.ts
├─output4.ts
├─output5.ts
├─package-lock.json
├─package.json
└─node_modules
    └─...省略
```

## GitHub

GitHubにソースコードを公開しています。

https://github.com/ryomeblog/Mp4ToM3U8Converter
