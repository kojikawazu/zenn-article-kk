---
title: "Terraformの道5：Route53でドメイン化"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws"]
published: false
---

# 🎯目的

# Route53の追加

この記事では、AWSのRoute53を使用してドメイン名を設定し、ブラウザからのアクセスを可能にする方法をTerraformを用いて実装します。これにより、ウェブアプリケーションがドメイン名を介して公開され、よりプロフェッショナルでアクセスしやすい形でユーザーに提供されます。

# 前回の内容

前回まではELB(ALB)の追加まで構築しました。その続きから始めます。

https://zenn.dev/kou_kawa/articles/18-terraform-aws-elb

# AWSアーキテクチャー図

以下のアーキテクチャー図は、Route53を導入した後のAWS環境を示しています。この導入により、アプリケーションはカスタムドメイン名でアクセス可能になります。

![アーキテクチャー図](https://storage.googleapis.com/zenn-user-upload/0e83eb475dc7-20240120.png)

# 変数の追加

## 変数定義の追加

Terraformの設定にカスタムドメイン名を組み込むためには、新しい変数の定義が必要です。以下のコードは、main.tf ファイルにドメイン名を保持する変数を追加する方法を示します。この変数は後ほど、Route53のドメイン名設定に使用されます。

```tf
# main.tf

variable "domain" {
  type = string
}
```

## ドメイン名を追加

定義した変数に具体的なドメイン名を割り当てるためには、terraform.tfvars ファイルにドメイン名を記載します。このファイルに記載された値は、Terraformがプロジェクトを実行する際に自動的に読み込まれ、Route53の設定に使用されます。下記の例では、ユーザーが自身のドメイン名を記載する場所を示しています。

```tf
# terraform.tfvars

domain        = [ドメイン名を記載してください]
```

この値を設定することで、TerraformはAWSのRoute53サービスを使用して、指定されたドメイン名をウェブアプリケーションに割り当てることができます。このステップにより、アプリケーションは指定されたカスタムドメイン名を介してアクセス可能になります。

# Route53

RAWSのRoute53を用いて、カスタムドメイン名の設定とDNSレコードの管理を行います。Terraformを使ってこれらの設定を自動化することにより、インフラの構成をより一貫性を持たせ、管理を容易にします。

## Route53ゾーン

Route53ゾーンの作成は、ドメイン名に対するDNSレコード管理の基礎を築きます。以下のコードは、Terraformを使用してRoute53ゾーンを設定する方法を示しています。このゾーンは、指定されたドメイン名のDNSレコードを集中的に管理するための空間として機能します。

```tf
# route53.tf

# ---------------------------------------------
# Route53
# ---------------------------------------------
resource "aws_route53_zone" "route53_zone" {
  name          = var.domain
  force_destroy = false

  tags = {
    Name    = "${var.project}-${var.environment}-domain"
    Project = var.project
    Env     = var.environment
  }
}
```

## Route53レコード

次に、Application Load Balancer（ALB）へのルーティングを指定するAレコードを作成します。このレコードは、カスタムドメイン名をALBにマッピングし、ユーザーがカスタムドメイン名を介してアプリケーションにアクセスできるようにします。

```tf
# route53.tf

resource "aws_route53_record" "route53_record" {
  zone_id = aws_route53_zone.route53_zone.id
  name    = "dev-elb.${var.domain}"
  type    = "A"
  alias {
    name                   = aws_lb.alb.dns_name
    zone_id                = aws_lb.alb.zone_id
    evaluate_target_health = true
  }
}
```

このレコードにより、ドメイン名を使用してALBにアクセスできるようになる。これにより、ユーザーはカスタムドメイン名を介してアプリケーションにアクセスできるようになる。

# インフラ反映後の確認

インフラの変更を適用した後は、Terraformのフォーマット、計画、適用の各ステップを実行して、設定が正しく反映されていることを確認します。特に、Route53の「ホストゾーン」の設定が正しく行われているかを確認することが重要です。

![ホストゾーン確認](https://storage.googleapis.com/zenn-user-upload/3f82a4d3bdd5-20240114.png)

# Route53のネームサーバーを登録

ドメイン名サービスを有効にするためには、Route53で設定したドメインに対してネームサーバーの登録が必要です。

1. ネームサーバーの確認： AWSのRoute53で構築した「NS（ネームサーバー）」レコードの「値/トラフィックのルーティング先」を確認します。
2. ドメイン登録サイトでの設定： 確認したネームサーバーの値を、ドメインを購入したレジストラ（例：「お名前.com」など）に登録します。

# ドメインの確認

ネームサーバーを登録した後、ドメインを通じたアクセスが正しく機能しているかを確認します。

1. Aレコードの確認： Route53のホストゾーンで、設定したゾーンを選択し、Aレコードの「レコード名」をコピーします。
2. ブラウザでのテスト： コピーしたレコード名をブラウザのアドレスバーに貼り付け、nginxのページが表示されることを確認します。これが成功すれば、ドメイン設定は正しく行われています。

![ドメイン確認](https://storage.googleapis.com/zenn-user-upload/4136734776db-20240114.png)

![NGINX確認](https://storage.googleapis.com/zenn-user-upload/0edb0ba9ac79-20240114.png)

# さいごに

この記事を通じて、TerraformとAWS Route53を使用して、カスタムドメインの設定とDNSレコードの管理を行うプロセスを学びました。Route53の設定を通じて、ウェブアプリケーションはカスタムドメインを介してアクセス可能になり、ユーザー体験の向上に寄与します。これらのステップは、AWSとTerraformを用いた効率的でセキュアなインフラ構築の重要な部分です。

最後までお読みいただき、ありがとうございました！
