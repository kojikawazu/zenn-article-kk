---
title: "Terraformの道1：AWSでインフラをコード化する旅"
emoji: "✍"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws", "VPC"]
published: true
---

# 🎯目的

AWSとIaC（Infrastructure as Code）の活用を深めるため、Terraformの学習を進めていきます。基本から始めて、徐々に複雑なトピックへと進んでいくことを目指します。そして、学習過程を備忘録として記録します。

# Terraformとは

HashiCorpが提供するIaCツールで、Go言語で開発されたオープンソースプロジェクトになります。

Terraformを使うことで、インフラの構成をコードで宣言し、手動操作なしにインフラ構成を自動管理できます。初期プロビジョニングから更新、破棄まで、すべてのプロセスをコード化して実行可能です。Terraformを選んだ主要な理由は、AWSとの強力な互換性と、インフラ構築の自動化におけるその顕著な利点にあります。

# まずは環境構築

今回のゴールは、AWSを使用したインフラ構築になります。ここではWindows環境に焦点を当てて進めます。

## 1. AWS CLIをインストール

以下からAWS CLIをインストールします。

https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/getting-started-install.html

インストールが完了したら、コマンドプロンプトを開き、以下のコマンドでインストールの確認を行います。

```bash
aws --version
```

## 2. IAMユーザーを作成(AWS)

IAMユーザーは、AWSリソースへのアクセスを安全に管理するために不可欠です。以下の手順に従ってIAMユーザーを作成します。

- AWSのダッシュボードを起動します
- IAMのダッシュボードへ移動します
- ユーザーへ移動します
- 「ユーザーの作成」ボタンを押下します
- 以下を入力し、「次へ」ボタンを押下します
  - ユーザー
  - 「AWS マネジメントコンソールへのユーザーアクセスを提供します - オプション」チェック無し
- 「ポリシーを直接アタッチする」を選択し、以下ロールを選択します
  - AdministratorAccess
- 「ユーザーの作成」ボタンを押下します
- 作成したユーザーのリンクを押下し、「セキュリティ認証情報」タブを押下します
- 「アクセスキー」に移動し、「アクセスキーを作成」ボタンを押下します
- 「コマンドラインインターフェイス (CLI)」を選択し、
  「上記のレコメンデーションを理解し、アクセスキーを作成します。」にチェックを入れます
  (そのアクセスキーを保管します)
- コマンドプロンプトを起動し、以下コマンドを入力します
  その後、保管したアクセスキー、シークレットキーを入力します

```bash
aws configure
```

## 3. Terraform用IAMユーザーを作成(AWS)

TerraformがAWSリソースを操作するためには、専用のIAMユーザーが必要です。同様の手順でTerraform用のIAMユーザーを作成し、アクセスキーを保管します。このユーザーはTerraform専用のプロファイルとして設定します。

- ユーザーへ移動します
- 「ユーザーの作成」ボタンを押下します
- 以下を入力し、「次へ」ボタンを押下します
  - ユーザー
  - 「AWS マネジメントコンソールへのユーザーアクセスを提供します - オプション」チェック無し
- 「ポリシーを直接アタッチする」を選択し、以下ロールを選択します
  - AdministratorAccess
- 「ユーザーの作成」ボタンを押下します
- 作成したユーザーのリンクを押下し、「セキュリティ認証情報」タブを押下します
- 「アクセスキー」に移動し、「アクセスキーを作成」ボタンを押下します
- 「コマンドラインインターフェイス (CLI)」を選択し、
  「上記のレコメンデーションを理解し、アクセスキーを作成します。」にチェックを入れます
  (そのアクセスキーを保管します)
- コマンドプロンプトを起動し、以下コマンドを入力します
  その後、保管したアクセスキー、シークレットキーを入力します

```bash
aws configure --profile terraform
```

## 4. 設定の確認

最後に、以下のファイルを確認し、正しく2つのプロファイル（デフォルトとTerraform）が設定されていることを確認します

```
C:\Users\個人ユーザー\.aws\credentials
```

## 5. Git Bashをインストールする(Windows用)

Git Bashは、Windows環境でUNIX風のコマンドライン操作を可能にするツールです。Git Bashを使用すると、WindowsでもLinuxやmacOSで一般的なコマンドライン操作が行えるようになります。インストール手順については、公式サイトを参照してください。

## 6. Terraformをインストールする

Terraformをインストールするには、以下の手順に従います。

- ユーザーのホームディレクトリに.tfenvという名の新しいフォルダを作成します。

- Bashターミナルを開き、tfenv（Terraformのバージョン管理ツール）の環境を以下のコマンドでセットアップします。これにより、異なるバージョンのTerraformを簡単に切り替えられるようになります。

```bash
git clone https://github.com/tfutils/tfenv.git .tfenv
```

