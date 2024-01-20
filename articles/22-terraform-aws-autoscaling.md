---
title: "Terraformの道8：AutoScalingを導入"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws"]
published: false
---

# 🎯目的

# Auto Scalingの追加

# AWSアーキテクチャー図

![アーキテクチャー図](https://storage.googleapis.com/zenn-user-upload/bd2feebd98c2-20240114.png)


# EC2インスタンスの削除

EC2インスタンスをコメントアウトする。
その影響を受けるELBも修正する。

```tf
# app_server.tf

# ---------------------------------------------
# EC2 instance
# ---------------------------------------------
# 削除
```

```tf

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

# EC2のテンプレート

```tf
# ---------------------------------------------
# launch template
# ---------------------------------------------
resource "aws_launch_template" "app_lt" {
  update_default_version = true

  name = "${var.project}-${var.environment}-app-lt"

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

# Auto Scaling Group

```tf
# ---------------------------------------------
# auto scaling group
# ---------------------------------------------
resource "aws_autoscaling_group" "app_asg" {
  name = "${var.project}-${var.environment}-app-asg"

  max_size         = 1
  min_size         = 1
  desired_capacity = 1

  health_check_grace_period = 300
  health_check_type         = "ELB"

  vpc_zone_identifier = [
    aws_subnet.public_subnet_1a.id,
    aws_subnet.public_subnet_1c.id
  ]

  target_group_arns = [
    aws_lb_target_group.alb_target_group.arn
  ]

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

インフラの変更を適用した後は、Terraformのフォーマット、計画、適用の各ステップを実行して、設定が正しく反映されていることを確認する。今回のケースでは、「起動テンプレート」と「Auto Scaling グループ」を確認する。

![起動テンプレート確認](https://storage.googleapis.com/zenn-user-upload/07b2793ed3d3-20240115.png)

![AutoScaling確認](https://storage.googleapis.com/zenn-user-upload/8f96c0f5a773-20240115.png)

HTTPS確認を再度確認する。無事にnginxが表示されればよしとする。

![NGINX確認](https://storage.googleapis.com/zenn-user-upload/50c6c8cd747c-20240115.png)


# さいごに











