---
title: "Ansible"
emoji: "🤝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["ansible", "aws"]
published: false
---

# Ansibleとは

Ansibleは、システムやアプリケーションの構成管理、自動デプロイメント、そしてタスクの自動実行を容易にするオープンソースツールです。Pythonで開発され、SSHを介してリモートマシンに接続し、YAMLフォーマットのPlaybookを用いて操作を指定します。これにより、ITインフラのセットアップ、ソフトウェアのデプロイ、タスク自動化などが実現可能になります。

エージェントレスな設計を採用しているAnsibleは、管理対象のサーバーに追加ソフトウェアのインストールを要求しません。セットアップの簡易さと低侵襲性、そして冪等性のサポートにより、再現可能で予測可能な環境管理が可能となります。

# Ansibleの特徴

- **エージェントレスアーキテクチャ**： 管理対象ノードへのエージェントインストール不要。SSHやWinRMを介して遠隔操作を実行。

- **冪等性のサポート**： 一度適用された変更は重複して適用されないため、一貫した状態を維持。

- **簡潔なYAML構文**： Playbookを通じてインフラの状態を直感的かつ容易に定義。

- **豊富なモジュールライブラリ**： 多彩なシステムやサービスをカバーする組み込みモジュールの提供。

- **カスタマイズ性と拡張性**： カスタムモジュール作成や既存モジュールの拡張が可能。

# Ansibleのメリット/デメリット

## メリット

- **低い導入障壁**: 特別なエージェントソフトウェア不要で、セットアップが容易。

- **直感的なPlaybook構文**: 構成が理解しやすく、チーム内での共有がスムーズ。

- **安定した環境管理**: 冪等性のサポートによる予期せぬ動作の低減。

- **幅広い対応範囲**: 豊富なモジュールによる様々なタスクの自動化。

- **柔軟な適用範囲**: 小規模から大規模環境まで幅広く対応。

## デメリット

- **パフォーマンスの課題**: 大規模環境ではSSH接続のオーバーヘッドが問題に。

- **複雑性への対応**: 高度なロジック実装時のPlaybookの複雑化。

- **限定されたエラーハンドリング**: 複雑なエラー処理が追加工夫を要求。

- **実行速度の問題**: 大規模デプロイや複雑タスクでは実行速度が低下。

# Ansibleの適用シナリオ

## Ansibleを適用する場面

- **アプリケーションデプロイメント**: 複数環境での一貫したデプロイが求められる場合。

- **構成管理**: サーバ設定やソフトウェア更新の自動化を求める場合。

- **オンプレミスとクラウドのハイブリッド環境**: オンプレミスとクラウドの組み合わせ管理に。

## Ansibleを使わない場合

- **コンテナデプロイメント**: KubernetesやDocker Swarmが適しています。

- **インフラストラクチャのプロビジョニング**: Terraformがクラウド環境での選択肢。

- **イメージベースのデプロイメント**:  PackerやDockerを使用。

- **高度なCI/CDパイプライン**: Jenkins, GitLab CI, CircleCIなどの専用ツールを使用。

# AWSアーキテクチャー図

AWS上に下記アーキテクチャーを構築します。
Ansible PCは、AWSの各種サービスとの連携を担い、アプリケーションおよびデータベースサーバーの設定や管理を自動化する役割を果たします。構築方法は今回割愛します。

