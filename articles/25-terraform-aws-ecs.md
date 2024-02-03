---
title: "ECSとコード化を学ぶ"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "aws", "ecs", "vpc", "cloudwatch"]
published: false
---

# 🎯目的

先月はAWSを一回り学び、Terraformを用いたインフラのコード化に挑戦しました。最近はEC2の構築に先立ち、簡易なネットワーク設計をTerraformのコードで実現し、AWSへさくっと構築ができるようになりました。今回は残っている分の中でECSのコンテナ環境について学び、そのプロセスをTerraformで実行する方法について学びます。

# ECSとは？

Amazon Elastic Container Service（ECS）は、AWSが提供するマネージドコンテナオーケストレーションサービスです。Dockerコンテナを簡単に実行、停止、管理することができるプラットフォームを提供します。ECSを使用することで、アプリケーションをコンテナ化し、スケーラブルでセキュアな方法で運用することが可能になります。ECSは、高度なスケーリングやロードバランシング機能を備えており、大規模なアプリケーションの運用にも適しています。

# ECSとEC2の比較

ECSとEC2（Elastic Compute Cloud）は、ともにAWSのコンピューティングサービスですが、提供する機能と用途が異なります。EC2は仮想サーバーの提供に焦点を当てています。ユーザーはEC2インスタンス上でOSを選択し、任意のアプリケーションをインストールして実行できます。一方、ECSはコンテナオーケストレーションに特化しており、アプリケーションをコンテナとしてデプロイし管理することに最適化されています。ECSを使用すると、複数のコンテナを自動的に配置し、スケーリングと管理を容易に行うことができます。

## EC2が適している場合

- カスタマイズ性と制御の必要性が高い場合： EC2は仮想マシンにフルアクセスを提供し、ユーザーが好きなように環境をカスタマイズできます。特定のオペレーティングシステムの設定や、独自のソフトウェアスタックが必要な場合に適しています。
  
- 既存の仮想マシンベースのアプリケーションの移行： 既存のオンプレミス環境からの移行を行う場合、EC2はそのプロセスを簡素化します。アプリケーションがコンテナ化されていない場合、EC2は移行の第一歩として最適な選択肢です。

## ECSが適している場合

- 自動スケーリングと管理の簡易化： ECSはタスクとサービスの自動スケーリングをサポートしており、トラフィックの増減に応じてリソースを自動で調整します。運用の手間を減らし、コスト効率を高めたい場合に適しています。

- DevOpsとCI/CDワークフロー： ECSはコンテナイメージの自動デプロイメントや更新を簡単に行えるため、継続的インテグレーションと継続的デリバリー（CI/CD）のプラクティスに適しています。これにより、開発サイクルを加速し、製品の市場投入時間を短縮できます。

# AWSアーキテクチャー図

今回は1から構築するので、ECSの検証に特化したアーキテクチャにします。

