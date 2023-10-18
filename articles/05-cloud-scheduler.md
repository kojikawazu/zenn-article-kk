---
title: "Cloud Functionsの関数をCloud Schedulerで実行"
emoji: "⏰"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["zenn","cloudfunctions", "cloudscheduler", "GCP","githubactions"]
published: true
---

# 🎯目的

- Cloud Functionsの関数をcronジョブとして定期実行する設定を理解し、構築する。
- Cloud SchedulerのジョブをGitHub Actionsで自動デプロイするプロセスを構築する。
- 最初の導入メモとしても利用。

# 前提条件

作業を開始する前に、いくつかの設定を完了させておく必要がある。

## Google Cloud SDKのインストールと設定

Google Cloud SDKをインストールし、初期設定を行います。詳細な手順はGoogle Cloud SDKのドキュメントを参照すること。

https://cloud.google.com/sdk/docs/install?hl=ja

## GCPプロジェクトの準備

GCPプロジェクトを新規に作成するか、既存のプロジェクトを使用します。プロジェクトの作成や管理については、GCPプロジェクト管理のドキュメントを参照すること。

https://cloud.google.com/resource-manager/docs/creating-managing-projects?hl=ja

# 🔐 Cloud Scheduler用のGCPのサービスアカウントを作成する

Cloud SchedulerがCloud Functionsの関数を呼び出すための認証に使用するサービスアカウントを作成する。

1. 左のナビゲーションペインから「IAM & 管理」→「サービスアカウント」を選択する。
2. 「サービスアカウントを作成」ボタンをクリックする。
3. 必要な情報（サービスアカウント名、説明等）を入力し、「作成」をクリックする。
4. 役割を付与して、サービスアカウントに必要な権限を設定する。
   - **Cloud Run 起動元**
     - Cloud SchedulerからCloud Functionsの実行権を付与する。

# ⏱️ Cloud Schedulerのジョブを作成する。

Cloud Schedulerで新しいジョブを作成し、Cloud Functionsの関数を定期的に実行するように設定する。

1. 「ジョブのスケジュールを設定」ボタンを押下する。
2. 必要な情報を入力する。
- スケジュールを定義する。
  - 名前
  - リージョン
  - 説明
  - 頻度
    - 毎日9時の場合
      - 0 9 * * *
  - タイムゾーン 

- 実行内容を定義する。
  -  ターゲットタイプ
    - HTTPに設定
  - URL
    - Cloud FunctionsのURLを設定
  - HTTPメソッド
    - Cloud Functionsで使用するメソッド
  - HTTPヘッダー
    - 必要の場合ヘッダも設定
  - Authヘッダー(Cloud Functionsの認証が必要な場合、以降を入力)
    - OIDCトークンを追加
  - サービスアカウント
    - 先ほど作成したサービスアカウントを選択する。
  - 対象
    - Cloud Functionsの関数のURLを設定する。

3. 「作成」ボタンを押下してジョブをスケジュールする。

これで、Cloud Schedulerは毎日指定した時間にCloud Functionsの関数を自動的に実行します。

# 🚀 GitHub ActionsでのCloud Schedulerジョブの自動デプロイ

GitHub Actionsを使用して、Cloud Schedulerのジョブを自動的にデプロイするCI/CDパイプラインを構築する。

### サービスアカウントの設定

1. GCPにて、Cloud Scheduler自動デプロイ用のサービスアカウントを作成する。
2. 新しいサービスアカウントに以下の役割を付与する：
   - Cloud Scheduler 閲覧者
   - Cloud Scheduler 管理者
   - サービス アカウント ユーザー

### GitHubリポジトリの設定

1. Cloud Schedulerのジョブ設定用の新しいGitHubリポジトリを作成する。
2. リポジトリに「.github/workflows/deploy-cloud-scheduler.yml」という名前の新しいワークフローを作成する。

以下は、ワークフローのYAMLファイルの例。各項目を自分の環境に合わせて適切に置き換えてください。

