---
title: "Terraformの道：AWSでインフラをコード化する旅1"
emoji: "✍"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws", "VPC"]
published: true
---

# 🎯目的

AWSとIaC（Infrastructure as Code）の活用を深めるため、Terraformの学習を進めていく。基本から始めて、徐々に複雑なトピックへと進んでいくことを目指す。そして、学習過程を備忘録として記録する。

# Terraformとは

HashiCorpが提供するIaCツールで、Go言語で開発されたオープンソースプロジェクト。

Terraformを使うことで、インフラの構成をコードで宣言し、手動操作なしにインフラ構成を自動管理できる。初期プロビジョニングから更新、破棄まで、すべてのプロセスをコード化して実行可能。Terraformを選んだ主要な理由は、AWSとの強力な互換性と、インフラ構築の自動化におけるその顕著な利点にある。

# まずは環境構築

今回のゴールは、AWSを使用したインフラ構築。ここではWindows環境に焦点を当てて進める。

## 1. AWS CLIをインストール

以下からAWS CLIをインストールする。

https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/getting-started-install.html

インストールが完了したら、コマンドプロンプトを開き、以下のコマンドでインストールの確認を行う。

```bash
aws --version
```

## 2. IAMユーザーを作成(AWS)

IAMユーザーは、AWSリソースへのアクセスを安全に管理するために不可欠。以下の手順に従ってIAMユーザーを作成する。

- AWSのダッシュボードを起動
- IAMのダッシュボードへ移動
- ユーザーへ移動
- 「ユーザーの作成」ボタンを押下
- 以下を入力し、「次へ」ボタンを押下
  - ユーザー
  - 「AWS マネジメントコンソールへのユーザーアクセスを提供する - オプション」チェック無し
- 「ポリシーを直接アタッチする」を選択し、以下ロールを選択する。
  - AdministratorAccess
- 「ユーザーの作成」ボタンを押下する。
- 作成したユーザーのリンクを押下し、「セキュリティ認証情報」タブを押下する。
- 「アクセスキー」に移動し、「アクセスキーを作成」ボタンを押下する。
- 「コマンドラインインターフェイス (CLI)」を選択し、
  「上記のレコメンデーションを理解し、アクセスキーを作成します。」にチェックを入れる。
  (そのアクセスキーを保管する)
- コマンドプロンプトを起動し、以下コマンドを入力する。
  その後、保管したアクセスキー、シークレットキーを入力する。

```bash
aws configure
```

## 3. Terraform用IAMユーザーを作成(AWS)

TerraformがAWSリソースを操作するためには、専用のIAMユーザーが必要。同様の手順でTerraform用のIAMユーザーを作成し、アクセスキーを保管する。このユーザーはTerraform専用のプロファイルとして設定する。

- ユーザーへ移動
- 「ユーザーの作成」ボタンを押下
- 以下を入力し、「次へ」ボタンを押下
  - ユーザー
  - 「AWS マネジメントコンソールへのユーザーアクセスを提供する - オプション」チェック無し
- 「ポリシーを直接アタッチする」を選択し、以下ロールを選択する。
  - AdministratorAccess
- 「ユーザーの作成」ボタンを押下する。
- 作成したユーザーのリンクを押下し、「セキュリティ認証情報」タブを押下する。
- 「アクセスキー」に移動し、「アクセスキーを作成」ボタンを押下する。
- 「コマンドラインインターフェイス (CLI)」を選択し、
  「上記のレコメンデーションを理解し、アクセスキーを作成します。」にチェックを入れる。
  (そのアクセスキーを保管する)
- コマンドプロンプトを起動し、以下コマンドを入力する。
  その後、保管したアクセスキー、シークレットキーを入力する。

```bash
aws configure --profile terraform
```

## 4. 設定の確認

最後に、以下のファイルを確認し、正しく2つのプロファイル（デフォルトとTerraform）が設定されていることを確認する。

```
C:\Users\個人ユーザー\.aws\credentials
```

## 5. Git Bashをインストールする(Windows用)

Git Bashは、Windows環境でUNIX風のコマンドライン操作を可能にするツール。Git Bashを使用すると、WindowsでもLinuxやmacOSで一般的なコマンドライン操作が行えるようになる。インストール手順については、公式サイトを参照してください。

