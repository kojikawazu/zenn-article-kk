---
title: "AWS App Runner + RDSにGitHub Actionsで自動デプロイする方法"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["aws", "githubactions", "terraform", "apprunner", "rds"]
published: true
---

# はじめに

AWSを学び進める過程で、様々なサービスを触ってきました。今度は自動デプロイを導入し、より効率的で使いやすい環境の引き出しを増やしていきます。

# スキルスタック

今回は、CodePipelineを用いたグリーンブルーデプロイとは異なるアプローチを採り、GitHub Actionsを活用してみることにしました。主に二つの理由があります。

- GitHubの統合: コード管理をGitHubで行っており、GitHub Actionsを頻繁に使用しています。この既に慣れ親しんだ技術を利用することで、スムーズに進められると考えています。
- 自動化の柔軟性: GitHub Actionsは、その柔軟性と統合性により、多様なワークフローを簡単に自動化できます。

将来的には、CodePipelineに関する知識を深めて、技術スタックを広げる予定です。

その他スタックは以下となります。

- バックエンド: Laravelフレームワークを使用したAPIアプリケーションの構築に焦点を当てています。
- 実行環境: AWS App Runnerを選択しました。
- データベース: PostgreSQLを搭載したAmazon RDSを利用しています。
- インフラストラクチャの自動構築: Terraformを採用し、コード化されたインフラ管理を実現します。

