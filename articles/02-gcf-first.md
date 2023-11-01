---
title: "Cloud Functionsの導入と実装に挑戦"
emoji: "💻"
type: "tech"
topics: ["zenn","CloudFunctions","GCP","Nodejs"]
published: true
---

# 🎯目的

- バックエンドAPIを実装する必要がある。
- 小規模な為、サーバレスとしたい。
- 最初の導入メモとしても利用。

# Cloud Functionsとは

クラウドサービスの構築と接続に使用するサーバーレスのランタイム環境です。
Cloud Functionsを使用すると、クラウドのインフラストラクチャやサービスで生じたイベントに関連するシンプルで一義的な関数を作成できます。

私はCloud Functionsで以下を実装しました。
- Cloud StorageからJSONデータを取得します
- 外部メールサービスのAPIを使用し、Gmailを送信します
- Cloud Loggingでエラーログを出力します

以下のドキュメントを参考にしました。
https://cloud.google.com/functions/docs/concepts/overview?hl=ja


# 手動で関数を追加する

まず、Cloud Functionsに関数を作成し、デプロイを実施します。
今回はドキュメントを基にテキストベースで記載しました。

ドキュメントは以下になります。
https://cloud.google.com/functions/docs/console-quickstart?hl=ja

1. Google Cloud Platform(GCP)のコンソール画面を開く

以下のURLへ移動します。
https://console.cloud.google.com/?hl=ja

2. Projectを1つ作成する

以下URLはプロジェクトの作成方法になります。
https://cloud.google.com/resource-manager/docs/creating-managing-projects?hl=ja&_ga=2.234129886.-1344870585.1694360309&_gac=1.196132190.1696416606.Cj0KCQjwmvSoBhDOARIsAK6aV7iBLE02RpuWv_ERVrkOlBUswnDStF54IQW9Cwg16v57p8aE371W3QIaAjFgEALw_wcB

3. 検索欄から「Cloud Functions」を検索し移動する 
&nbsp;

4. 新しい関数を1つ作成する
  「ファンクションを作成する」ボタンを押下します。
&nbsp;

5. 必要なAPIを有効にする
   
   以下が追加するAPIリストです。
   - Cloud Build API
   - Cloud Functions API
   - Cloud Logging API
   - Cloud Pub/Sub API
  
   ※ 後に「Cloud Run Admin API」も必要になるので、予めAPIの有効化を推奨します。
&nbsp;

6. 各項目を入力し、「次へ」ボタンを押下する
   今回は基本のみ入力しました。

   - 基本
     - 「第1世代」、「第2世代」があります。
        HTTPSがデフォルトで使用可能な「第2世代」を使用します。
     - 関数名
       任意で大丈夫です。
     - リージョン
       「asia-northest1(東京)」を使用します。
   - トリガー
     - 認証
      「未認証の呼び出しを許可」、「認証が必要」があります。
      お試し用として、「未認証の呼び出しを許可」を選択しました。
   - URL
     作成されるURLが表示されます。今後バックエンドAPIを実行する際に必要となります。
&nbsp;

7. 必要なAPIを有効にする

   以下追加するAPIリストです
   - Cloud Run Admin API
&nbsp;

8. **コードを実装する**

  - 構成
    - ランタイム
      .NET, Go, Java, Node.js, PHP, Python, Rubyが使用できます。
　　　今回はデフォルトのまま、「Node.js 20」を使用しました。
    - ソースコード
      ZIPアップロード、Cloud StorageのZIP、インラインエディタが使用できます。
      今回は「インラインエディタ」を使用しました。
    - エントリポイント 
      今回はそのまま使用しました。

実際のソースコードは以下となります。今回はデフォルトのまま使用しました。

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
    コードを実装したら、「関数をテスト」ボタンを押下します。
　　テスト環境が自動生成されるので、Cloud Shellのターミナルでテストの　　確認を行います。

# 🚀実行結果

Cloud FunctionsのURLを実行すると以下が表示されます。

![image](https://storage.googleapis.com/zenn-user-upload/9f18232cbc45-20231006.png)

# 今後の展望

最初のステップとして、Cloud Functionsのコード管理を最適化する予定です。具体的には、GitHubを利用して、GitHub Actionsを用いた自動デプロイの手順を確立します。次に、Cloud Storageからのデータ取得及び、非公開情報（文字列）の保管方法に注目します。後者については、Secret Managerを使用して情報を保管し、Cloud Functionsから安全に取得するプロセスを手順化する予定です。

最後までお読みいただき本当にありがとうございます！