```yml
name: Deploy to Google Cloud Scheduler Job

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
        # Node.jsのバージョンは任意
        node-version: 20
    
    - name: Setup Google Cloud Authentication
      uses: google-github-actions/auth@v1
      with:
        credentials_json: ${{ secrets.GCP_KEY }}
    
    - name: Decode GCP Service Account Key
      run: |
        # base64でエンコードしたキー情報をデコードし、ファイル出力する
        echo "${{ secrets.GCP_KEY_BASE64 }}" | base64 -d > temp-sa.json
    
    - name: Authenticate with gcloud using temp-sa.json
      run: |
        # デコードしたキー情報で認証を行う
        gcloud auth activate-service-account --key-file=temp-sa.json

    - name: Deploy Cloud Scheduler Job
      run: |
        _operation="create"

        if gcloud scheduler jobs describe ${{ secrets.SCHEDULER_JOB_NAME }} --location="${{ secrets.REGION_NAME }}"; then
          # 既にCloud Schedulerが存在する場合は更新処理とする
          # それ以外は新規追加とする
          _operation="update"
        fi

        # Cloud Schedulerのデプロイ
        gcloud scheduler jobs $_operation http ${{ secrets.SCHEDULER_JOB_NAME }} \
          --schedule="${{ secrets.SCHEDULER_VALUE }}" \
          --uri="${{ secrets.SCHEDULER_JOB_URL }}" \
          --oidc-service-account-email="${{ secrets.SERVICE_ACCOUNT_EMAIL }}" \
          --oidc-token-audience="${{ secrets.SCHEDULER_JOB_URL }}" \
          --http-method=POST \
          --location="${{ secrets.REGION_NAME }}" \
          --description="${{ secrets.SCHEDULER_DESC }}" \
          --time-zone="${{ secrets.SCHEDULER_TIME_ZONE }}"
    
    - name: Post Processing
      run : |
        rm -f temp-sa.json
```

- GCP_KEY: サービスアカウントのキー(JSONデータ)
- GCP_KEY_BASE64: サービスアカウントのキーをbase64で変換したデータ
- SCHEDULER_JOB_NAME: Cloud Schedulerのジョブ名
- REGION_NAME: リージョン名
- SCHEDULER_VALUE: スケジュール(0 9 * * *)
- SCHEDULER_JOB_URL: Cloud FunctionsのURL
- SERVICE_ACCOUNT_EMAIL: Cloud SchedulerからCloud Functionsを実行する場合のサービスアカウントのEmail。このサービスアカウントによりOIDC認証を行う。
- SCHEDULER_JOB_URL: 認証を行う対象。今回はCloud FunctionsのURLとなる。
- SCHEDULER_DESC: Cloud Schedulerの説明
- SCHEDULER_TIME_ZONE: Cloud Schedulerのタイムゾーン(例：Asia/Tokyo)

# OIDC認証の概要
OpenID Connect（OIDC）認証は、ユーザーの身元を確認する安全な手段です。これはOAuth 2.0に基づいており、特定の情報を共有する際にユーザーの許可が必要です。

###  サービスアカウントとの関連
- サービスアカウントの役割: サービスアカウントは、特定のアプリケーションやサービスがリソースにアクセスするために使用されます。これにより、人間の介入なしにプロセスが実行されます。

- 一時的な認証情報: OIDCを使用すると、サービスアカウントは短期間有効な認証情報を生成できます。これにより、必要なタスクに必要な期間だけアクセス権が与えられ、その後、認証情報は無効になります。

これにより、セキュリティが強化され、不正アクセスのリスクが減少します。

# 🎉 まとめ
この記事では、Cloud Schedulerを使用してCloud Functionsの関数を定期的に実行する方法、およびGitHub Actionsを使用してCloud Schedulerのジョブを自動デプロイする方法について解説しました。これらの技術を組み合わせることで、サーバーレス環境での定期的なタスク実行やCI/CDパイプラインの構築が容易になります。

最後までお読みいただきありがとうございました。