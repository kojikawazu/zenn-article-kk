---
title: "Terraformの道：AWSでインフラをコード化する旅3"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws", "packer"]
published: false
---

# 🎯目的

前回はEC2インスタンスの構築をコード化した。その際、AMIの構築をより効率的に行う方法がないかと考えた。調査の結果、Packerというツールが存在した。今回は、Packerを使ってAMIをテンプレート化する方法を探求する。

# Packerとは

HashiCorpによって開発されたオープンソースのツールで、自動化されたマシンイメージの作成をサポートする。これは特に、クラウドや仮想化された環境でのサーバーイメージの管理や展開において便利。Packerは複数のプラットフォーム上で一貫したイメージを作成するために使用され、異なるクラウドプロバイダーや仮想化技術間での移行や展開を容易にする。

# Packerの環境構築

1. AWS CLIインストール
2. 作業用IAMユーザー作成
3. Git Bashのインストール
4. Packerのインストール
5. git-secretsのインストール

今回は、作業用IAMユーザーの作成とPackerのインストールに焦点を当てる。

## 1-1. 作業用IAMユーザーの作成

AWS CLI管理用とPacker用の2つのIAMユーザーが必要。AWS CLI管理用は既にTerraformの環境構築で作成したため、ここではPacker用のIAMユーザーの作成について説明する。

### 1-1-1. ポリシーの作成

- AWSのダッシュボードを起動
- IAMのダッシュボードへ移動
- ポリシーに移動する
- 「ポリシーの作成」ボタンを押下する。
- 以下3つのポリシーを作成する。 
  - EC2のフルアクセス、全てのリソース
  - IAMのフルアクセス、全てのリソース
  - KMSのフルアクセス、全てのリソース

### 1-1-2. ユーザーの作成

- ユーザーへ移動
- 「ユーザーの作成」ボタンを押下
- 以下を入力し、「次へ」ボタンを押下
  - ユーザー
  - 「AWS マネジメントコンソールへのユーザーアクセスを提供する - オプション」チェック無し
- 「ポリシーを直接アタッチする」を選択し、以下ロールを選択する。
  - 先ほど作成したポリシーを選択する。
- 「ユーザーの作成」ボタンを押下する。
- 作成したユーザーのリンクを押下し、「セキュリティ認証情報」タブを押下する。
- 「アクセスキー」に移動し、「アクセスキーを作成」ボタンを押下する。
- 「コマンドラインインターフェイス (CLI)」を選択し、
  「上記のレコメンデーションを理解し、アクセスキーを作成します。」にチェックを入れる。
  (そのアクセスキーを保管する)
- コマンドプロンプトを起動し、以下コマンドを入力する。
  その後、保管したアクセスキー、シークレットキーを入力する。

```bash
aws configure --profile packer
```

## 1-2. Packerのインストール

Packerのインストールは以下の手順で行う。

- ユーザーのホームディレクトリに.packer\binという名の新しいフォルダを作成する。

- 以下URLからPackerをダウンロードしてくる。
  その後、ダウンロードしたファイルを解凍し、.packer\binフォルダに格納する。

https://developer.hashicorp.com/packer/install?product_intent=packer#Windows

- ホームディレクトリの直下に.bashrcファイルを編集し、以下の内容を追加する。

```bash
export PATH=$PATH:/c/Users/[ユーザー名]/.packer/bin
```

- Bashターミナルを再起動し、Packerが正常に動作するかを確認する。

```bash
packer version
```

# PackerでAMIの環境構築

## 変数定義追加

Packerの設定を開始するには、まず必要な変数を定義する。

```tf
# variables.pkr.hcl

# -----------------------------------
# 変数定義
# -----------------------------------
variable "project" {
  type = string
}

variable "version" {
  type = string
}

variable "region" {
  type = string
}

variable "ami_name" {
  type = string
}

variable "ins_type" {
  type = string
}

variable "ssh_user" {
  type = string
}

variable "vpc_id" {
  type = string
}

variable "subnet_id" {
  type = string
}

variable "security_group_id" {
  type = string
}
```

## 変数の値追加

次に、これらの変数に具体的な値を割り当てる。

```tf
# variables.pkrvars.hcl

project           = ""
version           = ""
region            = ""
ami_name          = ""
ins_type          = ""
ssh_user          = ""
vpc_id            = ""
subnet_id         = ""
security_group_id = ""
```

## AMI構築

以下の設定でAMIを構築する。

```tf
# app-ami.pkr.hcl

# -----------------------------------
# AMI(構築)
# -----------------------------------
source "amazon-ebs" "app-ami" {
  ami_name = "${var.project}-app-ami"
  profile  = "packer"

  instance_type = "${var.ins_type}"
  region        = "${var.region}"
  source_ami    = "${var.ami_name}"
  launch_block_device_mappings {
    device_name = "/dev/xvda"
    volume_size = 8
  }

  ssh_username = "${var.ssh_user}"
  vpc_id       = "${var.vpc_id}"
  subnet_id    = "${var.subnet_id}"
  security_group_ids = [
    "${var.security_group_id}"
  ]
  associate_public_ip_address = true

  tags = {
    Name          = "${var.project}-app-ami",
    SourceAMIID   = "{{ .SourceAMI }}",
    SourceAMIName = "{{ .SourceAMIName }}",
    Version       = "${var.version}"
  }
}

build {
  sources = [
    "source.amazon-ebs.app-ami"
  ]

  provisioner "shell" {
    inline = [
      "sudo yum -y install nginx",
      "sudo systemctl enable nginx",
      "sudo systemctl start nginx"
    ]
  }
}
```

# コードのフォーマット

プロジェクトのコードを整理するために、以下のコマンドを実行して、Packerファイルのフォーマットを整える。

```bash
packer fmt .
```
このコマンドは、一貫したスタイルでコードを整形する。

# AMIテンプレートの反映

最後に、以下のコマンドでAMIをビルドする。

```bash
packer build -var-file=variables.pkrvars.hcl .
```

# AMI構築結果

![AMI](https://storage.googleapis.com/zenn-user-upload/851de7d90d87-20240114.png)

## AMI名の変更

terraform.tfvars ファイルの変数の値をAMIのIDに書き換える。

```tf
# terraform.tfvars

ami_name      = "[amiのIDに書き換えてください]"
```

# インフラ再構築実施

再度インフラを再構築する。

```bash
# コードのフォーマット
terraform fmt
# インフラ環境の変更内容の確認
terraform plan
# インフラ環境の反映
terraform apply -auto-approve
```

# インフラ構築結果

## EC2インスタンス変更確認

無事にAMIの変更可能となった。

![EC2確認](https://storage.googleapis.com/zenn-user-upload/aaec4f1ff949-20240114.png)

## nginx確認

NGINXも無事に表示された。

![NGINX確認](https://storage.googleapis.com/zenn-user-upload/ca28a95ef1b6-20240114.png)

# さいごに

Packerを使用することによって、AMIもコード化することに成功した。この手法により、インフラストラクチャの管理が一層容易になり、再現性と可搬性が向上した。作成されたAMIは、Terraformによってさらに柔軟かつ効率的にデプロイや管理が可能となった。

今後もこのようなツールを活用し、効率的なインフラ運用を目指していきたい。

最後までお読みいただき、ありがとうございました！