![アーキテクチャ](https://storage.googleapis.com/zenn-user-upload/d668815e852f-20240217.png)

# IAMユーザーの作成(GitHubからAWSアクセス用)

GitHubからAWSのリソースにアクセスするためには、適切な権限を持つIAMユーザーを作成し、そのユーザーに必要なポリシーをアタッチする必要があります。

## ステップ1: IAMポリシーの作成

以下のJSON形式のポリシーをAWS Management Consoleで作成します。このポリシーには、ECRへのイメージプッシュに必要な権限と、App Runnerサービスのデプロイメントを開始する権限が含まれています。

:::message
App Runnerサービスに対するポリシーは、サービス作成後に、具体的なリソースARN（Amazon Resource Name）に対してポリシーを適用してください。
:::

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ecr:GetAuthorizationToken",
        "ecr:BatchCheckLayerAvailability",
        "ecr:InitiateLayerUpload",
        "ecr:UploadLayerPart",
        "ecr:CompleteLayerUpload",
        "ecr:PutImage"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "apprunner:StartDeployment",
      "Resource": "arn:aws:apprunner:region:account-id:service/service-name/service-id"
    }
  ]
}
```

## ステップ2: IAMユーザーの作成とポリシーのアタッチ

AWS Management ConsoleからIAMユーザーを作成し、上記で作成したポリシーをそのユーザーにアタッチします。IAMユーザーの作成方法は、AWSの公式ドキュメントやガイドを参照してください。

## ステップ3: アクセスキーの作成と保存

IAMユーザーの「セキュリティ認証情報」タブから「アクセスキー」を作成し、表示されるアクセスキーIDとシークレットアクセスキーを安全な場所に保存してください。これらのキーは、GitHubのリポジトリのシークレットに登録し、GitHub ActionsからAWSサービスに安全にアクセスするために使用します。

# ECRの構築

App Runnerを使用してDockerイメージからアプリケーションをデプロイします。そのためには、まずDockerイメージを保存できる場所、つまりECR（Elastic Container Registry）が必要になります。

以下の手順では、Terraformを利用してECRの構築します。
なお、AWSへのアクセスに必要なIAMユーザーの作成は割愛します。

## ステップ1: Terraformの設定

Terraformを使用してAWSリソースを管理するための基本的な設定は以下になります。
この設定では、Terraformのバージョンや必要なプロバイダーを指定しています。

:::details main.tf
```tf
# ---------------------------------------------
# Terraform configuration
# ---------------------------------------------
terraform {
  required_version = ">=1.6"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

# ---------------------------------------------
# Provider
# ---------------------------------------------
provider "aws" {
  profile = "terraform"
  region  = "ap-northeast-1"
}

# ---------------------------------------------
# Variables
# ---------------------------------------------
variable "project" {
  type = string
}

variable "environment" {
  type = string
}

# 変数定義はその都度追加してください
```
:::

## ステップ2: ECRリポジトリの構築

以下のTerraform設定は、ECRリポジトリを作成します。
このリポジトリはイメージのスキャンをプッシュ時に自動で行い、タグを付けて管理します。

```tf:api_ecr.tf
# ---------------------------------------------
# API - ECR
# ---------------------------------------------
resource "aws_ecr_repository" "api_repository" {
  name                 = "${var.project}-${var.environment}-api-repository"
  image_tag_mutability = "MUTABLE"

  image_scanning_configuration {
    scan_on_push = true
  }

  tags = {
    Name    = "${var.project}-${var.environment}-api-repository"
    Project = var.project
    Env     = var.environment
  }
}
```

:::message
構築を完了させるためには、「terraform init」「terraform plan」「terraform apply」のコマンドを順に実行してください。
:::

# Dockerfileの作成

Laravelプロジェクトを効率的にコンテナ化するために、以下のDockerfileを作成し、GitHubリポジトリ内のLaravelプロジェクトフォルダに配置します。

```bash:Dockerfile
# PHP 8.1の公式イメージをベースとする
FROM php:8.1-fpm

# 作業ディレクトリを設定
WORKDIR /var/www/html

# Laravelの要件に必要なパッケージをインストール
RUN apt-get update && apt-get install -y \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    libzip-dev \
    libpq-dev \
    postgresql-client \
    zip \
    unzip \
    curl \
    git \
    vim \
    postgresql-client \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd pdo_pgsql

# Composerをインストール
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Laravelアプリケーションのソースコードをコンテナにコピー
COPY . /var/www/html

# Composerを使ってPHP依存関係をインストール
RUN composer install --no-interaction --optimize-autoloader --no-dev

# Laravelのストレージとキャッシュディレクトリの権限を設定
RUN chown -R www-data:www-data /var/www/html/storage \
    && chmod -R 775 /var/www/html/storage \
    && chown -R www-data:www-data /var/www/html/bootstrap/cache \
    && chmod -R 775 /var/www/html/bootstrap/cache

# DBマイグレーション
RUN php artisan migrate; \
    # キャッシュの削除
    php artisan cache:clear; \
    # キャッシュの再生成
    php artisan config:cache

# コンテナがリッスンするポートを指定
EXPOSE 8000

# 開発サーバーの起動コマンド
CMD php artisan cache:clear; \
    php artisan config:cache; \
    php artisan serve --host=0.0.0.0 --port=8000
```

# GitHub Actionsで自動デプロイ

次は、LaravelプロジェクトのDockerイメージを自動的にAWS Elastic Container Registry (ECR)にプッシュするGitHub Actionsのワークフローを作成します。コードの変更がmainブランチにプッシュされるたびに、最新のイメージがECRにアップロードされます。

## ステップ1: ワークフローファイルの設定

「.github/workflows」ディレクトリをリポジトリのルートに作成し、その中にワークフロー定義ファイル（例：deploy-to-ecr.yml）を作成してください。

## ステップ2: ワークフロー定義

以下のYAMLコードは、Dockerイメージをビルドし、タグ付けして、ECRにプッシュするためのGitHub Actionsワークフローを定義します。

```yml:.github/workflows/deploy-to-ecr.yml
name: Deploy Docker Image to AWS ECR

# mainブランチへのプッシュイベント
on:
  push:
    branches:
      - main

jobs:
  build-and-push:
    name: Build and Push to Amazon ECR
    # Ubuntu最新版を実行環境として使用します。
    runs-on: ubuntu-latest
    steps:
      # GitHubリポジトリからコードをチェックアウトします。
      - name: Checkout code
        uses: actions/checkout@v2

      # AWSの認証情報を設定します。
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}

      # Amazon ECRにログインします。
      - name: Login to Amazon ECR
        run: aws ecr get-login-password --region ${{ secrets.AWS_REGION }} | docker login --username AWS --password-stdin ${{ secrets.AWS_ACCOUNT_ID }}.dkr.ecr.${{ secrets.AWS_REGION }}.amazonaws.com

      # Dockerイメージをビルドし、タグ付けしてAmazon ECRにプッシュします。
      - name: Build, tag, and push Docker image to Amazon ECR
        env:
          ECR_REPOSITORY: ${{ secrets.ECR_REPOSITORY }}
          IMAGE_TAG: latest
        run: |
          IMAGE_URI=${{ secrets.AWS_ACCOUNT_ID }}.dkr.ecr.${{ secrets.AWS_REGION }}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}
          docker build -t $IMAGE_URI -f [Laravelプロジェクト名]/Dockerfile [Laravelプロジェクト名]/
          docker push $IMAGE_URI

      # Amazon ECRからログアウトします。
      - name: Logout of Amazon ECR
        run: docker logout ${{ secrets.AWS_ACCOUNT_ID }}.dkr.ecr.${{ secrets.AWS_REGION }}.amazonaws.com
