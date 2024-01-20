---
title: "Terraformの道6：ACLを導入しHTTPS対応"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws"]
published: false
---

# 🎯目的

# ACM(SSL)の追加



# AWSアーキテクチャー図


![アーキテクチャー図](https://storage.googleapis.com/zenn-user-upload/7c729ab78617-20240114.png)

# ACMの追加

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

resource "aws_acm_certificate_validation" "cert_valid" {
  certificate_arn         = aws_acm_certificate.tokyo_cert.arn
  validation_record_fqdns = [for record in aws_route53_record.route53_acm_dns_resolve : record.fqdn]
}
```

# ELBにHTTPS用のリソース追加

```tf
# elb.tf

# ---------------------------------------------
# listener(追加)
# ---------------------------------------------
resource "aws_lb_listener" "aws_listener_https" {
  load_balancer_arn = aws_lb.alb.arn
  port              = 443
  protocol          = "HTTPS"
  ssl_policy        = "ELBSecurityPolicy-2016-08"
  certificate_arn   = aws_acm_certificate.tokyo_cert.arn

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.alb_target_group.arn
  }
}
```

# インフラ反映後の確認

インフラの変更を適用した後は、Terraformのフォーマット、計画、適用の各ステップを実行して、設定が正しく反映されていることを確認する。今回のケースでは、「AWS Certificate Manager (ACM)」が構築されていることを確認する。
初回は証明書が発行され、状況が「検証保留中」となる。

CNAMEレコードを「お名前.com」に必要がある。

![ACM確認](https://storage.googleapis.com/zenn-user-upload/9fd0adcd8c87-20240114.png)

![CNAMEレコード確認後](https://storage.googleapis.com/zenn-user-upload/043f096f8948-20240114.png)

# HTTPSの接続確認

前回ドメイン確認したドメイン名で今回はhttps接続を行い、nginxが表示されるか確認する。

![HTTPS確認](https://storage.googleapis.com/zenn-user-upload/f36185c0dced-20240114.png)

# さいごに