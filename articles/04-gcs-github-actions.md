---
title: "GitHub Actionsを利用してGCPのCloud Storageへの自動デプロイを実現する方法"
emoji: "⚙️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["zenn","cloudfunctions", "cloudstorage", "GCP","githubactions"]
published: true
---

# 🎯目的

- Cloud Storageのリソースデータを自動デプロイできるようにしたい。
- Cloud Storage, GitHub Actionsも非公開とする。
- 最初の導入メモとしても利用。

# 前提条件

まずは以下設定を行います。

- Google Cloud SDKのインストールと設定

  以下ドキュメントを参考にしてください。

https://cloud.google.com/sdk/docs/install?hl=ja

- GCPプロジェクトのセットアップ

まだ一度もGCPを触れていない方はこちらを参考にしてください。

https://cloud.google.com/resource-manager/docs/creating-managing-projects?hl=ja

# 🗝️GCPへの認証情報（JSONキー）をGitHub Secretsに保存

GCPでサービスアカウントを作成し、キーを生成します。

1. 左のナビゲーションペインから「IAM & 管理」→「サービスアカウント」を選択します。
2. 「サービスアカウントを作成」ボタンをクリックします。
3. 必要な情報（サービスアカウント名、説明等）を入力し、「作成」をクリックします。
4. 役割を付与して、サービスアカウントに必要な権限を設定します。
   - **Storage オブジェクト閲覧者**
   - **Storage オブジェクト作成者**
   - **ストレージ管理者**
5. 「キー」タブをクリックして、「鍵を追加」→「JSON」を選択し、キーを生成してダウンロードします。このJSONファイルは後ほどGitHub Secretsに追加するものとなります。

# 🔑GitHub Secretsにサービスアカウントキーを追加

1. 保存したJSONキーをテキストエディタで開き、その内容をコピーします。
2. GitHubリポジトリに移動し、「Settings」タブをクリックします。
3. 左のサイドバーから「Secrets and variables」を選択し、「Actions」へ移動します。
4. 「Secrets」タブを選択中に「New repository secret」ボタンをクリックします。
5. 「Name」フィールドに秘密情報の名前を入力します（例: GCP_KEY）。
   そして、「Value」フィールドに、先ほどコピーしたサービスアカウントキーのJSON文字列をペーストします。
6. 「Add secret」をクリックして保存します。

# 🔑その他シークレットをGitHub Secretsに追加

- BUCKET_REGION：デプロイ先バケットのリージョン
- GCP_KEY_BASE64：サービスアカウントキーをbase64で変換したもの

変換コマンドは以下を利用します。
```bash
base64 -w0 < [サービスアカウントキー].json
```

- GCS_BUCKET_PATH・・・Cloud Storageのバケット名(gs://バケット名)
- PROJECT_ID ・・・プロジェクトID
- STORAGE_CLASS・・・追加するバケットのストレージクラス(standard/nearline/coldline/archive)

## 📂リポジトリのフォルダ構成

```markdown

# 📂GitHubリポジトリのフォルダ構成

- .github
  - workflows
    - deploy-storage.yml
- data
  - json
    - sample.json
- README.md
```

「.github/workflows/deploy-storage.yml」は

```yml
name: Deploy to Google Cloud Storage

on:
  push:
    paths:
      - 'data/json/**'
    branches:
      - main
  workflow_dispatch:

env:
  # 環境変数
  JSON_DIR: data/json/

jobs:
    deploy:
        runs-on: ubuntu-latest

        steps:
        - uses: actions/checkout@v4
    
        - name: Setup GCP
          uses: google-github-actions/auth@v1
          with:
            credentials_json: ${{ secrets.GCP_KEY }}
        
        - name: Decode GCP Service Account Key
          run: |
            echo "${{ secrets.GCP_KEY_BASE64 }}" | base64 -d > temp-sa.json
        
        - name: Authenticate with gcloud using temp-sa.json
          run: |
            gcloud auth activate-service-account --key-file=temp-sa.json

        - name: Create GCS Bucket
          run: |
            if ! gsutil ls ${{ secrets.GCS_BUCKET_PATH }}; then
              gsutil mb -p ${{ secrets.PROJECT_ID }} -c ${{ secrets.STORAGE_CLASS }} -l ${{ secrets.BUCKET_REGION }} -b off ${{ secrets.GCS_BUCKET_PATH }}
            fi

        - name: Upload JSON data to GCS
          run: |
            gsutil -m rsync -r -d ${{ env.JSON_DIR }} ${{ secrets.GCS_BUCKET_PATH }}/${{ env.JSON_DIR }}

        - name: Post Processing
          run : |
            rm -f temp-sa.json
```

ここでは主に以下のステップを実行しています。

1. Setup GCP: GCPとの認証を設定します。
2. Decode GCP Service Account Key: Base64でエンコードされたサービスアカウントキーをデコードします。
3. Authenticate with gcloud using temp-sa.json: デコードしたキーを使用してgcloudに認証します。
4. Create GCS Bucket: Cloud Storageのバケットが存在しない場合、新しく作成します。
5. Upload JSON data to GCS: ローカルのデータをCloud Storageにアップロードします。
6. Post Processing: 一時ファイルを削除

以上の内容をGitHubでプルリクが通った後にGitHub Actionsが自動的に走ります。GitHub Actionsの処理に成功するとCLoud Storageが自動作成、自動更新されます。

# 参考URL

- バケットを作成します。
「gsutil mb」コマンドのオプションが確認できます。

https://cloud.google.com/storage/docs/creating-buckets?hl=ja#storage-create-bucket-gsutil

---

最後までお読みいただき本当にありがとうございます！