## 6. Terraformをインストールする

Terraformをインストールするには、以下の手順に従う。

- ユーザーのホームディレクトリに.tfenvという名の新しいフォルダを作成する。

- Bashターミナルを開き、tfenv（Terraformのバージョン管理ツール）の環境を以下のコマンドでセットアップする。これにより、異なるバージョンのTerraformを簡単に切り替えられるようになる。

```bash
git clone https://github.com/tfutils/tfenv.git .tfenv
```

- ホームディレクトリの直下に.bashrcファイルを作成（または編集）し、以下の内容を追加する。

```bash
export PATH=$PATH:/c/Users/[ユーザー名]/.tfenv/bin
```

- Bashターミナルを再起動し、tfenvが正常に動作するかを確認する。

```bash
tfenv
```

- 最後に、以下のコマンドを実行してTerraformをインストールする。

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

AWSのアクセスキーやシークレットキーなどの機密情報を誤って公開リポジトリにコミットしないよう、git-secretsを使用する。

### 7-1. git-secretsインストール

PowerShellを開き、以下のコマンドでgit-secretsをインストールする。

```powershell
Set-ExecutionPolicy RemoteSigned
git clone https://github.com/awslabs/git-secrets.git
cd ./git-secrets
./install.ps1
```

### 7-2. git-secrets初期化

git-secretsを初期化して、AWSの鍵を監視する設定を行う。

```powershell
git secrets --register-aws --global
git secrets --install $env:userprofile/.git-templates/git-secrets -f
git config --global init.templatedir $env:userprofile/.git-templates/git-secrets
```

# Terraformプロジェクトの環境構築

## 1. プロジェクトの初期化

まずは、Terraformプロジェクトの基礎を設定する。Bashターミナルを開き、以下のコマンドでTerraformプロジェクトの初期化を行う。

```bash
terraform init
```

## 2. VPCの構築

次に、基本的なネットワーク構造であるVPC（Virtual Private Cloud）をTerraformを用いて構築する。
以下の3つのファイルをプロジェクトに追加すること。

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

プロジェクトのコードを整理するために、以下のコマンドを実行して、Terraformファイルのフォーマットを整える。

```bash
terraform fmt
```
このコマンドは、一貫したスタイルでコードを整形する。

## 4. インフラ環境の変更内容の確認

変更を適用する前に、以下のコマンドでインフラ環境の変更内容を確認する。

```bash
terraform plan
```

このコマンドは、構築されるインフラの概要を表示し、コードに誤りがないかを確認するのに役立つ。

## 5. インフラ環境の反映

最後に、以下のコマンドでインフラ環境の変更を適用する。
-auto-approveオプションは、確認プロンプトをスキップして直接変更を適用する。

```bash
terraform apply -auto-approve
```

# インフラ構築結果

AWSコンソールにサインインし、VPCが正しく作成されたかを確認する。
以下のスクリーンショットは、作成されたVPCの例。

※ IAMユーザーやVPC ID、IPv4 CIDRは割愛する。

![VPC構築](https://storage.googleapis.com/zenn-user-upload/5c2c5f258f59-20240113.png)

# 後片付け

AWSは従量課金制なので、デモやテスト目的のインフラは使用後に破棄することが重要。
以下のコマンドで、Terraformを用いて構築したインフラを破棄できる。
このコマンドは、Terraformによって構築されたリソースを全て削除します。

```bash
terraform destroy -auto-approve
```

# さいごに

このシリーズの第一部として、Terraformを用いた開発環境の構築と、AWS上でのVPCの実装を行った。実際に手を動かしながらインフラを構築することは、知識を深める上で非常に重要となる。しかし、それと同じくらい重要なのが、インフラのコード化。このアプローチにより、インフラ構築のプロセスを標準化し、再利用可能にすることができる。これは、将来的にアプリケーション開発により多くの時間とリソースを割くための基盤を作る。

このシリーズでは今後、Webアプリケーションの構築、ドメインの設定、SSLの導入、オートスケーリングの実装など、さらに進んだトピックに挑戦していく。TerraformとAWSを用いたインフラのコード化に関する知識を深めることで、より効率的で安全なインフラストラクチャーの構築が可能となるようにしたい。