```

## ステップ3: GitHubリポジトリのシークレットの登録

GitHubリポジトリを作成し、以下のシークレットを登録してください。これにより、ワークフローがAWSの認証情報に安全にアクセスし、ECRにイメージをプッシュできるようになります。

- AWS_ACCOUNT_ID: AWSアカウントのID。
- AWS_REGION: 使用するAWSリージョン名。
- ECR_REPOSITORY: ECRのリポジトリ名。
- AWS_ACCESS_KEY_ID: AWSのIAMユーザーのアクセスキー。
- AWS_SECRET_ACCESS_KEY: AWSのIAMユーザーのシークレットアクセスキー。

## ステップ4: GitHubリポジトリへプッシュ

GitHubリポジトリへのプッシュを行った後、GitHub Actionsのワークフローが自動的にトリガーされ、設定したプロセスに従ってDockerイメージがAWS Elastic Container Registry(ECR)にプッシュされます。このプロセスは、リポジトリのmainブランチにコードの変更がプッシュされると起動します。

### GitHub Actionsの結果確認

GitHub Actionsの実行ログとともに「Actions」タブに表示されます。

![GitHub Actions](https://storage.googleapis.com/zenn-user-upload/f0edd1cd5c18-20240217.png)

### ECRへのイメージプッシュの確認

AWS Management ConsoleからECRサービスを開き、対象のリポジトリを選択することで確認できます。リポジトリ内に新しいイメージがプッシュされています。

![ECR](https://storage.googleapis.com/zenn-user-upload/4c6ba40c7c7f-20240217.png)

# App Runnerの構築

AWS App Runnerは、コンテナ化されたWebアプリケーションを簡単にデプロイし、運用するためのマネージドサービスです。Terraformを使用して、App Runnerの構成とデプロイメントを自動化することができます。

## ステップ1: API用のIAMロールの設定

App RunnerサービスがAWSリソースにアクセスするためのIAMロールを作成します。
App RunnerがECRにアクセスするためのIAMロールとポリシーのアタッチメントを定義しています。

:::message
var.変数名となっている箇所は、予め変数定義を随時行ってください。
:::

```tf:api_iam.tf
# ---------------------------------------------
# API - IAMロール
# ---------------------------------------------
# IAMロールの作成 (App Runner Access Role)
resource "aws_iam_role" "api_app_runner" {
  name = "${var.project}-${var.environment}-api-app-runner-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17",
    Statement = [
      {
        Action = "sts:AssumeRole",
        Principal = {
          Service = ["apprunner.amazonaws.com", "build.apprunner.amazonaws.com"]
        },
        Effect = "Allow",
        Sid    = ""
      },
    ]
  })
}

