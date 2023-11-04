---
title: "Webアプリの自動ビルドとArtifact Registryにイメージ保管"
emoji: "📄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nextjs", "typescript", "docker", "GCP", "githubactions"]
published: true
---

# 🎯目的

- Webアプリのビルドプロセスを自動化し、成果物をDockerイメージとしてArtifact Registryに保存します。
- GCPを使用したコードのバージョン管理と、そのセットアップ手順の記録します。

# Artifact Registryとは？

Artifact Registryは、コンテナイメージや言語ベースのパッケージを一元管理するGCPのサービスです。プライベートなアーティファクトの安全なホスティング、共有、管理が可能となります。

主な特徴は以下の通りです。

- 一元管理: 複数のリポジトリタイプを同じ場所で管理できます（例：Docker, npm, Maven, etc.）。
- セキュリティ: 組織のパッケージはプライベートな環境で安全にホストされます。
- 統合: CI/CDワークフローとのシームレスな統合が可能です。
- 拡張性: 大規模なプロジェクトや組織の成長に合わせてスケールすることができます。

# 前提条件

作業を開始する前に、いくつかの設定を完了させておく必要があります。

## Google Cloud SDKのインストールと設定

Google Cloud SDKをインストールし、初期設定を行います。詳細な手順はGoogle Cloud SDKのドキュメントを参照してください。

https://cloud.google.com/sdk/docs/install?hl=ja

## GCPプロジェクトの準備

GCPプロジェクトを新規に作成するか、既存のプロジェクトを使用します。プロジェクトの作成や管理については、GCPプロジェクト管理のドキュメントを参照してください。

https://cloud.google.com/resource-manager/docs/creating-managing-projects?hl=ja

# Artifact Registryの準備

1. Artifact Registryへ移動します。
2. リポジトリを作成ボタンを押下します。
3. 必要な情報を入力します。
  - 名前
  - 形式(Docker)
  - ロケーション タイプ：リージョン(例：asia-northeast1)
  - 暗号キー：Googleが管理する暗号鍵
  - クリーンアップポリシー：任意(例：ドライラン)
4. 「作成」をクリックしてリポジトリを作成します。

# 🔐 Artifact Registry用のGCPのサービスアカウントを作成する

Artifact Registryを操作するサービスアカウントを作成します。

1. 左のナビゲーションペインから「IAM & 管理」→「サービスアカウント」を選択します。
2. 「サービスアカウントを作成」ボタンをクリックします。
3. 必要な情報（サービスアカウント名、説明等）を入力し、「作成」をクリックします。
4. 役割を付与して、サービスアカウントに必要な権限を設定します。
   - **Artifact Registry 管理者**
   - **Artifact Registry 書き取り**
   - **Artifact Registry 読み取り**
5. サービスアカウントのキーを作成します。
6. GitHubのリポジトリを作成し、シークレットにキー情報を保存します。

# Next.jsアプリの構築

以下のテクノロジースタックを使用したサンプルWebアプリを構築します。
宜、ご自身のプロジェクトに合わせてバージョンを置き換えてください。

以下はサンプル
- Next.js ： 13
- React ： 18
- TypeScript 5
- TailWindCSS： 3
- Node.js：16.17.1

```bash
npx create-next-app@latest my-next-ts-app
```
# フォルダ構成

プロジェクトのフォルダ構成は以下となります。

```
.
├── .github
│   └── workflows
│      └── build-and-deploy.yml
├── docker
│   └── Dockerfile
├── scripts
│   └── entrypoints
│      └── entryPoint.sh
├── src
│   └── app
│      ├── globals.css
│      ├── layout.tsx
│      └── page.tsx
├── .gitignore
├── next.config.js
├── package-lock.json
├── package.json
├── postcss.config.js
├── tailwind.config.js
└── tsconfig.json
```

# Dockerfileの構築

`docker\Dockerfile`のファイル内容は以下となります。

```bash
# ベースとなるイメージを指定
FROM node:16.17.1

# ユーザ設定
RUN adduser --disabled-password --gecos '' appuser

# 作業ディレクトリの設定
WORKDIR /app
RUN chown -R appuser:appuser /app

# 依存関係のファイルをコピー
COPY package*.json ./
RUN chown appuser:appuser /app/package*.json

# 依存関係のインストール
RUN npm install

# アプリケーションのソースをコピー
COPY . .

# ファイル配置
RUN chown -R appuser:appuser /app
RUN mv -f /app/scripts/entrypoint/entryPoint.sh /app/.

# 所有者変更
RUN chmod +x *.sh

# ビルド
USER appuser
RUN npm run build

# 3000番ポートを公開
EXPOSE 3000

# コンテナ起動時に実行するコマンド
ENTRYPOINT ["/app/entryPoint.sh"]
```

