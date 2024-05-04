---
title: "Grafana,Prometheusを使った監視を学んでみた"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["grafana", "prometheus", "aws", "ec2"]
published: true
---

# はじめに

最近はAWSの資格（SAA）取得に向けて学習を進めていましたが、その合間にシステム監視ツールであるGrafanaとPrometheusの使用方法についても学んでみました。この記事では、その学びの記録として、これらのツールの基本的な概要と特徴を共有します。

# Grafanaとは

[Grafana](https://grafana.com/ja/)とは、さまざまなデータソースからのデータを可視化し、分析するためのオープンソースのプラットフォームです。

主にモニタリングとアラートの為に使用し、システムのメトリクス、ログ、トレースをリアルタイムで視覚的に追跡するのに役立ちます。Grafanaは非常に柔軟性が高く、多様なデータソース（Prometheus, InfluxDB, Elasticsearchなど）に対応しており、ダッシュボードをカスタマイズして、さまざまなチャートやグラフを作成することができます。

このツールは、データの動向を理解しやすくするインタラクティブなダッシュボードを提供することで、IT運用管理者やデータアナリストなどに広く利用されています。また、アラート機能を通じて、システムやアプリケーションの異常を即座に検出し、通知することができるため、システムの健全性を維持する上で重要な役割を果たしています。

# Prometheusとは

[Prometheus](https://prometheus.io/)はオープンソースの監視およびアラートツールで、特に大規模な分散システムの監視に適しています。

SoundCloudによって最初に開発され、現在はCloud Native Computing Foundation（CNCF）によって維持されています。Prometheusは、主に時系列データを収集し、保存することに特化しており、システムやアプリケーションのパフォーマンス監視に非常に有効です。サーバーのCPU使用率、メモリ使用量、ディスクIO、ネットワークトラフィックなどの基本的なシステムメトリクスの監視から、アプリケーション固有のカスタムメトリクスまで幅広くデータを収集し監視することができます。また、KubernetesやDockerなどのコンテナ技術との組み合わせで、ダイナミックなクラウド環境の監視にも非常に効果的です。

Prometheusのエコシステムには様々なexporterがあり、それぞれが特定の種類のメトリクスを収集する役割を果たしています。
今回はその中で2つ例に挙げます。

## Prometheus Node Exporter

Node Exporterは、ハードウェアとOSのメトリクスを収集するためのツールです。これは、LinuxサーバーのCPU使用率、メモリ使用量、ディスク使用状況、ネットワーク統計などのシステムレベルの情報を提供します。Node Exporterを各ノード（サーバー）にインストールすることで、Prometheusサーバーがこれらのメトリクスをスクレイプ（収集）し、時系列データベースに保存することができます。これにより、システムの健康状態をリアルタイムで監視し、パフォーマンスの問題を素早く特定できるようになります。

## Prometheus Blackbox Exporter

Blackbox Exporterは、外部からの視点でシステムの状態をチェックするためのツールです。このエクスポーターは、エンドポイントがアップしているか、または特定のサービス（HTTP、HTTPS、DNS、TCPなど）が正常に動作しているかを確認します。Blackbox Exporterは、定期的に外部のサービスやシステムに対してクエリやチェックを行い、その応答をメトリクスとして収集します。これにより、システムの可用性やレスポンスタイムを測定し、運用監視の観点からシステムのパフォーマンスを評価することができます。

# Grafana、Prometheusと組み合わせると

Grafana、Prometheus、Node Exporter、および Blackbox Exporterを組み合わせると、システムの内部状態と外部アクセシビリティの両方を包括的に監視する強力な監視ソリューションが形成されます。

## 1. 内部メトリクスの監視

Node Exporter を使用すると、各ホストマシンからOSレベルやハードウェアレベルのメトリクスが収集されます。これにはCPUの負荷、メモリ使用量、ディスクI/O、ネットワークの状態などが含まれます。これらのデータはPrometheusサーバによって定期的に収集され、時系列データベースに保存されます。

## 2. 外部サービスの監視

Blackbox Exporter を使用すると、システムやサービスが外部からどのように見えるかを監視できます。たとえば、WebサーバーへのHTTPリクエストの成功率や応答時間を測定し、DNS解決の問題やSMTPサービスのチェックが可能です。これは、サービスがエンドユーザーにとってどれだけ信頼性があるかを測定するのに役立ちます。

## 3. 総合的な監視ダッシュボード

Prometheusはこれらすべてのデータを集約し、Grafanaなどのビジュアリゼーションツールを使用してダッシュボードを通じて視覚的に表示します。管理者はこのダッシュボードを通じて、システムの状態をリアルタイムで一目で把握でき、必要に応じて迅速に対応することが可能です。

# GrafanaとPrometheusのメリット

GrafanaとPrometheusを組み合わせると以下のメリットがあります。

## 1. 開発者と運用者のための柔軟性と強力な機能

- 高度なクエリ言語:

Prometheusのクエリ言語（PromQL）は非常に強力で、複雑な時系列データのクエリや集計が可能です。これにより、詳細なデータ分析や高度なアラート条件の設定が容易になります。

- カスタマイズ可能なダッシュボード:

Grafanaはダッシュボードのカスタマイズが非常に柔軟で、複数のデータソースからのデータを統合して視覚化できます。ユーザーは自分のニーズに合わせてダッシュボードを構築し、重要なメトリクスを効果的に追跡できます。

## 2. オープンソースとコスト効率

- 無償で利用可能:

両ツールはオープンソースであり、ライセンス費用が発生しません。これにより、初期導入コストを抑えつつ、必要に応じてカスタマイズや拡張が可能です。

- アクティブなコミュニティ:

開発者やユーザーの大規模なコミュニティによって支えられているため、問題が発生した際のサポートや、新機能の提案・実装が活発です。

## 3. 柔軟なデータソースの統合

- 多様なデータソースへの対応:

GrafanaはPrometheusのみならず、InfluxDB、Elasticsearch、MySQLなど、多種多様なデータソースと統合できます。これにより、異なるシステムやアプリケーションからのデータを一元管理しやすくなります。

## 4. スケーラビリティと信頼性

- 大規模な監視環境に適応:

Prometheusは時間とともに増大するメトリクス量にも対応できる設計がされており、特にクラウドネイティブ環境（Kubernetesなど）での使用に適しています。

# GrafanaとPrometheusのデメリット

逆にGrafanaとPrometheusにはこれらの課題があります。

## 1. 設定と管理の複雑さ

- 設定の複雑性:

Prometheusの設定、特に大規模な環境や動的なクラウド環境での使用は、多くの初期設定と調整が必要です。Prometheusのアラートルール、ターゲットの管理、サービスディスカバリの設定が複雑になることがあります。

- 維持管理:

データの保持ポリシー、ストレージの管理、パフォーマンスの最適化など、継続的なメンテナンスが必要です。

## 2. スケーリングの課題

- 水平スケーリングの制限:

Prometheusはシングルインスタンスでの使用に最適化されており、大規模なデータを効率的に処理するための水平スケーリングがネイティブにはサポートされていません。大量のデータや高いクエリ負荷を扱う場合、追加の設計考慮やソリューション（たとえばThanosやCortexの使用）が必要です。

## 3. データ保持と長期ストレージ

- 長期データ保持の課題:

Prometheusはデフォルトで短期間のデータ保持に最適化されています。長期間のデータ保持が必要な場合は、追加の設定や外部の長期ストレージソリューションが必要になることがあります。

## 4. 高度なアナリティクスと機械学習のサポート

- 限定的な分析機能:

GrafanaとPrometheusはリアルタイムモニタリングと基本的な分析には優れていますが、より高度なデータ分析や機械学習の統合が直接的にはサポートされていません。このような機能が必要な場合、他のツールやプラットフォームへのデータのエクスポートが必要になります。

## 5. ユーザーインターフェースとユーザーエクスペリエンス

- ユーザーインターフェースの改善の余地:

Grafanaは高度にカスタマイズ可能ですが、そのインターフェースは初心者には直感的ではないかもしれません。また、より複雑なダッシュボードの設計は、一定の学習曲線を伴います。

# まずはEC2インスタンスを用意する

GrafanaとPrometheusをAWSクラウド上で利用するために、まずはEC2インスタンスを2つ用意します。1つは「Grafana用PC」、もう1つは「Node Exporter用PC」とします。具体的なインスタンスの起動手順は割愛します。

https://aws.amazon.com/console/

# Grafanaの構築

## Grafanaのインストール

以下の手順でGrafana用PCにGrafanaをインストールします。最新バージョンの確認は[公式サイト](https://grafana.com/grafana/download?pg=oss-graf&plcmt=hero-btn-1&edition=oss)で行ってください。

```bash
# Grafanaパッケージのインストール
sudo yum install -y https://dl.grafana.com/oss/release/grafana-10.4.2-1.x86_64.rpm

# パッケージがインストールされたか確認する
rpm -qa grafana

# Grafanaサービスの起動
sudo systemctl start grafana-server

# Grafanaサービスの自動起動有効化
sudo systemctl enable grafana-server

# Grafanaの確認
sudo systemctl status grafana-server
```

## Grafanaのインストール後確認

ブラウザを使用してGrafanaが正しく起動しているかを確認します。以下のURLにアクセスします：

「http://[Grafana用PCのIPアドレス]:3000」

![Grafana起動](https://storage.googleapis.com/zenn-user-upload/442dc00b3ce3-20240504.png)

Grafanaの初期ログインページが表示されたら、ユーザー名として「admin」、パスワードも「admin」でログインを試みます。

![Grafanaログイン](https://storage.googleapis.com/zenn-user-upload/364cc5d1e5c7-20240504.png)

ここでGrafanaのダッシュボードにアクセスできれば、インストールが成功しています。

# Prometheusの構築

## Prometheusのインストール

Grafana用PCに次にPrometheusをインストールします。最新のバージョンは[公式サイト](https://prometheus.io/download/)で確認してください。

```bash
# Prometheusのダウンロード
wget https://github.com/prometheus/prometheus/releases/download/v2.45.5/prometheus-2.45.5.linux-amd64.tar.gz

# ファイルの解凍
tar -zxvf prometheus-2.45.5.linux-amd64.tar.gz

# prometheusサービスUnitファイルを作成
# ファイル内容は別途記載してます。
cd prometheus-2.45.5.linux-amd64
sudo vi /etc/systemd/system/prometheus.service

# systemdの再読み込み
sudo systemctl daemon-reload
# Prometheusサービスが反映されたか確認
sudo systemctl status prometheus

# Prometheusサービスの起動
sudo systemctl start prometheus
# Prometheusサービスの自動起動有効化
sudo systemctl enable prometheus
# Prometheusサービスの起動後確認
sudo systemctl status prometheus
```

以下、Prometheusサービス用のUnitファイルの例です。パスは適宜置き換えてください。

```txt:/etc/systemd/system/prometheus.service 
[Unit]
Description=Prometheus Server
Documentation=https://prometheus.io/docs/introduction/overview/
After=network-online.target

[Service]
User=root
Restart=on-failuer
ExecStart=[Prometheus解凍後ディレクトリ]/prometheus --config.file=[Prometheus解凍後ディレクトリ]/prometheus.yml

[Install]
WantedBy=multi-user.target
```

## Prometheusのインストール後確認

インストールが成功したか確認するために、ブラウザを開き次のURLにアクセスします。

「http://[Grafana用PCのIPアドレス]:9090」

![Prometheus確認](https://storage.googleapis.com/zenn-user-upload/e5797251425d-20240504.png)

PrometheusのWeb UIが表示されれば、インストールは成功しています。

# Prometheus Node Exporterの構築

## Prometheus Node Exporterのインストール

Node Exporter用PCにPrometheus Node Exporterをインストールします。最新のバージョンは[公式サイト](https://prometheus.io/download/)で確認してください。

```bash
# Prometheus Node Exporterのダウンロード
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.0/node_exporter-1.8.0.linux-amd64.tar.gz

# Prometheus Node Exporterの解凍
tar -zxvf node_exporter-1.8.0.linux-amd64.tar.gz

# Prometheus Node ExporterサービスUnitファイルの作成
# ファイル内容は別途記載してます。
cd node_exporter-1.8.0.linux-amd64
sudo vi /etc/systemd/system/node_exporter.service

# systemdの再読み込み
sudo systemctl daemon-reload
# Node Exporterサービスが反映されたか確認
sudo systemctl status node_exporter

# Node Exporterサービスの起動
sudo systemctl start node_exporter
# Node Exporterサービスの自動起動有効化
sudo systemctl enable node_exporter
# Node Exporterサービスの起動後確認
sudo systemctl status node_exporter
```

以下、Node Exporterサービス用のUnitファイルの例です。パスは適宜置き換えてください。

```txt:/etc/systemd/system/node_exporter.service 
[Unit]
Description=Prometheus Server
Documentation=https://prometheus.io/docs/introduction/overview/
After=network-online.target

[Service]
User=root
Restart=on-failuer
ExecStart=[Prometheus Node Exporter解凍後ディレクトリ]/node_exporter

[Install]
WantedBy=multi-user.target
```

## Prometheus設定ファイルの編集

次にGrafana用PCでインストールしたPrometheusの設定ファイルprometheus.ymlを編集し、Node Exporterのメトリクスを収集するようにします。編集後、Prometheusサービスを再起動してください。

```yml:prometheus.yml
(省略)
scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
      # 追加(Node Exporter用PCの設定を追加する)
      - targets: ["[Node Exporter用PCのIPアドレス]:9100"]
```

```bash
# Prometheusサービスの再起動
sudo systemctl restart prometheus
```

## Prometheus Node Exporterのインストール後確認

ブラウザを使用してNode Exporterが正しく起動しているか確認します。以下のURLにアクセスします：

「http://[Node Exporter用PCのIPアドレス]:9100」

![Node Exporter確認](https://storage.googleapis.com/zenn-user-upload/e0cf97670885-20240504.png)

Node ExporterのWebページが表示され、メトリクスがリストされていれば、インストールは成功しています。

## Prometheusでの確認

PrometheusのWeb UIでupメトリクスを検索し、Node Exporterのステータスが1（稼働中）であることを確認します。

![Prometheus確認](https://storage.googleapis.com/zenn-user-upload/d48b1b4f9433-20240504.png)

# Prometheus Blackbox Exporterの構築

## Prometheus Blackbox Exporterのインストール

Grafana用PCにPrometheus Blackbox Exporterをインストールします。最新のバージョンは[公式サイト](https://prometheus.io/download/)で確認してください。

```bash
# Prometheus Blackbox Exporterのダウンロード
wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.25.0/blackbox_exporter-0.25.0.linux-amd64.tar.gz

# Prometheus Blackbox Exporterの解凍
tar -zxvf blackbox_exporter-0.25.0.linux-amd64.tar.gz

# モニターファイルを作成
# ファイル内容は別途記載してます。
cd blackbox_exporter-0.25.0.linux-amd64
vi monitor_website.yml

# Blackbox ExporterのUnitファイルの作成
# ファイル内容は別途記載してます。
sudo vi /etc/systemd/system/blackbox_exporter.service

# systemdの再読み込み
sudo systemctl daemon-reload
# Blackbox Exporterサービスが反映されたか確認
sudo systemctl status blackbox_exporter

# Blackbox Exporterサービスの起動
sudo systemctl start blackbox_exporter
# Blackbox Exporterサービスの自動起動有効化
sudo systemctl enable blackbox_exporter
# cサービスの起動後確認
sudo systemctl status blackbox_exporter
```

```yml:モニタリング設定ファイル（monitor_website.yml）
modules:
  http_2xx:
    prober: http
    timeout: 5s
    http:
      valid_http_versions: ["HTTP/1.1", "HTTP/2.0"]
      valid_status_codes: []
      method: GET
      preferred_ip_protocol: "ip4"
```

```txt:/etc/systemd/system/blackbox_exporter.service
[Unit]
Description=Prometheus Server
Documentation=https://prometheus.io/docs/introduction/overview/
After=network-online.target

[Service]
User=root
Restart=on-failuer
ExecStart=[Prometheus Blackbox Exporter解凍後ディレクトリ]/blackbox_exporter --config.file=[Prometheus Blackbox Exporter解凍後ディレクトリ]/monitor_website.yml

[Install]
WantedBy=multi-user.target
```
## Prometheus設定ファイルの編集

Grafana用PCでインストールしたPrometheusのprometheus.ymlを編集して、Blackbox Exporterからのメトリクス収集を設定します。編集後、Prometheusサービスを再起動してください。

```yml:prometheus.yml
# (語尾に追記する)
  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]
    static_configs:
      - targets:
        - https://twitter.com
        - https://www.youtube.com
        - https://www.instagram.com
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: 127.0.0.1:9115
```

```bash
# Prometheusサービスの再起動
sudo systemctl restart prometheus
```

## Prometheus Blackbox Exporterのインストール後確認

ブラウザを使用してBlackbox Exporterが正しく起動しているかを確認します。以下のURLにアクセスします：

「http://[Grafana用PCのIPアドレス]:9115」

![Blackbox Exporter確認](https://storage.googleapis.com/zenn-user-upload/91f06e5b4341-20240504.png)

Blackbox ExporterのWebページが表示されれば、インストールは成功しています。

## Prometheusでの確認

PrometheusのWeb UIでupメトリクスを検索し、Blackbox Exporterのステータスが1（稼働中）であることを確認します。

![Prometheus確認](https://storage.googleapis.com/zenn-user-upload/bf363b3bd068-20240504.png)

# Grafanaでお試し

構築が完了したため、Grafanaを使ってNode Exporterからのデータを可視化しました。特に、サーバーの空き容量を監視し、以下のスクリーンショットはそのデータのダッシュボードです。

![Grafana確認](https://storage.googleapis.com/zenn-user-upload/a284d9fba777-20240504.png)

# さいごに

GrafanaとPrometheusを使用したデータの可視化方法について学びました。これらのオープンソースツールを利用して、簡単に監視システムを構築し、データを視覚化することが可能であることを確認しました。また、Blackbox Exporterも構築済みであり、外部サービスの監視をいつでも開始できる状態です。Dockerの監視など、他の監視可能性についても認識しており、これらの知見を今後さらに深めていきたいと思います。利用可能なビジュアライゼーションオプションが豊富で、まだまだ学ぶべきことが多いと感じています。

最後まで読んでいただき、ありがとうございました！

# URL

- Grafanaダウンロードサイト

https://grafana.com/grafana/download?pg=oss-graf&plcmt=hero-btn-1&edition=oss

- Prometheusダウンロードサイト

https://prometheus.io/download/

- Docker用(おまけ)

https://docs.docker.com/config/daemon/prometheus/


- Prometheus Blackbox ExporterのMonitorファイル参考用(おまけ)

https://github.com/prometheus/blackbox_exporter/blob/master/example.yml