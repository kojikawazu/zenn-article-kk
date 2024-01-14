---
title: "Terraformの道5：Route53でドメイン化"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws"]
published: false
---

# 🎯目的

# Route53の追加

この記事では、AWSのRoute53を使用して、ドメイン名を設定し、ブラウザからのアクセスを可能にする方法をTerraformを用いて実装する。これにより、ウェブアプリケーションがドメイン名を介して公開され、よりプロフェッショナルでアクセスしやすい形でユーザーに提供される。

# AWSアーキテクチャー図

以下のアーキテクチャー図は、Route53を導入した後のAWS環境を示している。この導入により、アプリケーションはカスタムドメイン名でアクセス可能になる。

![アーキテクチャー図](https://storage.googleapis.com/zenn-user-upload/a832147268f4-20240114.png)


# 変数の追加

## 変数定義の追加

Terraformの設定にドメイン名を組み込むため、新しい変数の定義を追加する。

```tf
# main.tf

variable "domain" {
  type = string
}
```

この変数は、Route53におけるドメイン名の設定に使用される。

## ドメイン名を追加

定義した変数に具体的なドメイン名を割り当てる。

```
# terraform.tfvars

domain        = [ドメイン名を記載してください]
```

この値は、Route53の設定で使用される。

# Route53

Route53を用いて、ドメイン名の設定とDNSレコードの管理を行う。

## Route53ゾーン

最初に、Route53ゾーンを作成する。

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

このゾーンは、指定したドメイン名のDNSレコードを管理するための基盤を提供する。

## Route53レコード

続いて、ALBへのルーティングを指定するAレコードを作成する。

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

インフラの変更を適用した後は、Terraformのフォーマット、計画、適用の各ステップを実行して、設定が正しく反映されていることを確認する。今回のケースでは、Route53の「ホストゾーン」の設定が正しく行われているかを確認する。

![ホストゾーン確認](https://storage.googleapis.com/zenn-user-upload/3f82a4d3bdd5-20240114.png)

# Route53のネームサーバーを登録

Route53で設定したドメインに対して、実際にドメイン名サービスを有効にするためには、ネームサーバーの登録が必要となる。

1. ネームサーバーの確認：AWSのRoute53で構築した「NS（ネームサーバー）」レコードの「値/トラフィックのルーティング先」を確認しする。
2. ドメイン登録サイトでの設定：確認したネームサーバーの値を、ドメインを購入したレジストラ（例：「お名前.com」など）に登録する。

# ドメインの確認

ネームサーバーを登録した後、ドメインを通じたアクセスが正しく機能しているかを確認する。

1. Aレコードの確認：Route53のホストゾーンで、設定したゾーンを選択し、Aレコードの「レコード名」をコピーする。
2. ブラウザでのテスト：コピーしたレコード名をブラウザのアドレスバーに貼り付け、nginxのページが表示されることを確認する。これが成功すれば、ドメイン設定は正しく行われている。

![ドメイン確認](https://storage.googleapis.com/zenn-user-upload/4136734776db-20240114.png)

![NGINX確認](https://storage.googleapis.com/zenn-user-upload/0edb0ba9ac79-20240114.png)

# さいごに

この記事を通じて、TerraformとAWS Route53を使用して、カスタムドメインの設定とDNSレコードの管理を行うプロセスを学んだ。Route53の設定を通じて、ウェブアプリケーションがカスタムドメインを介してアクセス可能になり、ユーザー体験の向上に寄与した。これらのステップは、AWSとTerraformを用いた効率的でセキュアなインフラ構築の重要な部分となる。

最後までお読みいただき、ありがとうございました！