# ---------------------------------------------
# API - IAMロールアタッチメント
# ---------------------------------------------
# AWS管理ポリシーのアタッチメント
resource "aws_iam_role_policy_attachment" "api_app_runner_ecr" {
  role       = aws_iam_role.api_app_runner.name
  policy_arn = var.policy_app_runner_ecs_access_arn
}

# App Runnerサービス作成前の待機時間の設定
resource "time_sleep" "wait_10_seconds" {
  create_duration = "10s"
  triggers = {
    role_arn = aws_iam_role.api_app_runner.arn
  }
}
```

:::message alert
App RunnerサービスをTerraformで構築する際には、特定のリソースが完全にプロビジョニングされ、操作可能な状態になるまで待機する必要がある場合があります。特に、AWSのIAMロールやポリシーなど、他のサービスにアクセス権を提供するリソースは、AWS側で完全にアクティブ化されるまでにわずかな時間がかかることがあります。この遅延は、後続のリソース（この場合はApp Runnerサービス）がこれらのIAMリソースを利用する際にエラーを引き起こす可能性があります。

Terraformでは、time_sleepリソースを使用して、このような状況に対処し、必要なリソースのプロビジョニングが完了するまで処理を一時的に停止させることができます。
:::

## ステップ2: API用のApp Runnerの設定

App Runnerサービスを定義し、Dockerコンテナを実行する設定を行います。
以下のコードは、ECRからイメージを取得し、指定された環境変数を使用してApp Runnerサービスを構成します。

```tf:api_app_runner.tf
# ---------------------------------------------
# API - App Runner
# ---------------------------------------------
resource "aws_apprunner_service" "api_app_runner" {
  service_name = "${var.project}-${var.environment}-api-app-runner"
  depends_on   = [time_sleep.wait_10_seconds]

  source_configuration {
    authentication_configuration {
      # App RunnerがECRにアクセスするためのIAMロールARNを指定します。
      access_role_arn = aws_iam_role.api_app_runner.arn
    }

    image_repository {
       # 使用するECRイメージの識別子（リポジトリ名:タグ）を指定します。
      image_identifier      = var.app_repository
      # イメージリポジトリのタイプをECRに設定します。
      image_repository_type = "ECR"
      image_configuration {
        # コンテナがリッスンするポート番号を指定します。
        port = var.api_port
        # アプリケーションの実行時環境変数を設定します。(Laravelの.envをここに記載するイメージ)
        runtime_environment_variables = {
          APP_NAME  = var.app_name
          APP_ENV   = var.app_env
          APP_KEY   = var.app_key
          APP_DEBUG = var.app_debug
          APP_URL   = var.app_url

          LOG_CHANNEL              = var.log_channel
          LOG_DEPRECATIONS_CHANNEL = var.log_deprecations_channel
          LOG_LEVEL                = var.log_level

          BROADCAST_DRIVER = var.broadcast_driver
          CACHE_DRIVER     = var.cache_driver
          FILESYSTEM_DISK  = var.filesystem_disk
          QUEUE_CONNECTION = var.queue_connection
          SESSION_DRIVER   = var.session_driver
          SESSION_LIFETIME = var.session_lifetime
        }
      }
    }
  }

  network_configuration {
    egress_configuration {
      egress_type = "DEFAULT"
    }
  }
}
```

## ステップ3: Terraform構築後の確認

Terraformを用いてApp Runnerサービスの設定が完了した後、以下の手順でデプロイメントの成功を確認します。

### App Runnerコンソールの確認: 

AWSの管理コンソールからApp Runnerサービスにアクセスし、新しく作成されたサービスの状態を確認します。

![App Runner](https://storage.googleapis.com/zenn-user-upload/001ca67a3907-20240217.png)

### アプリケーションの動作確認: 

App Runnerによって提供されるURLにアクセスし、ブラウザからアプリケーションが正しく動作しているかを確認します。

![アプリケーション確認](https://storage.googleapis.com/zenn-user-upload/3d76be65af92-20240217.png)

# App Runnerの更新トリガーを追加

GitHub ActionsワークフローにApp Runnerサービスの更新トリガーを追加することで、新しいDockerイメージがAmazon Elastic Container Registry(ECR)にプッシュされた際に自動的にApp Runnerサービスを更新することができます。

以下のYAMLコードスニペットは、ビルドしてプッシュしたDockerイメージをもとにApp Runnerサービスを更新するためのステップです。このステップを追加します。

```yml:.github/workflows/deploy-to-ecr.yml
# (省略)

