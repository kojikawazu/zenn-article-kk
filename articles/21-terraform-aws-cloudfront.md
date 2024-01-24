---
title: "Terraformの道7：CloudFrontを導入"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws", "route53", "acm", "cloudfront"]
published: false
---

# 🎯目的

この記事では、AWSのサービスの一つであるCloudFrontをTerraformを用いて導入する方法について記載しました。CloudFrontはAWSが提供するCDN（コンテンツデリバリネットワーク）サービスであり、ウェブサイトやウェブアプリケーションのパフォーマンス向上とコスト削減に有効です。Terraformを使用することで、インフラの構成をコード化し、より効率的で再現性の高いインフラ構築が可能になります。

# 前回の内容

前回まではAWS上でACMを導入しました。その続きとして、この記事ではCloudFrontを導入します。

https://zenn.dev/kou_kawa/articles/20-terraform-aws-ssl

# AWSアーキテクチャー図

ここには、CloudFrontを含むAWSのアーキテクチャー図を示します。この図は、CloudFrontがどのように他のAWSサービスと連携するかを示しています。

![アーキテクチャー図](https://storage.googleapis.com/zenn-user-upload/c4c223b6c390-20240114.png)

# CloudFrontを使用するために別のリージョンのACM証明書が必要な理由

AWSのサービスは、リージョナルサービスとグローバルサービスという二つのカテゴリに分けられます。EC2、S3、ELBなどのリージョナルサービスは、選択した**特定のAWSリージョン内**でのみ運用されます。これらのサービスは、そのリージョン内でリソースを作成し、管理します。

一方で、**CloudFrontはグローバルサービス**として分類されます。これは、AWSのグローバルインフラストラクチャを通じて世界中で運用されるコンテンツ配信ネットワーク（CDN）です。CloudFrontを使用する際には、SSL/TLS証明書が必要になりますが、これらの証明書はAWS Certificate Manager（ACM）を通じて、特定のリージョン、すなわち「北バージニア」（us-east-1）で発行する必要があります。

この要件は、CloudFrontがグローバルな範囲でコンテンツを提供するため、その設定やリソースがグローバルな視点で一貫性を持つ必要があるためです。北バージニアリージョンで発行されるSSL/TLS証明書を使用することにより、**CloudFrontは世界中のどこからでもセキュアなコンテンツ配信**を実現します。

# プロバイダー追加

まずは、Terraformの設定ファイル（main.tf）にAWSプロバイダーを追加します。以下はそのサンプルコードです。

```tf
# main.tf

provider "aws" {
  alias   = "virginia"
  profile = "terraform"
  region  = "us-east-1"
}
```

この設定により、Terraformは指定されたプロファイルとリージョン（この場合はバージニア州のus-east-1）でAWSリソースを管理します。

# バージニアリージョンの追加

CloudFrontの導入にはSSL/TLS証明書が必要です。ここでは、バージニアリージョン（us-east-1）にAWS Certificate Manager(ACM)を用いてSSL/TLS証明書を作成します。以下はその設定例です。

```tf
# acm.tf

# for virginia region
resource "aws_acm_certificate" "virginia_cert" {
  provider = aws.virginia

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

この設定により、指定したドメイン名にワイルドカードSSL証明書が発行され、Route 53でのDNS検証が行われます。

# CloudFrontの追加

CloudFrontの設定では、キャッシュ配信、オリジンの設定、デフォルトキャッシュ動作の定義が重要です。以下のTerraformコードは、これらの設定を実装する方法を示しています。

```tf
# cloudfront.tf

# ---------------------------------------------
# CloudFront cache distibution
# ---------------------------------------------
resource "aws_cloudfront_distribution" "cf" {
  enabled         = true
  is_ipv6_enabled = true
  comment         = "cache distibution"
  price_class     = "PriceClass_All"

  origin {
    domain_name = aws_route53_record.route53_record.fqdn
    origin_id   = aws_lb.alb.name

    custom_origin_config {
      origin_protocol_policy = "match-viewer"
      origin_ssl_protocols   = ["TLSv1", "TLSv1.1", "TLSv1.2"]
      http_port              = var.http_port
      https_port             = var.https_port
    }
  }

  default_cache_behavior {
    allowed_methods = ["GET", "HEAD"]
    cached_methods  = ["GET", "HEAD"]

    forwarded_values {
      query_string = true
      cookies {
        forward = "all"
      }
    }

    target_origin_id       = aws_lb.alb.name
    viewer_protocol_policy = "redirect-to-https"
    min_ttl                = 0
    default_ttl            = 0
    max_ttl                = 0
  }

  restrictions {
    geo_restriction {
      restriction_type = "none"
    }
  }

  aliases = ["dev.${var.domain}"]

  viewer_certificate {
    acm_certificate_arn      = aws_acm_certificate.virginia_cert.arn
    minimum_protocol_version = "TLSv1.2_2019"
    ssl_support_method       = "sni-only"
  }
}

resource "aws_route53_record" "route53_cloudfront" {
  zone_id = aws_route53_zone.route53_zone.id
  name    = "dev.${var.domain}"
  type    = "A"

  alias {
    name                   = aws_cloudfront_distribution.cf.domain_name
    zone_id                = aws_cloudfront_distribution.cf.hosted_zone_id
    evaluate_target_health = true
  }
}
```

この設定により、CloudFrontディストリビューションが作成され、指定されたオリジンからのコンテンツ配信が可能になります。HTTPSへのリダイレクト、キャッシュの動作設定、ジオリストリクション（地理的制限）などが含まれており、これらによりセキュリティとパフォーマンスの最適化が図られます。

加えて、Route 53を使用してCloudFrontディストリビューションにカスタムドメイン（例えば「dev.example.com」）を設定する方法も含まれています。

# インフラ反映後の確認

Terraformを使用してインフラの変更を適用した後、以下の手順で設定が正しく反映されていることを確認します。

1. CloudFrontディストリビューションの確認： AWS管理コンソールからCloudFrontのディストリビューションが正しく構築されているか確認します。

![CloudFrontの確認](https://storage.googleapis.com/zenn-user-upload/f486161068d6-20240114.png)

2. HTTPS経由でのアクセス確認： HTTPS経由でディストリビューションにアクセスし、正しくNginxのページが表示されるか確認します。

![NGINX確認](https://storage.googleapis.com/zenn-user-upload/9567f706501b-20240114.png)

# さいごに

この記事では、Terraformを使用してAWS CloudFrontを導入する方法について説明しました。CloudFrontを使うことで、コンテンツの配信速度が向上し、ユーザー体験が改善されます。また、Terraformを活用することで、インフラの構築と管理がより効率的かつ再現性のあるものになります。

最後までお読みいただき、ありがとうございました！