- ホームディレクトリの直下に.bashrcファイルを作成（または編集）し、以下の内容を追加します。

```bash
export PATH=$PATH:/c/Users/[ユーザー名]/.tfenv/bin
```

- Bashターミナルを再起動し、tfenvが正常に動作するかを確認します。

```bash
tfenv
```

- 最後に、以下のコマンドを実行してTerraformをインストールします。

```bash
# インストール可能なバージョンを表示する
tfenv list-remote
# Terraformのインストール(仮にv1.6.6をインストールする)
tfenv install 1.6.6
# Terraformのバージョンを指定する
tfenv use 1.6.6
# Terraformのバージョンを確認する
terraform version
tfenv list
```

## 7. git-secretsインストール

AWSのアクセスキーやシークレットキーなどの機密情報を誤って公開リポジトリにコミットしないよう、git-secretsを使用します。

### 7-1. git-secretsインストール

PowerShellを開き、以下のコマンドでgit-secretsをインストールします。

```powershell
Set-ExecutionPolicy RemoteSigned
git clone https://github.com/awslabs/git-secrets.git
cd ./git-secrets
./install.ps1
```

### 7-2. git-secrets初期化

git-secretsを初期化して、AWSの鍵を監視する設定を行います。

```powershell
git secrets --register-aws --global
git secrets --install $env:userprofile/.git-templates/git-secrets -f
git config --global init.templatedir $env:userprofile/.git-templates/git-secrets
```

# Terraformプロジェクトの環境構築

## 1. プロジェクトの初期化

まずは、Terraformプロジェクトの基礎を設定します。Bashターミナルを開き、以下のコマンドでTerraformプロジェクトの初期化を行います。

```bash
terraform init
```

## 2. VPCの構築

次に、基本的なネットワーク構造であるVPC（Virtual Private Cloud）をTerraformを用いて構築します。
以下の3つのファイルをプロジェクトに追加します。

main.tf: Terraformの設定とAWSプロバイダーの定義。
terraform.tfvars: プロジェクト名や環境名などの変数の定義。
network.tf: VPCのリソース定義。

```tf
# main.tf
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
# 変数名：project
# 型：string
variable "project" {
  type = string
}

# 変数名：environment
# 型：string
variable "environment" {
  type = string
}

# 変数名：vpc_address
# 型：string
variable "vpc_address" {
  type = string
}
```

```tf
# terraform.tfvars
# 変数定義
project     = "sample-project"
environment = "dev"
vpc_address = "[IPアドレス]/20"
```

```tf
# network.tf
# ---------------------------------------------
# VPC
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
```

## 3. コードのフォーマット

プロジェクトのコードを整理するために、以下のコマンドを実行して、Terraformファイルのフォーマットを整えます。

```bash
terraform fmt
```
このコマンドは、一貫したスタイルでコードを整形します。

## 4. インフラ環境の変更内容の確認

変更を適用する前に、以下のコマンドでインフラ環境の変更内容を確認します。

```bash
terraform plan
```

このコマンドは、構築されるインフラの概要を表示し、コードに誤りがないかを確認するのに役立ちます。

## 5. インフラ環境の反映

最後に、以下のコマンドでインフラ環境の変更を適用します。
-auto-approveオプションは、確認プロンプトをスキップして直接変更を適用します。

```bash
terraform apply -auto-approve
```

# インフラ構築結果

AWSコンソールにサインインし、VPCが正しく作成されたかを確認します。
以下のスクリーンショットは、作成されたVPCの例です。

※ IAMユーザーやVPC ID、IPv4 CIDRは割愛します。

![VPC構築](https://storage.googleapis.com/zenn-user-upload/5c2c5f258f59-20240113.png)

# 後片付け

AWSは従量課金制なので、デモやテスト目的のインフラは使用後に破棄することが重要です。
以下のコマンドで、Terraformを用いて構築したインフラを破棄できます。
このコマンドは、Terraformによって構築されたリソースを全て削除します。

```bash
terraform destroy -auto-approve
```

# さいごに

このシリーズの第一部として、Terraformを用いた開発環境の構築と、AWS上でのVPCの実装を行いました。実際に手を動かしながらインフラを構築することは、知識を深める上で非常に重要となります。しかし、それと同じくらい重要なのが、インフラのコード化。このアプローチにより、インフラ構築のプロセスを標準化し、再利用可能にすることができます。これは、将来的にアプリケーション開発により多くの時間とリソースを割くための基盤を作ります。

このシリーズでは今後、Webアプリケーションの構築、ドメインの設定、SSLの導入、オートスケーリングの実装など、さらに進んだトピックに挑戦していきます。TerraformとAWSを用いたインフラのコード化に関する知識を深めることで、より効率的で安全なインフラストラクチャーの構築が可能となるようにしたい。

最後までお読みいただき、ありがとうございました！