jobs:
  build-and-push:
    name: Build and Push to Amazon ECR
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      # (省略)

      - name: Update App Runner service
        run: |
          aws apprunner start-deployment --service-arn ${{ secrets.APP_RUNNER_SERVICE_ARN }}
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: ${{ secrets.AWS_REGION }}
```

:::message
GitHubリポジトリに以下シークレットを追加してください。

- APP_RUNNER_SERVICE_ARN : App RunnerサービスのARN
:::

## 設定後の確認

GitHub Actionsを動かし、再デプロイを行います。
全てのJobが終了した後にApp Runnerのサービスが自動的にデプロイを始めたら成功です。

![App Runner確認](https://storage.googleapis.com/zenn-user-upload/09647a66dd79-20240217.png)

# RDSの構築

Amazon Relational Database Service(RDS)を含む環境を構築します。Terraformを使って構築します。

## ステップ1: ネットワークの構築

まず、RDSインスタンス用のVPCネットワークを構築します。このVPCはApp Runnerとは別に設定します。

:::details network.tf
```tf
# ---------------------------------------------
# RDS - VPC
# ---------------------------------------------
resource "aws_vpc" "vpc" {
  cidr_block                       = var.vpc_address
  instance_tenancy                 = "default"
  enable_dns_support               = true
  enable_dns_hostnames             = true
  assign_generated_ipv6_cidr_block = false

  tags = {
    Name    = "${var.project}-${var.environment}-vpc"
    Project = var.project
    Env     = var.environment
  }
}

# ---------------------------------------------
# Subnet
# ---------------------------------------------
# private 1a
resource "aws_subnet" "private_subnet_1a" {
  vpc_id                  = aws_vpc.vpc.id
  availability_zone       = "ap-northeast-1a"
  cidr_block              = var.private_1a_address
  map_public_ip_on_launch = false

  tags = {
    Name    = "${var.project}-${var.environment}-private-subnet-1a"
    Project = var.project
    Env     = var.environment
    Type    = "private"
  }
}

# private 1c
resource "aws_subnet" "private_subnet_1c" {
  vpc_id                  = aws_vpc.vpc.id
  availability_zone       = "ap-northeast-1c"
  cidr_block              = var.private_1c_address
  map_public_ip_on_launch = false

  tags = {
    Name    = "${var.project}-${var.environment}-private-subnet-1c"
    Project = var.project
    Env     = var.environment
    Type    = "private"
  }
}

# ---------------------------------------------
# Route table
# ---------------------------------------------
resource "aws_route_table" "private_rt" {
  vpc_id = aws_vpc.vpc.id

  tags = {
    Name    = "${var.project}-${var.environment}-private-rt"
    Project = var.project
    Env     = var.environment
    Type    = "private"
  }
}

resource "aws_route_table_association" "private_rt_1a" {
  route_table_id = aws_route_table.private_rt.id
  subnet_id      = aws_subnet.private_subnet_1a.id
}