![アーキテクチャー図](https://storage.googleapis.com/zenn-user-upload/ae3c96f27d82-20240317.png)

- **Ansible PC**: Ansibleがインストールされ、AWSリソースの設定や管理を行う中心的なマシンです。AnsibleのPlaybookを実行して、他のAWSリソースに対する操作を自動化します。

- **アプリケーションサーバー**: publicサブネットに配置された2台のEC2インスタンス。これらはWebアプリケーションをホストし、インターネットからのアクセスを受け入れます。

- **データベースサーバー**: privateサブネットに配置された2台のEC2インスタンス。これらはデータベースをホストし、アプリケーションサーバーからのみアクセス可能です。

- **NATゲートウェイ**: プライベートサブネットにあるリソースがインターネットにアクセスするために利用します。直接インターネットからアクセスされることはありませんが、外部リソースからの更新やパッケージのダウンロードなどを可能にします。

- **セキュリティグループ**: ネットワークアクセスコントロールを提供し、指定されたポート（SSH (22), HTTP (80), HTTPS (443)）に対するインバウンドとアウトバウンドの通信を許可します。

- **ネットワーク構成**:
  - publicサブネット: アプリケーションサーバーが配置され、インターネットからの直接アクセスが可能です。
  - privateサブネット: データベースサーバーが配置され、インターネットからは直接アクセスできません。アプリケーションサーバーからの内部通信のみ許可されます。

- **セキュリティ**:
  - セキュリティグループ: 今回は試験的に1つのセキュリティグループを使用し、必要なポートのみを開放します。これにより、必要最低限のセキュリティを確保しつつ、環境のシンプルさを保持します。

※ 今回はssh接続用の公開鍵/秘密鍵は1つのみ使用します。セキュリティ面を考慮すると、各PC台数分のカギを用意することが望ましいですが、今回はお試しの為、鍵は1つのみとします。

# AnsiblePCのセットアップ方法

Ansibleを活用するためには、Ansibleを実行するマシン（AnsiblePC）の準備です。
以下に、AnsiblePCでSSHの秘密鍵を設定し、Ansibleをインストールする手順を示します。

## SSHの秘密鍵をAnsiblePCに設定

AnsiblePCから管理対象のノードへSSHアクセスするためには、適切な秘密鍵がAnsiblePCに設定されている必要があります。これを行う手順は以下の通りです。

```bash:AnsiblePCで実行
# 秘密鍵を配置する
sudo mv ansible-key.pem ~/.ssh/.
sudo chmod 400 ~/.ssh/ansible-key.pem
# sshエージェントを起動する
ssh-agent bash
# sshエージェントに秘密鍵を登録する
ssh-add ~/.ssh/ansible-key.pem
```

このプロセスにより、AnsiblePCは管理対象のノードに安全にアクセスできるようになります。

## Ansibleをインストール

AnsiblePCにAnsibleをインストールするには、以下のコマンドを実行します。この例では、YUMパッケージマネージャーを使用しています（CentOSやRed Hat Enterprise Linuxなどのディストリビューションで使われています）。

```bash
# Ansibleのインストール
sudo yum install -y ansible
# Ansibleのバージョン確認
ansible --version
```

Ansibleのインストールが完了すれば、そのバージョンを確認して、正しくインストールされたことを確認します。これにより、AnsiblePCは管理対象のノードに対して様々な自動化タスクを実行する準備が整います。

# Playbookファイルの用意

Ansibleを利用してサーバーを構成するためには、Playbookファイルが必要です。
以下は、アプリケーションサーバーとデータベースサーバーの設定を自動化するためのPlaybook例です。

## アプリケーションサーバーのPlaybook

以下の内容でroles/app_server_role/tasks/main.yamlファイルをAnsiblePCに配置します。
これにより、nginxのインストール、設定ファイルのコピー、nginxサービスの開始・有効化を行います。

```yaml:roles/app_server_role/tasks/main.yaml
- name: Install the latest version of nginx
  yum:
    name: nginx
    state: latest

- name: Ansible copy dile to remote server
  copy:
    src: "/home/ec2-user/projects/index.html"
    dest: "{{ html_path }}"

- name: Start nginx service
  service:
    name: nginx
    state: started
    
- name: Enable nginx service
  service:
    name: nginx
    enabled: yes

- name: Get nginx Installed version
  command: nginx -v
    
- name: Get status of nginx service
  command: systemctl status nginx
```

## データベースサーバーのPlaybook

以下の内容でroles/db_server_role/tasks/main.yamlファイルをAnsiblePCに配置します。
PostgreSQLのインストール、初期化、サービスの開始・有効化、バージョン確認とサービス状態の確認を自動化します。

```yaml:roles/db_server_role/tasks/main.yaml
- name: Install PostgreSQL
  yum:
    name: postgresql15-server
    state: present

- name: Initialize PostgreSQL database
  command: postgresql-setup initdb

- name: Start PostgreSQL
  service:
    name: postgresql
    state: started
    enabled: true

- name: Get PostgreSQL version
  command: psql --version

- name: Get status of PostgreSQL service
  command: systemctl status postgresql
```

## インベントリファイルの設定

Ansibleのインベントリファイルは、管理対象のサーバー群（ノード）を定義します。以下の内容でプロジェクトのルートディレクトリにinventory.txtファイルを配置してください。これにより、アプリケーションサーバー群とデータベースサーバー群の設定が行えます。

```txt:inventory.txt
[appservers]
ansible-dev-app1 ansible_host=[app用PC1のIPアドレス] ansible_connection=ssh ansible_user=ec2-user
ansible-dev-app2 ansible_host=[app用PC2のIPアドレス] ansible_connection=ssh ansible_user=ec2-user
[dbservers]
ansible-dev-db1 ansible_host=[DB用PC1のIPアドレス] ansible_connection=ssh ansible_user=ec2-user
ansible-dev-db2 ansible_host=[DB用PC2のIPアドレス] ansible_connection=ssh ansible_user=ec2-user
```

このファイルにより、Ansibleはどのホストがアプリケーションサーバー群に、どのホストがデータベースサーバー群に属しているかを識別できます。

## メインのPlaybook

以下は、アプリケーションサーバーとデータベースサーバーの設定を行うためのメインPlaybookです。このPlaybookでは、まずすべてのサーバーでユーザーを作成し、次にアプリケーションサーバーにはnginxを、データベースサーバーにはPostgreSQLをそれぞれセットアップします。

まず、共通の変数を定義するvars.yamlを配置します。これにより、後続のPlaybookで利用する変数が設定されます。

```yaml:vars.yaml
html_path: "/usr/share/nginx/html/"
```

次に、メインの構成を実行するansible.yamlを配置します。

```yaml:ansible.yaml
- name: Setup same server
  hosts: 
    - appservers
    - dbservers
  become: True
  tasks:
    - name: Ensure group normalusers exists
      group:
        name: normalusers
        state: present

    - name: Create new user
      user:
        name: normaluser
        group: normalusers
        createhome: yes
        shell: /bin/bash
      register: create_user

    - name: Print create user status
      debug:
        msg: "ユーザー作成に成功しました。"
      when: create_user.changed

- name: Setup nginx server
  hosts: appservers
  become: True
  roles:
    - app_server_role

- name: Setup db server
  hosts: dbservers
  become: True
  roles:
    - db_server_role
```

# Playbookの実行

以下コマンドを実行し、app用PCとDB用PCにそれぞれ設定を行っていきます。

```bash
ansible-playbook -i inventory.txt ansible.yaml -v
```

## Playbook実行ログの確認

Playbookの実行ログ配下となります。今回はオプション「-v」を外した簡易的な実行ログを以下に乗せてます。

```txt
[ec2-user@IPアドレス projects]$ ansible-playbook -i inventory.txt ansible.yaml
PLAY [Setup same server] **********************************************************************************************************************************************

[ここに共通のインストール結果が表示されます]
[省略]

PLAY [Setup nginx server] *********************************************************************************************************************************************

[ここにnginxのインストール結果が表示されます]
[省略]

PLAY [Setup db server] ************************************************************************************************************************************************

[ここにPostgreSQLのインストール結果が表示されます]
[省略]

PLAY RECAP ************************************************************************************************************************************************************
ansible-dev-app1           : ok=11   changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
ansible-dev-app2           : ok=11   changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
ansible-dev-db1            : ok=8    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
ansible-dev-db2            : ok=8    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

[ec2-user@IPアドレス projects]$
```

最後に各タスクの実行結果が表示されます。failedが1つでも存在すると失敗となります。失敗したタスクの箇所を探し、エラーログを解析し、デバッグを行います。今回はfailed=0であれば成功です。

## アプリケーション用PCの確認

![アプリケーション用PC01の確認](https://storage.googleapis.com/zenn-user-upload/4fd3ce3de791-20240317.png)

![アプリケーション用PC02の確認](https://storage.googleapis.com/zenn-user-upload/30aebb5073ab-20240317.png)

## DB用PCの確認

DB用PCはprivateサブネットに配置されている為、Ansible PCから各DB用PCへssh接続でアクセスし、下記コマンドでPostgreSQLのバージョンを確認し、正常にインストールされたことを確認します。

```bash
[ec2-user@[DB用PC01のIPアドレス] ~]$ psql --version
psql (PostgreSQL) 15.6
[ec2-user@[DB用PC01のIPアドレス] ~]$

[ec2-user@[DB用PC02のIPアドレス] ~]$ psql --version
psql (PostgreSQL) 15.6
[ec2-user@[DB用PC02のIPアドレス] ~]$
```


# URL

https://docs.ansible.com/ansible/2.9/modules/list_of_commands_modules.html

https://docs.ansible.com/ansible/2.9/modules/service_module.html#service-module

