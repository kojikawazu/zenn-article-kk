---
title: "Cloud Functionsを使ってみる"
emoji: "📄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["zenn","CloudFunctions","GCP","Nodejs"]
published: false
---

# 目的

- バックエンドAPIを実装する必要がある。
- 小規模な為、サーバレスとしたい。

# Cloud Functionsとは

クラウドサービスの構築と接続に使用するサーバーレスのランタイム環境。
Cloud Functionsを使用すると、クラウドのインフラストラクチャやサービスで生じたイベントに関連するシンプルで一義的な関数を作成できる。

私はCloud Functionsで
- Cloud StorageからJSONデータを取得する
- 外部メールサービスのAPIを使用し、Gmailを送信する
- CLoud Loggingでエラーログを出力する

以下ドキュメントを参考としている。
https://cloud.google.com/functions/docs/concepts/overview?hl=ja


# 手動で関数を追加する

まずはGoogle Functionsに関数を作成し、デプロイを実施する。
今回はドキュメントを基にテキストベースで記載する。

ドキュメントは以下になる。
https://cloud.google.com/functions/docs/console-quickstart?hl=ja

1. Google Cloud Platform(GCP)のコンソール画面を開く

以下URLへ移動する。
https://console.cloud.google.com/?hl=ja

2. Projectを1つ作成する

以下URLはプロジェクトの作成方法になります。
https://cloud.google.com/resource-manager/docs/creating-managing-projects?hl=ja&_ga=2.234129886.-1344870585.1694360309&_gac=1.196132190.1696416606.Cj0KCQjwmvSoBhDOARIsAK6aV7iBLE02RpuWv_ERVrkOlBUswnDStF54IQW9Cwg16v57p8aE371W3QIaAjFgEALw_wcB

3. 検索欄から「Google Functions」を検索し移動する 
&nbsp;

4. 新しい関数を1つ作成する
  「ファンクションを作成する」ボタンを押下する
&nbsp;

5. 必要なAPIを有効にする
   
   以下追加するAPIリストになる。
   - Cloud Build API
   - Cloud Functions API
   - Cloud Logging API
   - Cloud Pub/Sub API
  
   ※ 後に「Cloud Run Admin API」も必要となる為、予めAPIの有効化を推奨する。
&nbsp;

6. 各項目を入力し、「次へ」ボタンを押下する
   今回は基本のみ入力する。

   - 基本
     - 「第1世代」、「第2世代」がある。
        HTTPSがデフォルトで使用可能な「第2世代」を使用する。 
     - 関数名
       任意でOK
     - リージョン
       「asia-northest1(東京)」を使用する。
   - トリガー
     - 認証
      「未認証の呼び出しを許可」、「認証が必要」がある。
      お試し用の為、「未認証の呼び出しを許可」を選択
   - URL
     作成されるURLが表示される。今後バックエンドAPIを実行する時に必要となる。 
&nbsp;

7. 必要なAPIを有効にする

   以下追加するAPIリストになる。
   - Cloud Run Admin API
&nbsp;

8. コードを実装する

  - 構成
    - ランタイム
      .NET, Go, Java, Node.js, PHP, Python, Rubyと使用できる。
      今回はデフォルトのまま、「Node.js 20」を使用する。
    - ソースコード
      ZIPアップロード、Cloud StorageのZIP、インラインエディタと使用できる。
      今回は「インラインエディタ」を使用する。
    - エントリポイント 
      今回はそのまま使用する。 

実際のソースコードは以下となる。今回はデフォルトのまま使用する。

```javascript
// index.js
const functions = require('@google-cloud/functions-framework');

/**
 *  HTTPSで実行される関数
 *  http関数の第1引数は、エントリポイントと一致させること
 */
functions.http('helloHttp', (req, res) => {
  res.send(`Hello ${req.query.name || req.body.name || 'World'}!`);
});
```

```json
// package.json

// ここに必要となるパッケージを追加していく
{
  "dependencies": {
    "@google-cloud/functions-framework": "^3.0.0"
  }
}
```

9. デプロイ前テスト
   コードを実装したら、「関数をテスト」ボタンを押下する。
   テスト環境が自動生成する為、Cloud Shellのターミナルでテストの確認を行う。
&nbsp;

10.  デプロイする
    「デプロイ」ボタンを押下する。
    デプロイが完了すると「URL」が作成され、そのリンクへ移動すると実行が可能。

# 実行結果

Cloud FunctionsのURLを実行すると以下が表示される

![image](https://storage.googleapis.com/zenn-user-upload/9f18232cbc45-20231006.png)