# Dockerエントリーポイントスクリプト

Dockerコンテナ起動時に実行する `scripts\entrypoint\entryPoint.sh`は以下とします。

```bash
#!/bin/bash

# Webアプリ起動
npm start

# 次へ
exec "$@"
```

# GitHub Actionsのワークフロー

GitHub Actionsで実行するワークフロー(.github\workflows\build-and-deploy.yml)は以下とります。
このワークフローは、GitHubリポジトリにプッシュされる度にトリガーされ、新しいDockerイメージがArtifact Registryにプッシュされます。

```yml
name: Build and Deploy to Artifact Resistory

on:
  push:
    branches:
      - main

env:
    PROJECT_ID: ${{ secrets.PROJECT_ID_NAME }}
    REPO_NAME: ${{ secrets.ARTIFACT_REPOSITORY_APP }}
    IMAGE_NAME: ${{ secrets.CONTAINER_IMAGE }}
    LOCATION: ${{ secrets.GCP_LOCATION }}
    
jobs:
  build-web:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Setup Google Cloud Authentication
      uses: google-github-actions/auth@v1
      with:
        credentials_json: ${{ secrets.GCP_KEY }}

    - name: Configure Docker
      run: gcloud auth configure-docker asia-northeast1-docker.pkg.dev

    - name: Build Docker image
      uses: docker/build-push-action@v5
      with:
        context: .
        file: docker/Dockerfile
        push: false
        tags: ${{ env.LOCATION }}-docker.pkg.dev/${{ env.PROJECT_ID }}/${{ env.REPO_NAME }}/${{ env.IMAGE_NAME }}:${{ github.sha }}

    - name: Remove previous backup
      run: |
        gcloud artifacts docker images delete ${{ env.LOCATION }}-docker.pkg.dev/${{ env.PROJECT_ID }}/${{ env.REPO_NAME }}/${{ env.IMAGE_NAME }}:backup --delete-tags || true
  
    - name: Backup latest image
      run: |
        if docker pull ${{ env.LOCATION }}-docker.pkg.dev/${{ env.PROJECT_ID }}/${{ env.REPO_NAME }}/${{ env.IMAGE_NAME }}:latest; then
          docker tag ${{ env.LOCATION }}-docker.pkg.dev/${{ env.PROJECT_ID }}/${{ env.REPO_NAME }}/${{ env.IMAGE_NAME }}:latest ${{ env.LOCATION }}-docker.pkg.dev/${{ env.PROJECT_ID }}/${{ env.REPO_NAME }}/${{ env.IMAGE_NAME }}:backup
          docker push ${{ env.LOCATION }}-docker.pkg.dev/${{ env.PROJECT_ID }}/${{ env.REPO_NAME }}/${{ env.IMAGE_NAME }}:backup
        else
          echo "The 'latest' tag does not exist, skipping..."
        fi

    - name: Tag and Push Docker image
      run: |
        docker tag ${{ env.LOCATION }}-docker.pkg.dev/${{ env.PROJECT_ID }}/${{ env.REPO_NAME }}/${{ env.IMAGE_NAME }}:${{ github.sha }} ${{ env.LOCATION }}-docker.pkg.dev/${{ env.PROJECT_ID }}/${{ env.REPO_NAME }}/${{ env.IMAGE_NAME }}:latest
        docker push ${{ env.LOCATION }}-docker.pkg.dev/${{ env.PROJECT_ID }}/${{ env.REPO_NAME }}/${{ env.IMAGE_NAME }}:latest

 ```

GCP_KEY：サービスアカウントのキー情報
PROJECT_ID_NAME：プロジェクトID
ARTIFACT_REPOSITORY_APP：リポジトリ名
CONTAINER_IMAGE：コンテナイメージ名
GCP_LOCATION：リージョン名(例：asia-northest1)

# 最後に

この記事では、WebアプリのDockerイメージを自動的にビルドし、GCPのArtifact Registryに保存するプロセスを設定しました。これにより、開発チームはアプリケーションのデプロイメントを簡単かつ迅速に行うことができ、バージョン管理を効果的に行うことが可能となります。

最後までお読みいただきありがとうございました。