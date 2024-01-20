---
title: "Terraformの道9：RDSを導入"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws"]
published: false
---

# 🎯目的

# RDSの追加

# AWSアーキテクチャー図

![アーキテクチャ図](https://storage.googleapis.com/zenn-user-upload/8d01f57c6a64-20240116.png)

```tf
# main.tf

variable "private_1a_address" {
  type = string
}

variable "private_1c_address" {
  type = string
}

variable "db_port" {
  type = number
}

variable "db_engine" {
  type = string
}

variable "db_version" {
  type = string
}

variable "db_full_version" {
  type = string
}

variable "db_username" {
  type = string
}

variable "db_name" {
  type = string
}

variable "db_instance_type" {
  type = string
}
```

```tf
# terraform.tfvars

private_1a_address = "[プライベート1aのIPアドレス]/24"
private_1c_address = "[プライベート1cのIPアドレス]/24"
db_port            = [DB用のポート番号]
db_engine          = "[DBエンジン名]"
db_version         = "[DBバージョン(8.0)]"
db_full_version    = "[DBフルバージョン(8.0.35)]"
db_username        = "[DBユーザー名]"
db_name            = "[DB名]"
db_instance_type   = "[DBインスタンス名]"
```

# ネットワーク追加構築

## サブネット追加

```tf
# network.tf
# ---------------------------------------------
# Subnet(追加)
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
```

```tf
# network.tf

# ---------------------------------------------
# Route table(追加)
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

# ファイアーウォール追加構築

## セキュリティグループ追加構築

```tf
# security_group.tf

# ---------------------------------------------
# Security Group
# ---------------------------------------------
# app security group(追加)
resource "aws_security_group_rule" "app_out_tcp3306" {
  security_group_id        = aws_security_group.app_sg.id
  type                     = "egress"
  protocol                 = "tcp"
  from_port                = var.db_port
  to_port                  = var.db_port
  source_security_group_id = aws_security_group.db_sg.id
}


# DB security group(追加)
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

resource "aws_security_group_rule" "db_in_tcp3306" {
  security_group_id        = aws_security_group.db_sg.id
  type                     = "ingress"
  protocol                 = "tcp"
  from_port                = var.https_port
  to_port                  = var.https_port
  source_security_group_id = aws_security_group.app_sg.id
}
```

# パラメータストア追加


```tf
# parameter_store.tf
# ---------------------------------------------
# SSM Parameter Store
# ---------------------------------------------
resource "aws_ssm_parameter" "host" {
  name  = "/${var.project}/${var.environment}/app/DB_HOST"
  type  = "String"
  value = aws_db_instance.mysql_standalone.address
}

resource "aws_ssm_parameter" "port" {
  name  = "/${var.project}/${var.environment}/app/DB_PORT"
  type  = "String"
  value = aws_db_instance.mysql_standalone.port
}

resource "aws_ssm_parameter" "database" {
  name  = "/${var.project}/${var.environment}/app/DB_DATABASE"
  type  = "String"
  value = aws_db_instance.mysql_standalone.db_name
}

resource "aws_ssm_parameter" "username" {
  name  = "/${var.project}/${var.environment}/app/DB_USERNAME"
  type  = "SecureString"
  value = aws_db_instance.mysql_standalone.username
}

resource "aws_ssm_parameter" "password" {
  name  = "/${var.project}/${var.environment}/app/DB_PASSWORD"
  type  = "SecureString"
  value = aws_db_instance.mysql_standalone.password
}
```

# RDSの追加

## DBパラメーターグループの追加

```tf
# rds.tf

# ---------------------------------------------
# RDS parameter group
# ---------------------------------------------
resource "aws_db_parameter_group" "mysql_standalone_parametergroup" {
  name   = "${var.project}-${var.environment}-mysql-standalone-parametergroup"
  family = "${var.db_engine}${var.db_version}"

  parameter {
    name  = "character_set_database"
    value = "utf8mb4"
  }

  parameter {
    name  = "character_set_server"
    value = "utf8mb4"
  }
}
```

## RDSオプショングループの追加

```tf
# ---------------------------------------------
# RDS option group
# ---------------------------------------------
resource "aws_db_option_group" "mysql_standalone_optiongroup" {
  name                 = "${var.project}-${var.environment}-mysql-standalone-optiongroup"
  engine_name          = var.db_engine
  major_engine_version = var.db_version
}
```

## RDSサブネットグループの追加

```tf
# rds.tf

# ---------------------------------------------
# RDS subnet group
# ---------------------------------------------
resource "aws_db_subnet_group" "mysql_standalone_subnetgroup" {
  name = "${var.project}-${var.environment}-mysql-standalone-subnetgroup"
  subnet_ids = [
    aws_subnet.private_subnet_1a.id,
    aws_subnet.private_subnet_1c.id
  ]

  tags = {
    Name    = "${var.project}-${var.environment}-mysql-standalone-subnetgroup"
    Project = var.project
    Env     = var.environment
  }
}
```

## RDSインスタンスの追加

```tf
# rds.tf

# ---------------------------------------------
# RDS instance
# ---------------------------------------------
resource "random_string" "db_password" {
  length  = 16
  special = false
}

resource "aws_db_instance" "mysql_standalone" {
  engine         = var.db_engine
  engine_version = var.db_full_version

  identifier = "${var.project}-${var.environment}-mysql-standalone"
  username   = var.db_username
  password   = random_string.db_password.result

  instance_class = var.db_instance_type

  allocated_storage     = 20
  max_allocated_storage = 50
  storage_type          = "gp2"
  storage_encrypted     = false

  multi_az               = false
  availability_zone      = "ap-northeast-1a"
  db_subnet_group_name   = aws_db_subnet_group.mysql_standalone_subnetgroup.name
  vpc_security_group_ids = [aws_security_group.db_sg.id]
  publicly_accessible    = false
  port                   = var.db_port

  db_name              = var.db_name
  parameter_group_name = aws_db_parameter_group.mysql_standalone_parametergroup.name
  option_group_name    = aws_db_option_group.mysql_standalone_optiongroup.name

  backup_window              = "04:00-05:00"
  backup_retention_period    = 7
  maintenance_window         = "Mon:05:00-Mon:08:00"
  auto_minor_version_upgrade = false

  deletion_protection = false
  skip_final_snapshot = true

  apply_immediately = true

  tags = {
    Name    = "${var.project}-${var.environment}-mysql-standalone"
    Project = var.project
    Env     = var.environment
  }
}
```

# インフラ反映後の確認

インフラの変更を適用した後は、Terraformのフォーマット、計画、適用の各ステップを実行して、設定が正しく反映されていることを確認する。今回のケースでは、「RDS」の「データベース」を確認する。

![RDS確認](https://storage.googleapis.com/zenn-user-upload/63c2618f8b81-20240116.png)

その後、EC2にssh接続し、mysqlコマンドでRDSのホストにユーザー、パスワードでログインする。ログインが成功し、SQLコマンドが実行できるところまで確認する。

![MYSQLログイン確認](https://storage.googleapis.com/zenn-user-upload/4083ea86d79b-20240116.png)