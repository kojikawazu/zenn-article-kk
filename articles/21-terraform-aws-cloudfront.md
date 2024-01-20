---
title: "Terraformの道7：CloudFrontを導入"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws"]
published: false
---

# 🎯目的

# CloudFrontの追加

# AWSアーキテクチャー図

![アーキテクチャー図](https://storage.googleapis.com/zenn-user-upload/c4c223b6c390-20240114.png)

# プロバイダー追加

```tf
# main.tf

provider "aws" {
  alias   = "virginia"
  profile = "terraform"
  region  = "us-east-1"
}
```

# バージニアリージョンの追加

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

# CloudFrontの追加

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
      http_port              = 80
      https_port             = 443
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

# インフラ反映後の確認

インフラの変更を適用した後は、Terraformのフォーマット、計画、適用の各ステップを実行して、設定が正しく反映されていることを確認する。今回のケースでは、「CloudFront」の「ディストリビューション」が構築されていることを確認する。

![CloudFrontの確認](https://storage.googleapis.com/zenn-user-upload/f486161068d6-20240114.png)

HTTPS確認を再度確認する。無事にnginxが表示されればよしとする。

![NGINX確認](https://storage.googleapis.com/zenn-user-upload/9567f706501b-20240114.png)

# さいごに