resource "aws_route_table_association" "private_rt_1c" {
  route_table_id = aws_route_table.private_rt.id
  subnet_id      = aws_subnet.private_subnet_1c.id
}
```
:::

## ステップ2: セキュリティグループの設定

App RunnerからRDSインスタンスへのアクセスを制御します。
これにより、特定のポート経由でのみデータベースへのアクセスを許可し、セキュリティを確保します。

:::details security_group.tf
```tf
# ---------------------------------------------
# Security Group
# ---------------------------------------------
# API App Runner security group
resource "aws_security_group" "api_app_runner_sg" {
  name        = "${var.project}-${var.environment}-api-app-runner-sg"
  description = "api app runner security group"
  vpc_id      = aws_vpc.vpc.id

  tags = {
    Name    = "${var.project}-${var.environment}-api-app-runner-sg"
    Project = var.project
    Env     = var.environment
  }
}

resource "aws_security_group_rule" "api_app_runner_out_db" {
  security_group_id        = aws_security_group.api_app_runner_sg.id
  type                     = "egress"
  protocol                 = "tcp"
  from_port                = var.db_port
  to_port                  = var.db_port
  source_security_group_id = aws_security_group.db_sg.id
}

# --------------------------------------------------------------------

# DB security group
resource "aws_security_group" "db_sg" {
  name        = "${var.project}-${var.environment}-db-sg"
  description = "db server security group"
  vpc_id      = aws_vpc.vpc.id

  tags = {
    Name    = "${var.project}-${var.environment}-db-sg"
    Project = var.project
    Env     = var.environment
  }
}

resource "aws_security_group_rule" "db_in_api_app_runner" {
  security_group_id        = aws_security_group.db_sg.id
  type                     = "ingress"
  protocol                 = "tcp"
  from_port                = var.db_port
  to_port                  = var.db_port
  source_security_group_id = aws_security_group.api_app_runner_sg.id
}
```
:::

## ステップ3: RDSの構築

RDSを構築します。今回はPostgreSQLのデータベースを使用します。

:::details rds.tf
```tf
# ---------------------------------------------
# RDS parameter group
# ---------------------------------------------
resource "aws_db_parameter_group" "standalone_parametergroup" {
  name   = "${var.project}-${var.environment}-pgql-standalone-parametergroup"
  family = "${var.db_engine}${var.db_version}"

  parameter {
    name  = "client_encoding"
    value = "UTF8"
  }
}

# ---------------------------------------------
# RDS subnet group
# ---------------------------------------------
resource "aws_db_subnet_group" "standalone_subnetgroup" {
  name = "${var.project}-${var.environment}-pgsql-standalone-subnetgroup"
  subnet_ids = [
    aws_subnet.private_subnet_1a.id,
    aws_subnet.private_subnet_1c.id
  ]

  tags = {
    Name    = "${var.project}-${var.environment}-pgsql-standalone-subnetgroup"
    Project = var.project
    Env     = var.environment
  }
}

# ---------------------------------------------
# RDS instance
# ---------------------------------------------
resource "random_string" "db_password" {
  length  = 16
  special = false
}

