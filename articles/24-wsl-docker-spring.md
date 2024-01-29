---
title: "WSL(Ubuntu)環境上でSpringプロジェクトコンテナを効率的にテンプレート化"
emoji: "🔩"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["wsl", "ubuntu", "linux", "spring", "docker"]
published: false
---

# 始めに

Springプロジェクトの開発において、環境構築からコンテナ化までのプロセスは、しばしば時間と手間がかかります。この一連の流れを効率化するために、Dockerfileを用いてSpringプロジェクトをコンテナ化するテンプレートを作成しました。この記事では、そのプロセスとテンプレートの作成方法を残します。

# フォルダ構成の詳細

私たちのプロジェクトは、Maven, Spring, そしてMyBatisを使用しており、RESTfulなAPIを実装しています。フォルダ構成は以下の通りです：

```
.
├── (Mavan + Spingプロジェクト)
│   ├── src/main
│   │  ├── java/com/example/myapp
│   │  │   ├── controller
│   │  │   │   └── UserController.java
│   │  │   ├── dao
│   │  │   │   └── UserMapper.java
│   │  │   ├── entity
│   │  │   │   └── User.java
│   │  │   ├── servie
│   │  │   │   └── UserService.java
│   │  │   └── MyAppApplication.java
│   │  └── resources
│   │      ├──  mappers
│   │      ├──  └── UserMapper.xml
│   │      ├──  application.properties   # MySQLの設定とDBコンテナの設定を一致させてください
│   │      ├──  schema.sql
│   │      └──  data.sql
│   ├── Dockerfile.dev                   # 開発環境用Dockerfile
│   ├── Dockerfile.prod                  # 実行環境用Dockerfile
│   └── pom.xml
└── cocker-compose.yml                   # このディレクトリでdocker-composeコマンド実行します
```

この構成は、開発から本番環境への移行をスムーズに行うためのものです。

# コンテナ構築前のプロジェクト結果

本格的な実装を始める前にシンプルなユーザーテーブルを作成し、データをJSON形式で返す処理を実装しました。これをコンテナ化後も実行できるようにします。

![リクエスト結果](https://storage.googleapis.com/zenn-user-upload/0a715f3dca58-20240129.png)

# Linux環境のセットアップ

開発環境は、Windows 10上のWSL2にUbuntuをインストールし、Docker Desktop for Windowsと連携して構築します。

- WSLバージョン：2.0.14.0
- Dockerバージョン：4.26.1
- Docker-composeバージョン：2.24.1

## WSL2のインストールと設定

WSL2はWindows上でLinux環境を動作させるための重要なツールです。以下のMicrosoft公式サイトからインストールしてください：

https://learn.microsoft.com/ja-jp/windows/wsl/install

インストール後、PowerShellでバージョンを確認してください。

![WSL確認](https://storage.googleapis.com/zenn-user-upload/9e737e2722e5-20240129.png)

## Ubuntuのインストール

Microsoft Storeから「Ubuntu for Windows」をインストールし、WSL上での動作を確認します：

https://apps.microsoft.com/detail/9PDXGNCFSCZV?hl=ja-jp&gl=JP

Ubuntuが正常にインストールされたことを確認してください。

![Ubuntu確認](https://storage.googleapis.com/zenn-user-upload/e3f198d51246-20240129.png)

## Docker Desktopのセットアップ

次に、Docker Desktop for Windowsをインストールし、WSL2上のUbuntuと連携させます：

https://docs.docker.com/desktop/install/windows-install/

Ubuntu環境でDockerが正常に動作するか確認してください。

![Dockerの同期](https://storage.googleapis.com/zenn-user-upload/38238971fdbd-20240129.png)

![Docker使用確認](https://storage.googleapis.com/zenn-user-upload/a97ab815724c-20240129.png)

このセットアップにより、WSL(Ubuntu)環境でSpringプロジェクトのコンテナを効率的にテンプレート化する基盤が整いました。

# 各実装の詳細

このセクションでは、Docker-composeとDockerfileを用いたコンテナ環境の構築手順を記載します。特に、トラブルが発生しやすいポイントに焦点を当てます。

## docker-composeファイルの解説

docker-compose.ymlファイルは、プロジェクトのコンテナ環境を定義する重要なファイルです。このファイルでは、Springアプリケーションのコンテナ、データベースのコンテナ、その他のサービスのコンテナを設定します。ファイルの内容は以下のGitHubリポジトリで確認できます。

https://github.com/kojikawazu/MavenSpringTemplate/blob/a9f1d0092893c5a59e08f467b0fccfad1380144f/docker-compose.yml

# Dockerfileの詳細

## 実行環境用Dockerfile

実行環境用のDockerfileは、本番環境で使用されるコンテナイメージを構築します。このDockerfileは、アプリケーションが本番環境で正常に動作するように最適化されています。

https://github.com/kojikawazu/MavenSpringTemplate/blob/a9f1d0092893c5a59e08f467b0fccfad1380144f/MavenSpring/Dockerfile.prod

## 開発環境用Dockerfile

開発環境用のDockerfileは、開発中のコンテナイメージを構築するために使用されます。このDockerfileは、開発プロセスをサポートするために必要なツールや設定を含んでいます。

https://github.com/kojikawazu/MavenSpringTemplate/blob/a9f1d0092893c5a59e08f467b0fccfad1380144f/MavenSpring/Dockerfile.dev

# application.propertiesの設定

application.propertiesファイルでは、データベース接続設定などの重要なパラメータを設定します。特に以下のポイントに注意してください：

- spring.datasource.url：MySQLデータベースへの接続URLです。ホスト名はDBコンテナ名に設定してください。
- spring.datasource.usernameとspring.datasource.password：データベースのユーザー名とパスワードを指定します。
- server.port：Springアプリケーションのポート番号を設定します。ホスト側のポート番号と一致させる必要があります。

```file
spring.datasource.url=jdbc:mysql://localhost:xxxx/xxxx
spring.datasource.username=xxxx
spring.datasource.password=xxxx
server.port=xxxx                                  
(省略)
```

# Dockerコンテナの起動

以下のコマンドを使用して、Dockerコンテナをバックグラウンドで起動します。このコマンドは、docker-compose.ymlファイルに基づいて、必要なすべてのコンテナをビルドし、起動します。

```bash
docker-compose up -d --build
```

# 起動結果の確認

## コンテナのビルドと起動

コンテナのビルドと起動の結果は以下の画像で確認できます。

![コンテナ結果](https://storage.googleapis.com/zenn-user-upload/c48a115cf843-20240120.png)

## データベースの状態確認

PHPMyAdminなどのツールを使用して、データベースの状態を確認します。

![PHPMYADMIN画面](https://storage.googleapis.com/zenn-user-upload/5a6d35b96399-20240129.png)

## アプリケーションへのリクエスト実行

アプリケーションが正常に動作しているかを確認するために、HTTPリクエストを実行し、結果を確認します。

![リクエスト結果](https://storage.googleapis.com/zenn-user-upload/37dd3f968572-20240129.png)

# 最後に

この記事を通して、WSL(Ubuntu)環境上でSpringプロジェクトのDockerコンテナをテンプレート化しました。このテンプレートを活用することで、プロジェクトの開始から本番環境への移行までの工程を効率化できます。引き続き、便利な技術やアプローチに取り組んでいきたいと思います。

最後までお読みいただき、ありがとうございました！