![AWSアーキテクチャ図](https://storage.googleapis.com/zenn-user-upload/3fca0f683024-20240203.png)

# Terraformでコード化

## Terraformの設定や変数定義

Terraformの定義やプロバイダ、変数定義を設定します。

https://github.com/kojikawazu/terraform-aws-practice-sample/blob/90bd99e4dadbfb3e715720f356a125b33c19e002/sample/15-ecs/main.tf

## ネットワーク定義

次にネットワークを構築します。VPC、サブネット、ルートテーブル、インターネットゲートウェイを構築します。

https://github.com/kojikawazu/terraform-aws-practice-sample/blob/90bd99e4dadbfb3e715720f356a125b33c19e002/sample/15-ecs/network.tf

## セキュリティグループ定義

次にセキュリティグループを構築します。今回はインバウンドとアウトバウンドでHTTPおよびHTTPSトラフィックを許可する設定を行います。

https://github.com/kojikawazu/terraform-aws-practice-sample/blob/90bd99e4dadbfb3e715720f356a125b33c19e002/sample/15-ecs/security_group.tf

## ECS用のIAMロール定義

ECS用のIAMロールを構築します。今回はデバッグログ用にCloudWatchを使用した為、IAMロールとポリシーを設定します。

https://github.com/kojikawazu/terraform-aws-practice-sample/blob/90bd99e4dadbfb3e715720f356a125b33c19e002/sample/15-ecs/iam.tf

## デバッグログ用のCloud Watch定義

CloudWatchを利用して、ECSサービスのデバッグログを収集・監視します。バグ解消の手助けになるようにします。

https://github.com/kojikawazu/terraform-aws-practice-sample/blob/90bd99e4dadbfb3e715720f356a125b33c19e002/sample/15-ecs/cloud_watch.tf

## ECS用のELB定義

ECS用のELBを構築します。今回もALBを使用します。ELBのターゲットグループ、リスナー、リスナールールをそれぞれ構築します。

https://github.com/kojikawazu/terraform-aws-practice-sample/blob/90bd99e4dadbfb3e715720f356a125b33c19e002/sample/15-ecs/elb.tf

## ECSの定義

最後にタスク定義を作成し、ECSクラスタとサービスを構築します。今回はECR（Elastic Container Registry）を使用せずにタスク定義を行いましたが、将来的にはECRにあるコンテナイメージを使用する方法にシフトすることを検討しています。

https://github.com/kojikawazu/terraform-aws-practice-sample/blob/90bd99e4dadbfb3e715720f356a125b33c19e002/sample/15-ecs/ecs.tf

# Terraformで構築

コードが揃いましたので、プロジェクトの初期化から反映まで、以下のコマンドを実行します。

```bash
# Terraformプロジェクトの初期化
terraform init

# Terraformのフォーマット
terraform fmt

# Terraformの計画確認
terraform plan

# Terraformの反映
terrafoam apply -auto-approve
```

これにより、定義したリソースがAWS上に構築されます。

# 構築後の確認

構築後の確認を行います。今回はECS周りの確認、ALBのDNS名によるnginxの確認を行います。

## ECSクラスタの確認

クラスタの確認では、クラスタがアクティブ状態であること、およびEC2インスタンスまたはFargateタスクが適切に登録されていることを検証します。

![ECSクラスタ](https://storage.googleapis.com/zenn-user-upload/4d7b19e5a1c4-20240204.png)

## タスク定義の確認

コンテナイメージ、必要なCPUとメモリ、環境変数、ポートマッピングなど、タスク定義の詳細を検証します。

![タスク定義](https://storage.googleapis.com/zenn-user-upload/839c514ecc5b-20240204.png)

## ECSサービスの確認

サービスの確認では、サービスがアクティブであり、設定されたデザイアドカウント（希望するタスク数）に達しているかを確認します。

![ECSサービス](https://storage.googleapis.com/zenn-user-upload/0cf8c55c0a1e-20240204.png)

## ECSタスクの確認

実行中のタスクの確認では、各タスクが正常に起動しているか、コンテナの健全性チェックがパスしているかを検証します。タスクのログを確認し、起動時のエラーまたは実行中の問題がないかを確認します。

![ECSタスク1](https://storage.googleapis.com/zenn-user-upload/b2499b56fc75-20240204.png)

![ECSタスク2](https://storage.googleapis.com/zenn-user-upload/fef22cb2082b-20240204.png)

## nginx起動確認

最終的に、ALBのDNS名を使用してnginxの応答性をテストします。これは、ECSを介してデプロイされたコンテナが外部からアクセス可能であり、正常にサービスを提供しているかを検証するための重要なステップになります。ブラウザやcurlコマンドを使用して、ALB経由でnginxのデフォルトページへのアクセスを試み、成功した応答を確認します。

![nginx確認](https://storage.googleapis.com/zenn-user-upload/864d52bee0f9-20240204.png)

# 最後に

ECSを中心にTerraformを活用したインフラストラクチャのコード化を学びました。EC2上でのDockerコンテナのデプロイメントも有効なですが、ECSの利用は、コンテナ管理とオーケストレーションをより効率的かつスケーラブルに行う方法でもあります。更に、AWS CodePipelineを組み合わせることで、CI/CDパイプラインの構築と、自動ビルドおよび自動デプロイのプロセスを効率化することが可能になります。

更なるスキル向上に励みます。
最後にお読みいただき、ありがとうございました！
