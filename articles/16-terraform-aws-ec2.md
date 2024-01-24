---
title: "Terraformの道2：EC2用の環境構築をコード化"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws", "vpc", "ec2", "iam"]
published: true
---

# 🎯目的

前回はTerraformを用いた基本的な環境構築に焦点を当てました。今回は、その知識を活かして、AWS上での実際のインフラ構築を段階的に進めていきます。最初のステップとして、EC2インスタンスを用いた環境の構築をコード化します。

# 前回の内容

前回の内容はこちらとなります。

https://zenn.dev/kou_kawa/articles/15-terraform-aws-first

# AWSアーキテクチャー図

今回のAWSのアーキテクチャー図は以下となります。

![構築](https://storage.googleapis.com/zenn-user-upload/228ddbe517cc-20240114.png)

# 変数定義追加

IPアドレスやポート番号を変数定義します。

```tf
# main.tf

# 変数名：public_1a_address
# 型：string
variable "public_1a_address" {
  type = string
}

# 変数名：http_port
# 型：number
variable "http_port" {
  type = number
}

# 変数名：https_port
# 型：number
variable "https_port" {
  type = number
}

# 変数名：ssh_port
# 型：number
variable "ssh_port" {
  type = number
}
```

```tf
# terraform.tfvars

public_1a_address  = "[パブリック1aのIPアドレス]/24"
http_port          = [HTTPのポート番号]
https_port         = [HTTPSのポート番号]
ssh_port           = [ssh接続のポート番号]
```

# ネットワーク構築

まずはネットワークの構築から行う。以前作成したVPC環境内に構築していきます。

## サブネット

EC2インスタンス用のサブネットを用意する際、パブリックサブネットを選択しました。パブリックサブネットはインターネットに直接アクセスできるため、アプリケーションの外部公開に適しています。以下のコードでその設定を行います。

```tf
# network.tf

# ---------------------------------------------
# VPC
# ---------------------------------------------
# 変更なし

# ---------------------------------------------
# Subnet(パブリック用追加)
# ---------------------------------------------
# public 1a
resource "aws_subnet" "public_subnet_1a" {
  vpc_id                  = aws_vpc.vpc.id
  availability_zone       = "ap-northeast-1a"
  cidr_block              = var.public_1a_address
  map_public_ip_on_launch = true

  tags = {
    Name    = "${var.project}-${var.environment}-public-subnet-1a"
    Project = var.project
    Env     = var.environment
    Type    = "public"
  }
}
```

## ルートテーブル

EC2インスタンス用のルートテーブルを用意します。ルートテーブルはネットワーク内のトラフィックのルーティングを決定します。サブネットごとにルートテーブルを設定することで、ネットワークの柔軟性とセキュリティを向上させることができます。以下のコードでルートテーブルとサブネットの関連付けを行います。

```tf
# network.tf

# ---------------------------------------------
# Route table(パブリック用追加)
# ---------------------------------------------
resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.vpc.id

  tags = {
    Name    = "${var.project}-${var.environment}-public-rt"
    Project = var.project
    Env     = var.environment
    Type    = "public"
  }
}

# ルートテーブルの関連付け
resource "aws_route_table_association" "public_rt_1a" {
  route_table_id = aws_route_table.public_rt.id
  subnet_id      = aws_subnet.public_subnet_1a.id
}
```

## インターネットゲートウェイ

インターネットゲートウェイは、VPC内のリソースがインターネットと通信するためのポイントです。これは、外部からのアクセスと内部からのアクセスの両方に必要となります。以下のコードでインターネットゲートウェイを設定し、ルートテーブルにルートを追加します。

```tf
# network.tf

# ---------------------------------------------
# Internet Gateway(追加)
# ---------------------------------------------
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.vpc.id

  tags = {
    Name    = "${var.project}-${var.environment}-igw"
    Project = var.project
    Env     = var.environment
  }
}

# ルート
resource "aws_route" "public_rt_igw_r" {
  route_table_id         = aws_route_table.public_rt.id
  destination_cidr_block = "0.0.0.0/0"
  gateway_id             = aws_internet_gateway.igw.id
}
```

# ファイアーウォール構築

ファイアーウォールとして、セキュリティグループを設定します。今回は作業用のセキュリティグループを用意し、SSH (22)、HTTP (80)、HTTPS (443) のポートを開放します。重要な注意点として、全てのIPアドレス (0.0.0.0/0) からのアクセスを許可する設定はリスクが伴います。セキュリティの観点から、特定の信頼できるIPアドレスからのみアクセスを許可する設定を推奨します。

```tf
# security_group.tf

# ---------------------------------------------
# Security Group
# ---------------------------------------------
# opmng security group
resource "aws_security_group" "opmng_sg" {
  name        = "${var.project}-${var.environment}-opmng-sg"
  description = "Operation Management Security Group"
  vpc_id      = aws_vpc.vpc.id

  tags = {
    Name    = "${var.project}-${var.environment}-opmng-sg"
    Project = var.project
    Env     = var.environment
  }
}

# インバウンドルール追加(ポート：22)
resource "aws_security_group_rule" "opmng_in_ssh" {
  security_group_id = aws_security_group.opmng_sg.id
  type              = "ingress"
  protocol          = "tcp"
  from_port         = var.ssh_port
  to_port           = var.ssh_port
  cidr_blocks       = ["0.0.0.0/0"]
}

# インバウンドルール追加(ポート：80)
resource "aws_security_group_rule" "opmng_in_http" {
  security_group_id = aws_security_group.opmng_sg.id
  type              = "ingress"
  protocol          = "tcp"
  from_port         = var.http_port
  to_port           = var.http_port
  cidr_blocks       = ["0.0.0.0/0"]
}

# アウトバウンドルール追加(ポート：80)
resource "aws_security_group_rule" "opmng_out_http" {
  security_group_id = aws_security_group.opmng_sg.id
  type              = "egress"
  protocol          = "tcp"
  from_port         = var.http_port
  to_port           = var.http_port
  cidr_blocks       = ["0.0.0.0/0"]
}

# インバウンドルール追加(ポート：443)
resource "aws_security_group_rule" "opmng_in_https" {
  security_group_id = aws_security_group.opmng_sg.id
  type              = "ingress"
  protocol          = "tcp"
  from_port         = var.https_port
  to_port           = var.https_port
  cidr_blocks       = ["0.0.0.0/0"]
}

# アウトバウンドルール追加(ポート：443)
resource "aws_security_group_rule" "opmng_out_https" {
  security_group_id = aws_security_group.opmng_sg.id
  type              = "egress"
  protocol          = "tcp"
  from_port         = var.https_port
  to_port           = var.https_port
  cidr_blocks       = ["0.0.0.0/0"]
}
```

# EC2環境構築

## 変数定義追加

キーペアファイルのパスとAMI名を変数として定義します。変数を使用することで、コードの再利用性が向上し、さまざまな環境での設定変更が容易になります。

```tf
# main.tf

# ---------------------------------------------
# Variables(追加)
# ---------------------------------------------
variable "key_pair_path" {
  type = string
}

variable "ami_name" {
  type = string
}
```

## 変数の値追加

terraform.tfvars ファイルに変数の値を設定します。この設定はTerraformの実行時に利用され、コードのカスタマイズを容易になります。

```tf
# terraform.tfvars

key_pair_path = "[公開鍵ファイルパスに書き換えてください]"
ami_name      = "[amiのIDに書き換えてください]"
```

## AMI追加

AMIをフィルタリングして選択します。most_recent フラグを使用して最新のAMIを選択し、owners オプションで信頼できるソース（自分自身やAmazon）からのAMIのみを対象になります。これにより、安全で最新のAMIを使用できます。

```tf
# data.tf

# ami追加
data "aws_ami" "app" {
  most_recent = true
  owners      = ["self", "amazon"]

  filter {
    name   = "name"
    values = ["${var.ami_name}"]
  }
  filter {
    name   = "root-device-type"
    values = ["ebs"]
  }
  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}
```

## キーペア

AWSキーペアを追加し、EC2インスタンスへの安全なSSHアクセスを可能にします。キーペアはインスタンスのセキュリティとアクセス制御に不可欠となります。

```tf
# app_server.tf

# ---------------------------------------------
# key pair(追加)
# ---------------------------------------------
resource "aws_key_pair" "keypair" {
  key_name   = "${var.project}-${var.environment}-keypair"
  public_key = file("${var.key_pair_path}")

  tags = {
    Name    = "${var.project}-${var.environment}-keypair"
    Project = var.project
    Env     = var.environment
  }
}
```

## EC2のIAMロールの追加

EC2用のIAMロールを設定する際、これはインスタンスのセキュリティとアクセス権限を管理する上で重要です。IAMロールにより、EC2インスタンスは他のAWSサービスに安全にアクセスでき、必要な権限を効率的に管理できます。

```tf
# iam.tf

# ---------------------------------------------
# インスタンスプロフィール
# ---------------------------------------------
resource "aws_iam_instance_profile" "app_ec2_profile" {
  name = aws_iam_role.app_iam_role.name
  role = aws_iam_role.app_iam_role.name
}

# ---------------------------------------------
# IAM role
# ---------------------------------------------
resource "aws_iam_role" "app_iam_role" {
  name               = "${var.project}-${var.environment}-app-iam-role"
  assume_role_policy = data.aws_iam_policy_document.ec2_assume_role.json
}

# ---------------------------------------------
# 信頼ポリシー
# ---------------------------------------------
data "aws_iam_policy_document" "ec2_assume_role" {
  statement {
    actions = ["sts:AssumeRole"]

    principals {
      type        = "Service"
      identifiers = ["ec2.amazonaws.com"]
    }
  }
}

# ---------------------------------------------
# ロールポリシーアタッチ
# ---------------------------------------------
resource "aws_iam_role_policy_attachment" "app_iam_role_ec2_readonly" {
  role       = aws_iam_role.app_iam_role.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess"
}

resource "aws_iam_role_policy_attachment" "app_iam_role_ssm_managed" {
  role       = aws_iam_role.app_iam_role.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"
}

resource "aws_iam_role_policy_attachment" "app_iam_role_ssm_readonly" {
  role       = aws_iam_role.app_iam_role.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonSSMReadOnlyAccess"
}
```

## EC2

最後に、EC2インスタンスを設定します。ここでは、選択したインスタンスタイプ、AMI、セキュリティグループの重要性について説明します。また、user_data を使ってインスタンスの初期設定を自動化する方法についても触れます。

```tf
# app_server.tf

# ---------------------------------------------
# EC2 instance(追加)
# ---------------------------------------------
resource "aws_instance" "app_server" {
  ami                         = data.aws_ami.app.id
  instance_type               = "t2.micro"
  subnet_id                   = aws_subnet.public_subnet_1a.id
  associate_public_ip_address = true

  # セキュリティグループ
  vpc_security_group_ids = [
    aws_security_group.opmng_sg.id
  ]

  # インスタンスプロフィール(IAMロール)
  iam_instance_profile = aws_iam_instance_profile.app_ec2_profile.name

  # キーペア
  key_name = aws_key_pair.keypair.key_name

  # タグ
  tags = {
    Name    = "${var.project}-${var.environment}-app-ec2"
    Project = var.project
    Env     = var.environment
    Type    = "app"
  }

  # EC2インスタンス内構築スクリプト
  user_data = file("./scripts/startup.sh")
}
```

## EC2内構築スクリプト

以下のスクリプトはEC2インスタンスの初期設定を自動化します。システムの全体更新を行い、nginxがインストールされていなければインストールし、その後nginxサービスを起動して自動起動を有効になります。これにより、インスタンスが起動するたびにnginxが稼働するようになります。

```bash
#!/bin/bash

# まずは全体更新
sudo yum -y update

# nginx存在チェック
nginx -v

if [ $? -ne 0 ]; then
  # nginxインストールなしの場合、インストール実施
  sudo yum -y install nginx
fi

# nginxサービスの自動起動有効化、起動
sudo systemctl start nginx
sudo systemctl enable nginx
```

# インフラ構築実施

Terraformを使用してインフラを構築するプロセスは以下の通り。terraform fmt はコードを整形し、terraform plan で変更内容を確認し、terraform apply で実際にインフラに適用します。-auto-approve オプションは確認無しに変更を適用するため、注意して使用してください。

```bash
# コードのフォーマット
terraform fmt
# インフラ環境の変更内容の確認
terraform plan
# インフラ環境の反映
terraform apply -auto-approve
```

# インフラ構築結果

## EC2インスタンス構築確認

AWSコンソールやTerraformの出力を使用して、EC2インスタンスが正しく構築されたことを確認します。ここでは、ネットワーク設定やセキュリティグループの詳細は割愛し、インスタンスの基本的な起動と設定の確認に焦点を当てます。

![EC2確認](https://storage.googleapis.com/zenn-user-upload/edd0b6aeff81-20240113.png)

## ssh接続確認

SSHを使用してEC2インスタンスに接続し、接続が成功したことを確認します。
セキュリティ面を考慮し、割愛した部分もあります。

![ssh接続確認](https://storage.googleapis.com/zenn-user-upload/73ad4a2692a6-20240113.png)

## nginx確認

ブラウザでEC2インスタンスにアクセスし、nginxが起動していることを確認します。また、curl コマンドを使用してコマンドラインからnginxの応答を確認する方法も有効です。

![nginx確認](https://storage.googleapis.com/zenn-user-upload/6d87acbea440-20240113.png)

# さいごに

今回はEC2インスタンスのインフラ構築をTerraformを使用してコード化しました。このアプローチにより、データベースを必要としないアプリケーションの場合、EC2インスタンスへのデプロイと動作確認が容易になります。さらに、今回の構築手順は、他のアプリケーションのインフラ構築にも応用可能となります。Terraformのコードをカスタマイズすることで、さまざまな環境に適したインフラ構築のテンプレートとして使用できます。

最後までお読みいただき、ありがとうございました！
