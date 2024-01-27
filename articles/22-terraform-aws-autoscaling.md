---
title: "Auto Scaling Groupを導入し、EC2インスタンスをスケーリングしてみる"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws", "route53", "acm", "autoscaling"]
published: false
---

# 🎯目的

この記事では、AWS上でTerraformを使用してAuto Scalingを設定し、負荷分散と可用性の向上を目指します。特に、前回のCloudFrontの導入に続いて、アプリケーションのスケーラビリティと耐障害性をさらに強化する方法に焦点を当てます。

# 前回の内容

前回はCloudFrontを導入し、より高速で安定したコンテンツ配信を実現しました。この記事では、その構成にAuto Scalingを追加し、アプリケーションのスケーラビリティと耐障害性を向上させます。

https://zenn.dev/kou_kawa/articles/21-terraform-aws-cloudfront

# AWSアーキテクチャー図

以下の図は、Auto Scalingを組み込んだ新しいAWSアーキテクチャーを示しています。Auto Scalingの導入により、システムの全体的な耐障害性と拡張性がどのように向上するかが分かります。

![アーキテクチャー図](https://storage.googleapis.com/zenn-user-upload/bd2feebd98c2-20240114.png)

# EC2をテンプレート化する

Auto Scalingを効率的に実現するために、EC2インスタンスをテンプレート化します。これにより、インスタンスの生成と管理が自動化され、スケーリングプロセスが簡素化されます。

## EC2インスタンスの削除

Auto Scalingへの移行に先立ち、既存のEC2インスタンスをコメントアウトします。この手順により、既存のインスタンスがAuto Scalingの影響を受けないように保護されます。

```tf
# app_server.tf

# ---------------------------------------------
# EC2 instance
# ---------------------------------------------
# 削除
```

```tf
# ---------------------------------------------
# target group
# ---------------------------------------------
# 以下コメントアウト

# resource "aws_lb_target_group_attachment" "instance" {
#   target_group_arn = aws_lb_target_group.alb_target_group.arn
#   target_id        = aws_instance.app_server.id
# }
```

## EC2のテンプレート追加

次に、Auto Scalingグループによって使用されるEC2インスタンスの新しいテンプレートを作成します。このテンプレートを通じて、必要に応じてインスタンスが自動的に起動されます。

```tf
# ---------------------------------------------
# launch template
# ---------------------------------------------
resource "aws_launch_template" "app_lt" {
  name = "${var.project}-${var.environment}-app-lt"
  update_default_version = true

  image_id = data.aws_ami.app.id
  key_name = aws_key_pair.keypair.key_name

  tag_specifications {
    resource_type = "instance"

    tags = {
      Name    = "${var.project}-${var.environment}-app-ec2"
      Project = var.project
      Env     = var.environment
      Type    = "app"
    }
  }

  network_interfaces {
    associate_public_ip_address = true
    security_groups = [
      aws_security_group.app_sg.id,
      aws_security_group.opmng_sg.id
    ]

    delete_on_termination = true
  }

  iam_instance_profile {
    name = aws_iam_instance_profile.app_ec2_profile.name
  }

  user_data = filebase64("./scripts/startup.sh")
}
```

# Auto Scaling Groupの追加

Auto Scaling Groupを設定することで、アプリケーションのトラフィックに応じてEC2インスタンスの数を自動で調整します。これにより、高負荷時にはリソースを増やし、低負荷時には減らすことができます。以下のコードは、Auto Scaling Groupの基本設定を示しています。

```tf
# ---------------------------------------------
# auto scaling group
# ---------------------------------------------
resource "aws_autoscaling_group" "app_asg" {
  name = "${var.project}-${var.environment}-app-asg"

  # EC2をスケーリングする数を指定します
  max_size         = 2
  min_size         = 2
  desired_capacity = 2

  # ヘルスチェック
  health_check_grace_period = 300
  health_check_type         = "ELB"

  # VPC指定
  vpc_zone_identifier = [
    aws_subnet.public_subnet_1a.id,
    aws_subnet.public_subnet_1c.id
  ]

  # ターゲットグループ指定
  target_group_arns = [
    aws_lb_target_group.alb_target_group.arn
  ]

　# 起動テンプレートとインスタンスタイプの指定
  mixed_instances_policy {
    launch_template {
      launch_template_specification {
        launch_template_id = aws_launch_template.app_lt.id
        version            = "$Latest"
      }
      override {
        instance_type = "t2.micro"
      }
    }
  }
}
```

# インフラ反映後の確認

ンフラの変更を適用した後、以下の手順で設定が正しく反映されているかを確認します。

1. Terraformのフォーマット、計画、適用のコマンドを実行します。
2. AWSコンソールで「起動テンプレート」と「Auto Scaling グループ」を確認し、「インスタンス」画面で指定した数でインスタンスが起動されていることを確認します。

![起動テンプレート確認](https://storage.googleapis.com/zenn-user-upload/07b2793ed3d3-20240115.png)

![AutoScaling確認](https://storage.googleapis.com/zenn-user-upload/8f96c0f5a773-20240115.png)

![EC2インスタンス](https://storage.googleapis.com/zenn-user-upload/791a89e4c814-20240127.png)

3. 各インスタンスの外部IPアドレスでアプリケーション（例: nginx）を確認します。ポートは3000にしてください。
4. 最後にHTTPSを介してアプリケーションへのアクセスを確認します。

![NGINX確認](https://storage.googleapis.com/zenn-user-upload/6d5d48ae24c9-20240127.png)

# さいごに

この記事を通じて、Terraformを使用してAWSのAuto Scalingを設定し、インフラの変更が正しく適用されたことを確認する方法を学びました。Auto Scalingを導入することで、アプリケーションの負荷に応じた柔軟なスケーリングが可能となり、より高い可用性とコスト効率を実現できます。

最後までお読みいただき、ありがとうございました！









