---
title: "GitHub ActionsでCloud Functionsを自動デプロイ"
emoji: "📄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["zenn","cloudfunctions","GCP","githubactions"]
published: true
---

# 🎯目的

- Cloud Functionsの関数をコード管理したい。
- 関数をローカル上で実装した。
- 最初の導入メモとしても利用。

# 前提条件

まずは以下の設定を行いました。

- Google Cloud SDKのインストールと設定

  以下ドキュメントを参考にしてください。

https://cloud.google.com/sdk/docs/install?hl=ja

- GCPプロジェクトのセットアップ

まだ一度もGCPを触れてない方はこちらを参考にしてください。

https://cloud.google.com/resource-manager/docs/creating-managing-projects?hl=ja

# 🗝️GCPへの認証情報（JSONキー）をGitHub Secretsに保存

GCPでサービスアカウントを作成し、キーを生成します。

1. 左のナビゲーションペインから「IAM & 管理」→「サービスアカウント」を選択します。
2. 「サービスアカウントを作成」ボタンをクリックします。
3. 必要な情報（サービスアカウント名、説明等）を入力し、「作成」をクリックします。
4. 役割を付与して、サービスアカウントに必要な権限を設定する。
   （例: Cloud Functions 管理者や サービス アカウント ユーザー など）
5. 「キー」タブをクリックして、「鍵を追加」→「JSON」を選択し、キーを生成してダウンロードする。このJSONファイルは後ほどGitHub Secretsに追加するものとなります。

# 🔐サービスアカウントに関するセキュリティ注意

サービスアカウントの権限付与には、Principle of Least Privilege (PoLP) を念頭に置くことが重要です。つまり、サービスアカウントが行う作業のみに必要な最小限の権限のみを付与することをお勧めします。不要な権限は削除し、もし秘密鍵が漏洩しても被害を最小限に抑えるよう努めましょう。

# 🕵️‍♂️GitHub Secretsにサービスアカウントキーを追加

1. 保存したJSONキーをテキストエディタで開き、その内容をコピーします。
2. GitHubリポジトリに移動し、「Settings」タブをクリックします。
3. 左のサイドバーから「Secrets and variables」を選択し、「Actions」へ移動します。
4. 「Secrets」タブを選択中に「New repository secret」ボタンをクリックします。
5. 「Name」フィールドに秘密情報の名前を入力します（例: GCP_KEY）。
「Value」フィールドに、先ほどコピーしたサービスアカウントキーのJSON文字列をペーストします。
6. 「Add secret」をクリックして保存します。

# 🚀デプロイする関数のコードをリポジトリにプッシュ

まずはGitHubにリポジトリを作成し、cloneします。
その後、Node.jsの初期化を行います。

```bash
# GitHubからリポジトリをcloneします
# (例: git clone https://github.com/username/repo.git)
git clone [GitHubのリポジトリのURL]
cd [リポジトリのディレクトリ名]

# Node.jsの初期化を行います
npm init
```

次にpackage.jsonに以下設定を追加します。
その後、もう一度プロジェクトの再更新を行います。

```json
// package.json

{
  "dependencies": {
    "@google-cloud/functions-framework": "^3.0.0"
  }
}
```

```bash
# プロジェクトのpackage.jsonの再更新
npm install
```

その後、index.jsを作成する。

```javascript
// index.js
const functions = require('@google-cloud/functions-framework');

/**
 *  HTTPSで実行される関数
 *  http関数の第1引数は、エントリポイントと一致させること
 */
functions.http('helloHttp', (req, res) => {
  res.send(`Hello ${req.query.name || req.body.name || 'World'}!`);
});
```

# 🔄GitHub Actionsワークフロー設定

「.github/workflows/任意の名前.yaml」を生成し、以下内容で実装します。

```yaml
# deploy-gcf.yml
name: Deploy to Google Cloud Functions

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Use Node.js
      uses: actions/setup-node@v2
      with:
        node-version: 20
    
    - name: Setup Google Cloud Authentication
      uses: google-github-actions/auth@v1
      with:
        credentials_json: ${{ secrets.GCP_KEY }}

    - name: Deploy to Google Cloud Functions
      run: |
        gcloud functions deploy YOUR-FUNCTIONS-NAME \
          --trigger-http \
          --region REGION-NAME \
          --runtime RUNTIME-NAME \
          --entry-point ENTRY-POINT \
          --allow-unauthenticated
```

- YOUR-FUNCTIONS-NAME・・・Cloud Functionsの関数名
- REGION-NAME・・・リージョン名
- ENTRY-POINT・・・エントリポイント名
- RUNTIME-NAME・・・ランタイム名
- --allow-unauthenticated・・・「認証無し」
- secrets.GCP_KEY・・・GitHubのシークレットに保存した値

# 🔐セキュリティについての注意

--allow-unauthenticated オプションを使用すると、認証なしで関数にアクセスできるようになります。これはテストやデモなど、セキュリティが要求されないケースでのみ使用すべきオプション。本番環境では別の方法で認証を行うことを推奨します。

具体的な認証のアプローチとしては：

- APIキー: 一部のAPIでは、リクエストの認証にAPIキーを使用することができます。
- OAuth トークン: よりセキュアなオプションとして、OAuthを使用してユーザー認証を行う方法があります。
- IAM (Identity and Access Management): GCPのIAMを利用して、特定のGoogleアカウントに対して関数のアクセスを許可します。

---

最後までお読みいただき本当にありがとうございます！