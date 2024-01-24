---
title: "Terraformの道6：ACMを導入しHTTPS対応"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws", "ec2", "vpc", "iam", "route53", "acm"]
published: true
---

# 🎯目的

今回の目的は、AWS Certificate Manager(ACM)を導入して、HTTP接続からより安全なHTTPS接続に移行することになります。HTTPSはセキュリティが強化されており、データの機密性と完全性を保証します。

# 前回の内容

前回まではAmazon Web Services(AWS)上でRoute53を構築しました。その続きとして、この記事ではACMを導入し、HTTPSへの移行を実現します。

https://zenn.dev/kou_kawa/articles/19-terraform-aws-route53

# AWSアーキテクチャー図

![アーキテクチャー図](https://storage.googleapis.com/zenn-user-upload/cc6fc8fa7944-20240122.png)

# ACMの追加

HTTPSへの移行にはSSL/TLS証明書が不可欠です。これを達成するために、まずはACMでSSL/TLS証明書を作成します。

## SSL/TLS証明書を作成

以下のコードは、ワイルドカード証明書をリクエストしています。これにより、指定されたドメインのすべてのサブドメインがカバーされます。

```tf
# acm.tf

# ---------------------------------------------
# Certificate
# ---------------------------------------------
# for tokyo region
resource "aws_acm_certificate" "tokyo_cert" {
  domain_name       = "*.${var.domain}"
  validation_method = "DNS"

  tags = {
    Name    = "${var.project}-${var.environment}-wildcard-sslcert"
    Project = var.project
    Env     = var.environment
  }

  lifecycle {
    create_before_destroy = true
  }

  depends_on = [
    aws_route53_zone.route53_zone
  ]
}
```

## Route53を作成

次に、ACMの証明書のDNS検証に必要なRoute53のDNSレコードを作成します。これは、ACMが発行する証明書が正しくドメインに紐づいていることを保証するために重要です。

```tf
# acm.tf
resource "aws_route53_record" "route53_acm_dns_resolve" {
  for_each = {
    for dvo in aws_acm_certificate.tokyo_cert.domain_validation_options : dvo.domain_name => {
      name   = dvo.resource_record_name
      type   = dvo.resource_record_type
      record = dvo.resource_record_value
    }
  }

  allow_overwrite = true
  zone_id         = aws_route53_zone.route53_zone.id
  name            = each.value.name
  type            = each.value.type
  ttl             = 600
  records         = [each.value.record]
}
```

## SSL/TLS証明書の検証

最後に、ACMで発行されたSSL/TLS証明書の検証を行うためのリソースを作成します。このステップは、証明書が有効であり、適切に構成されていることを保証するために不可欠です。

```tf
# acm.tf

resource "aws_acm_certificate_validation" "cert_valid" {
  certificate_arn         = aws_acm_certificate.tokyo_cert.arn
  validation_record_fqdns = [for record in aws_route53_record.route53_acm_dns_resolve : record.fqdn]
}
```

# ELBにHTTPS用のリソース追加

HTTPS通信をサポートするために、AWS Elastic Load Balancer(ELB)にHTTPSリスナーを追加します。以下のコードは、HTTPSプロトコルを使用するリスナーを定義し、先ほど作成した ACMのSSL/TLS 証明書をELBに関連付けます。

```tf
# elb.tf

# ---------------------------------------------
# listener(追加)
# ---------------------------------------------
resource "aws_lb_listener" "aws_listener_https" {
  load_balancer_arn = aws_lb.alb.arn
  port              = var.https_port
  protocol          = "HTTPS"
  ssl_policy        = "ELBSecurityPolicy-2016-08"
  certificate_arn   = aws_acm_certificate.tokyo_cert.arn

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.alb_target_group.arn
  }
}
```

このリスナーは、HTTPSトラフィックを受け取り、定義されたターゲットグループに転送します。ssl_policyはセキュリティポリシーを指定し、安全な通信を保証します。

# インフラ反映後の確認

インフラの変更を適用した後、Terraformの format、plan、apply の各コマンドを実行して、設定が正しく反映されていることを確認します。今回のケースでは、特にACMの設定が正しく行われているかを確認します。初回は証明書の発行状況が「検証保留中」と表示されます。

また、ACMの証明書のDNS検証には、CNAMEレコードの設定が必要です。このレコードは、ドメインのDNSプロバイダー（例：「お名前.com」）に設定する必要があります。

![ACM確認](https://storage.googleapis.com/zenn-user-upload/9fd0adcd8c87-20240114.png)

DNS設定後、ACMの管理コンソールで「検証済み」と表示されることを確認します。

![CNAMEレコード確認後](https://storage.googleapis.com/zenn-user-upload/043f096f8948-20240114.png)

## HTTPSの接続確認

HTTPS接続が正しく機能しているかを確認するために、前回設定したドメイン名でHTTPS接続を試みます。ブラウザでドメインにアクセスし、安全な接続が行われていること、そして予定通りにNginxのページが表示されることを確認してください。

![HTTPS確認](https://storage.googleapis.com/zenn-user-upload/f36185c0dced-20240114.png)

# さいごに

この記事では、Terraformを使用してAWS上でHTTPS対応のインフラを構築する方法を紹介しました。ACMでSSL/TLS証明書を作成し、Route53でDNS検証を行い、ELBにHTTPSリスナーを追加することで、安全な通信を実現しました。これらのステップを通じて、安全かつ効率的なインフラ管理の基礎を学ぶことができました。

最後までお読みいただき、ありがとうございました！