resource "aws_db_instance" "standalone" {
  engine         = var.db_engine
  engine_version = var.db_full_version

  identifier = "${var.project}-${var.environment}-pgsql-standalone"
  username   = var.db_username
  password   = random_string.db_password.result

  instance_class = var.db_instance_type

  allocated_storage     = 20
  max_allocated_storage = 50
  storage_type          = "gp2"
  storage_encrypted     = true

  multi_az               = false
  availability_zone      = "ap-northeast-1a"
  db_subnet_group_name   = aws_db_subnet_group.standalone_subnetgroup.name
  vpc_security_group_ids = [aws_security_group.db_sg.id]
  publicly_accessible    = false
  port                   = var.db_port

  db_name              = var.db_database
  parameter_group_name = aws_db_parameter_group.standalone_parametergroup.name

  backup_window              = "04:00-05:00"
  backup_retention_period    = 7
  maintenance_window         = "Mon:05:00-Mon:08:00"
  auto_minor_version_upgrade = true

  deletion_protection = false
  skip_final_snapshot = true

  apply_immediately = true

  tags = {
    Name    = "${var.project}-${var.environment}-pgsql-standalone"
    Project = var.project
    Env     = var.environment
  }
}
```
:::

## ステップ4: VPCコネクタの構築

App Runnerサービスがプライベートネットワーク内のRDSインスタンスにアクセスできるように、VPCコネクタを設定します。これにより、アプリケーション層とデータベース層の間の安全な通信が確保されます。

```tf:api_network.tf
# ---------------------------------------------
# VPC connector - App Runner - RDS
# ---------------------------------------------
resource "aws_apprunner_vpc_connector" "api_app_runner_vpc_connector" {
  vpc_connector_name = "${var.project}-${var.environment}-api-app-runner-vpc-connector"
  subnets = [
    aws_subnet.private_subnet_1a.id,
    aws_subnet.private_subnet_1c.id,
  ]
  security_groups = [aws_security_group.api_app_runner_sg.id]
}
```

## ステップ5: App Runnerの変更

App Runnerサービスで以下の設定を変更します。

- LaravelアプリケーションからDBへの接続の環境変数を追加します。
- VPCコネクタを追加し、App RunnerとRDSのVPCネットワークを接続します。

```tf:api_app_runner.tf
# ---------------------------------------------
# API - App Runner
# ---------------------------------------------
resource "aws_apprunner_service" "api_app_runner" {
  # (省略)

 source_configuration {
    # (省略)

    image_repository {
      image_configuration {
        runtime_environment_variables = {
          # (省略)

          DB_CONNECTION = var.db_connection
          DB_HOST       = aws_db_instance.standalone.address
          DB_PORT       = var.db_port
          DB_DATABASE   = var.db_database
          DB_USERNAME   = var.db_username
          DB_PASSWORD   = random_string.db_password.result

          # (省略)
        }
      }
    }
  }

  network_configuration {
    egress_configuration {
      egress_type       = "VPC"
      vpc_connector_arn = aws_apprunner_vpc_connector.api_app_runner_vpc_connector.arn
    }
  }
}
```

## ステップ6: RDS構築後の確認

Amazon RDSの構築が完了した後、Terraformを用いて設定されたRDSインスタンスの確認を行います。RDSのVPCネットワークが正しく設定され、App Runnerとの接続がVPCコネクタを通じて行われているかを確認します。

### RDSの確認

RDSの確認を行います。今回は構築されたことの確認のみとします。

![RDS確認](https://storage.googleapis.com/zenn-user-upload/ba939ffdcfbc-20240217.png)

### App Runnerの確認

App Runnerの確認を行います。
今回は環境変数の設定とネットワークが変更されたことを確認します。

![ネットワーク変更の確認](https://storage.googleapis.com/zenn-user-upload/db5304610b95-20240217.png)

![環境変数の確認](https://storage.googleapis.com/zenn-user-upload/2bfa6c9adf32-20240217.png)

### App RunnerからRDSの接続確認

App RunnerからRDSまで接続できるか確認します。

以下の手順でLaravelの「routes/web.php」にテスト用のルーティングを追加し、App RunnerからRDSデータベースへの接続をテストします。

```php
Route::get('/db-test', function () {
    try {
        \DB::connection()->getPdo();
        return 'データベースに接続成功';
    } catch (\Exception $e) {
        return 'データベース接続失敗: ' . $e->getMessage();
    }
});
```

# さいごに

AWS App Runnerの構築からRDSのセットアップ、GitHub Actionsを用いた自動デプロイまでの一連の流れを構築しました。他にもログによる不具合解析の効率化や自動テストの導入、スケーリング環境の場合と考慮する点を挙げるときりがないですが、自動デプロイの一歩になればと思います。これからも色々な知見を深めていきたいと考えてます。

最後までお読みいただき、ありがとうございました！