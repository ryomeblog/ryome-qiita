---
title: 個人的Macbookを買ったら最初にやることまとめ
tags:
  - "Mac"
  - "初心者"
  - "AWS"
  - "Git"
  - "Chrome"
private: false
updated_at: "2024-03-23"
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

# 個人的 Macbook を買ったら最初にやることまとめ

## 設定編

### ダークモードに変更

### Dock を自動的に表示/非表示

### ディスプレイスペースを拡大

### 壁紙を変更

### ロック画面

### トラックパッドの奇跡の速さ

### マウスカーソルのサイズと大きさ

## インストール編

### AWS CLI インストール

```
% curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 37.8M  100 37.8M    0     0  33.9M      0  0:00:01  0:00:01 --:--:-- 33.9M
% sudo installer -pkg ./AWSCLIV2.pkg -target /
Password:
installer: Package name is AWS Command Line Interface
installer: Installing at base path /
installer: The install was successful.
% which aws
/usr/local/bin/aws
% aws --version
aws-cli/2.15.59 Python/3.11.8 Darwin/23.5.0 exe/x86_64
%
```

### Clipy インストール

### Rectangle インストール

### Microsoft Remote Desktop インストール

### VSCode インストール

### Chrome インストール

### Microsoft Edge インストール

### Discord インストール

### Git インストール

### Homebrew インストール

以下サイトにアクセスしてインストールコマンドを実行する。

- https://brew.sh/

```
% /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
==> Checking for `sudo` access (which may request your password)...
Password:
==> This script will install:
/opt/homebrew/bin/brew
/opt/homebrew/share/doc/homebrew
/opt/homebrew/share/man/man1/brew.1
/opt/homebrew/share/zsh/site-functions/_brew
/opt/homebrew/etc/bash_completion.d/brew
/opt/homebrew
==> The following new directories will be created:
/opt/homebrew/bin
/opt/homebrew/etc
/opt/homebrew/include
/opt/homebrew/lib
/opt/homebrew/sbin
/opt/homebrew/share
/opt/homebrew/var
/opt/homebrew/opt
/opt/homebrew/share/zsh
/opt/homebrew/share/zsh/site-functions
/opt/homebrew/var/homebrew
/opt/homebrew/var/homebrew/linked
/opt/homebrew/Cellar
/opt/homebrew/Caskroom
/opt/homebrew/Frameworks

Press RETURN/ENTER to continue or any other key to abort:
==> /usr/bin/sudo /usr/bin/install -d -o root -g wheel -m 0755 /opt/homebrew
...省略
==> Next steps:
- Run these two commands in your terminal to add Homebrew to your PATH:
    (echo; echo 'eval "$(/opt/homebrew/bin/brew shellenv)"') >> /Users/【ユーザー名】/.zprofile
    eval "$(/opt/homebrew/bin/brew shellenv)"
- Run brew help to get started
- Further documentation:
    https://docs.brew.sh
% echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/【ユーザー名】/.zprofile
% eval "$(/opt/homebrew/bin/brew shellenv)"
% brew -v
Homebrew 4.3.2
```

### Node.js インストール

```
% nvm use node
Now using node v22.2.0 (npm v10.7.0)
% echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.zshrc
echo '[ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"' >> ~/.zshrc
echo '[ -s "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm"' >> ~/.zshrc

% source ~/.zshrc
% command -v nvm
nvm
% nvm install node
Downloading and installing node v22.2.0...
Downloading https://nodejs.org/dist/v22.2.0/node-v22.2.0-darwin-arm64.tar.xz...
######################################################################################################################################### 100.0%
Computing checksum with shasum -a 256
Checksums matched!
Now using node v22.2.0 (npm v10.7.0)
Creating default alias: default -> node (-> v22.2.0)
% nvm use node
Now using node v22.2.0 (npm v10.7.0)
```

## アプリ設定編

### AWS IAM 作成

https://qiita.com/ryome/items/ff2804be600cd5dcd61a

https://qiita.com/ryome/items/066d2deea3e6fbe577ad


