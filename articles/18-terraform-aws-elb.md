---
title: "Terraformの道4：ELBの導入"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws"]
published: true
---

# 🎯目的

この記事では、Terraformを使用してAWS環境にElastic Load Balancer（ELB、特にApplication Load Balancer：ALB）を導入します。ELBは、トラフィックの分散、可用性の向上、およびアプリケーションのスケーラビリティ強化を目的としています。本稿では、既存のアーキテクチャーにELBを統合する際の重要なステップを詳細に解説します。

# 前回の内容

前回の内容はこちらとなります。

https://zenn.dev/kou_kawa/articles/17-terraform-aws-ec2-ami

# ELB(ALB)の追加

以下のアーキテクチャー図は、ELB導入前後のAWS環境を示しています。この導入により、アプリケーションへのトラフィックはELBを介して適切に管理され、異なるアベイラビリティーゾーン(AZ)に分散されます。

# AWSアーキテクチャー図

![アーキテクチャー図](https://storage.googleapis.com/zenn-user-upload/d013b5a2ac8f-20240118.png)

# 変数定義追加

IPアドレスやポート番号を変数定義します。

```tf
# main.tf

# 変数名：public_1c_address
# 型：string
variable "public_1c_address" {
  type = string
}

# 変数名：app_port
# 型：number
variable "app_port" {
  type = number
}
```

```tf
# terraform.tfvars

public_1c_address  = "[パブリック1cのIPアドレス]/24"
app_port           = [アプリケーション用のポート番号]
```

# ネットワーク追加構築

ELBを効果的に導入するためには、ネットワークの構造を調整する必要があります。ここでは、既存のネットワークに新しいパブリックサブネットとルートテーブルを追加します。

## サブネットの追加構築

ELBを使用する場合は、複数のAZが設定された複数のサブネットが必要となります。
その為、パブリックなサブネットを追加します。

```tf
# network.tf

# ---------------------------------------------
# Subnet(追加)
# ---------------------------------------------
resource "aws_subnet" "public_subnet_1c" {
  vpc_id                  = aws_vpc.vpc.id
  availability_zone       = "ap-northeast-1c"
  cidr_block              = var.public_1c_address
  map_public_ip_on_launch = true

  tags = {
    Name    = "${var.project}-${var.environment}-public-subnet-1c"
    Project = var.project
    Env     = var.environment
    Type    = "public"
  }
}
```

このコードでは、ap-northeast-1c ゾーンに新しいパブリックサブネットを作成しています。このサブネットは、ELBによるトラフィックの管理と分散に不可欠です。サブネットは、特定のCIDRブロック（この場合は 変数：public_1c_address）に割り当てられ、外部からのIPアドレス割り当てが可能になります。

## ルートテーブルの追加構築

追加したパブリックサブネットを効果的に使用するために、適切なルートテーブルの設定が必要です。このセクションでは、ルートテーブルを新しいサブネットに関連付けます。

```tf
# network.tf

# ---------------------------------------------
# Route table(追加)
# ---------------------------------------------
resource "aws_route_table_association" "public_rt_1c" {
  route_table_id = aws_route_table.public_rt.id
  subnet_id      = aws_subnet.public_subnet_1c.id
}
```

このコードブロックは、先ほど追加したパブリックサブネット（public_subnet_1c）を既存のパブリックルートテーブル（public_rt）に関連付けています。これにより、ELBからのトラフィックは新しいサブネットを経由して適切にルーティングされます。

# ファイアーウォールの再構築

前回の構築に引き続き、今回はWeb用、APP用、運用管理用の各セキュリティグループの再構築について説明します。これらのグループは、アプリケーションのセキュリティを強化し、特定の通信のみを許可することで、システムの堅牢性を高めます。

## Web用セキュリティグループ

Web用セキュリティグループは、外部からのHTTPおよびHTTPS通信を許可し、内部からは特定のポートを介した通信のみを許可するよう設定されます。これにより、Webサーバーは安全に外部と通信できるようになります。

```tf
# security-group.tf

# ---------------------------------------------
# Security Group
# ---------------------------------------------

# web security group(追加)
resource "aws_security_group" "web_sg" {
  name        = "${var.project}-${var.environment}-web-sg"
  description = "web front role security group"
  vpc_id      = aws_vpc.vpc.id

  tags = {
    Name    = "${var.project}-${var.environment}-web-sg"
    Project = var.project
    Env     = var.environment
  }
}

resource "aws_security_group_rule" "web_in_http" {
  security_group_id = aws_security_group.web_sg.id
  type              = "ingress"
  protocol          = "tcp"
  from_port         = var.http_port
  to_port           = var.http_port
  cidr_blocks       = ["0.0.0.0/0"]
}

resource "aws_security_group_rule" "web_in_https" {
  security_group_id = aws_security_group.web_sg.id
  type              = "ingress"
  protocol          = "tcp"
  from_port         = var.https_port
  to_port           = var.https_port
  cidr_blocks       = ["0.0.0.0/0"]
}

resource "aws_security_group_rule" "web_out_app" {
  security_group_id        = aws_security_group.web_sg.id
  type                     = "egress"
  protocol                 = "tcp"
  from_port                = var.app_port
  to_port                  = var.app_port
  source_security_group_id = aws_security_group.app_sg.id
}
```

この設定では、インバウンドで ポート(変数：http_port) と ポート(変数：https_port) が許可され、アウトバウンドでは ポート(変数：app_port) を介してWebからAppへの通信が可能になります。これにより、Webサーバーは安全にアプリケーションサーバーと通信できます。

## App用セキュリティグループ

App用セキュリティグループは、Webサーバーからの通信のみを許可するように設定されています。これにより、アプリケーションサーバーへの不正なアクセスを防ぎ、システムのセキュリティを強化します。

```tf
# security-group.tf

# app security group(追加)
resource "aws_security_group" "app_sg" {
  name        = "${var.project}-${var.environment}-app-sg"
  description = "application server role security group"
  vpc_id      = aws_vpc.vpc.id

  tags = {
    Name    = "${var.project}-${var.environment}-app-sg"
    Project = var.project
    Env     = var.environment
  }
}

resource "aws_security_group_rule" "app_in_app" {
  security_group_id        = aws_security_group.app_sg.id
  type                     = "ingress"
  protocol                 = "tcp"
  from_port                = var.app_port
  to_port                  = var.app_port
  source_security_group_id = aws_security_group.web_sg.id
}
```

この設定により、ポート(変数：app_port) を介したWebサーバーからのインバウンド通信のみが許可されます。外部からの直接的なアクセスは防がれ、セキュリティが保たれます。

## 運用管理用セキュリティグループ

運用管理用のセキュリティグループは、SSH通信、App用通信、およびHTTP/HTTPS通信を許可するように設定されています。このグループは運用チームがシステムの監視と管理を行うための重要な要素となります。

```tf
# security-group.tf

# opmng security group(変更)
resource "aws_security_group" "opmng_sg" {
  name        = "${var.project}-${var.environment}-opmng-sg"
  description = "operation management role security group"
  vpc_id      = aws_vpc.vpc.id

  tags = {
    Name    = "${var.project}-${var.environment}-opmng-sg"
    Project = var.project
    Env     = var.environment
  }
}

resource "aws_security_group_rule" "opmng_in_ssh" {
  security_group_id = aws_security_group.opmng_sg.id
  type              = "ingress"
  protocol          = "tcp"
  from_port         = var.ssh_port
  to_port           = var.ssh_port
  cidr_blocks       = ["0.0.0.0/0"]
}

resource "aws_security_group_rule" "opmng_in_app" {
  security_group_id = aws_security_group.opmng_sg.id
  type              = "ingress"
  protocol          = "tcp"
  from_port         = var.app_port
  to_port           = var.app_port
  cidr_blocks       = ["0.0.0.0/0"]
}

resource "aws_security_group_rule" "opmng_in_http" {
  security_group_id = aws_security_group.opmng_sg.id
  type              = "ingress"
  protocol          = "tcp"
  from_port         = var.http_port
  to_port           = var.http_port
  cidr_blocks       = ["0.0.0.0/0"]
}

resource "aws_security_group_rule" "opmng_out_http" {
  security_group_id = aws_security_group.opmng_sg.id
  type              = "egress"
  protocol          = "tcp"
  from_port         = var.http_port
  to_port           = var.http_port
  cidr_blocks       = ["0.0.0.0/0"]
}

resource "aws_security_group_rule" "opmng_out_https" {
  security_group_id = aws_security_group.opmng_sg.id
  type              = "egress"
  protocol          = "tcp"
  from_port         = var.https_port
  to_port           = var.https_port
  cidr_blocks       = ["0.0.0.0/0"]
}
```

この設定により、ポート(変数：ssh_port)(SSH)、(変数：app_port)(App用通信)、および(変数：http_port)/(変数：https_port)(HTTP/HTTPS)のインバウンド通信が許可されます。また、アウトバウンドではHTTPおよびHTTPS通信が可能となり、運用管理チームがシステムを適切に監視し、管理するための通信が確保されます。

# ELB(ALB)

このセクションでは、Application Load Balancer（ALB）の構築方法を詳述します。ALBは、ウェブトラフィックの分散や、高可用性とスケーラビリティを実現する重要なコンポーネントとなります。

```tf
# elb.tf

# ---------------------------------------------
# ALB(追加)
# ---------------------------------------------
resource "aws_lb" "alb" {
  name               = "${var.project}-${var.environment}-app-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups = [
    aws_security_group.web_sg.id
  ]
  subnets = [
    aws_subnet.public_subnet_1a.id,
    aws_subnet.public_subnet_1c.id
  ]
}

# ---------------------------------------------
# listener(追加)
# ---------------------------------------------
resource "aws_lb_listener" "aws_listener_http" {
  load_balancer_arn = aws_lb.alb.arn
  port              = var.http_port
  protocol          = "HTTP"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.alb_target_group.arn
  }
}

# ---------------------------------------------
# target group(追加)
# ---------------------------------------------
resource "aws_lb_target_group" "alb_target_group" {
  name     = "${var.project}-${var.environment}-app-tg"
  port     = var.app_port
  protocol = "HTTP"
  vpc_id   = aws_vpc.vpc.id

  tags = {
    Name    = "${var.project}-${var.environment}-app-tg"
    Project = var.project
    Env     = var.environment
  }
}

resource "aws_lb_target_group_attachment" "instance" {
  target_group_arn = aws_lb_target_group.alb_target_group.arn
  target_id        = aws_instance.app_server.id
}
```

このTerraformコードは、ALBを設定し、適切なセキュリティグループとサブネットを関連付けています。また、リスナーとターゲットグループを設定して、トラフィックを適切にルーティングしています。

# EC2内構築スクリプト

次に、EC2インスタンスの起動時に自動実行されるスクリプトを変更します。ELBの構成変更に伴い、nginxの設定も更新する必要があります。

```bash
#!/bin/bash
# startup.sh

# まずは全体更新
sudo yum -y update

# nginx存在チェック
nginx -v

if [ $? -ne 0 ]; then
  # nginxインストールなしの場合、インストール実施
  sudo yum -y install nginx
fi

# ポートの変更(80 → 3000)
sudo sed -i 's/80/3000/g' /etc/nginx/nginx.conf

# nginxサービスの自動起動有効化、起動
sudo systemctl start nginx
sudo systemctl enable nginx
```

このスクリプトでは、nginxのインストールと設定を自動化しています。特に、リスニングポートをデフォルトの80から3000に変更して、ALBの設定と一致させます。
(ポート番号は各設計内容に合わせてください)

# インフラ反映後の確認

インフラの変更を適用した後は、Terraformのフォーマット、計画、適用の各ステップを実行して、設定が正しく反映されていることを確認します。ここでは、特にEC2インスタンスと組み合わせて使用するロードバランサーをチェックします。

![ロードバランサー確認](https://storage.googleapis.com/zenn-user-upload/2f781fe8365f-20240114.png)


「ロードバランサー」を選択すると詳細情報が表示されます。ここで、「DNS 名」に表示されるAレコードをブラウザのアドレスバーに貼り付け、nginxのウェブページが正しく表示されるかを確認します。

![NGINX確認](https://storage.googleapis.com/zenn-user-upload/be1bc93d3b9c-20240114.png)

# さいごに

この記事を通じて、Terraformを使用してAWS環境にELB(ALB)を導入し、関連するネットワークとセキュリティ設定を行う方法を学びました。これらのステップは、可用性が高く、セキュリティが強化されたウェブアプリケーションの構築に不可欠です。このプロセスを通じて、ALBの導入、セキュリティグループの設定、EC2インスタンスの自動化スクリプトの更新、そしてインフラの変更を適用した後の確認方法について詳細に説明しました。

次回の記事では、Route53を使用してドメイン設定を行う手順を取り上げました。これにより、ウェブアプリケーションの設定と管理がさらに強化され、総合的なインフラストラクチャの構築に向けた重要な一歩を踏み出します。

最後までお読みいただき、ありがとうございました！
