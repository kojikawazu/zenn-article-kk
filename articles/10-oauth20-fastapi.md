---
title: "FastAPIでOAuth2.0認証"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python", "fastapi", "GCP"]
published: false
---

# 🎯目的

- OAuth2.0認証を理解する。
- FastAPIとGCPで実装してみる

# OAuth 2.0とは？

OAuth 2.0は、ユーザーが自分のデータを安全に第三者アプリケーションと共有できるようにするための標準プロトコルです。
例えば、SNSアカウントでログインする機能や、カレンダーアプリがメールアカウントの予定を読み取る機能など、日常の多くのサービスでOAuth 2.0が背後で動作しています。

主に以下の特徴があります。

- トークンベース: ユーザーの資格情報を直接共有するのではなく、アクセストークンを使用してリソースにアクセスします。
- スコープ: アクセス許可の範囲を指定し、アプリケーションがどのリソースにアクセスできるかを制限します。
- 複数のフロー: 状況やニーズに応じて、異なる認可フロー（例: 認可コード、暗黙、リソースオーナーパスワード、クライアントクレデンシャルズ）を提供します。

# OAuth 同意画面の設定

1. 同意画面を開く:
    - Google Cloud Console の「API とサービス」の「認証情報」ページで、「OAuth 同意画面」タブをクリックします。

2. アプリの種類を選択:
    - 「外部」または「内部」のいずれかを選択します。
      - 「外部」: あなたの組織の外部のユーザーにアプリを公開する場合。
      - 「内部」: あなたの組織のユーザーのみにアプリを公開する場合。

3. アプリの詳細を入力:
    - 必要な情報を入力します。これには以下の項目が含まれます:

4. テストユーザーを追加
    - アプリを公開する前に、テストユーザーを追加してアプリの動作を確認することができます。

5. 変更を保存:
    - 入力した情報を保存します。

# GCPにOAuth2.0認証できるように設定

1. Google Cloud Console にログイン:
    - Google Cloud Console にアクセスして、Google アカウントでログインします。

2. プロジェクトを選択または作成:
    - 既存のプロジェクトを選択するか、新しいプロジェクトを作成します。
      (新しいプロジェクトの作成方法は割愛します。)

3. ナビゲーションメニューを開く:
    - 画面の左上にあるハンバーガーメニュー（三本線のアイコン）をクリックして、ナビゲーションメニューを開きます。
      ナビゲーションメニューから「API とサービス」を選択し、そのサブメニューから「認証情報」をクリックします。

4. 認証情報を作成:
    - 「認証情報を作成」ボタンをクリックし、ドロップダウンメニューから「OAuth クライアント ID」を選択します。

5. アプリケーションの種類を選択:
    - クライアント ID の作成ページで、アプリケーションの種類（ウェブアプリケーション）を選択します。

6. クライアント ID の詳細を入力:
    - 必要な情報を入力します。
      - 名前
      - 承認済みのリダイレクトURI 
        - ([ドメイン名]/oauth2/login/callback/) を追加してください。

7. クライアント ID を作成:
    - 「作成」ボタンをクリックして、クライアント ID とシークレットを生成します。
      このクライアント ID とシークレットを「.env」ファイルに記載します。

# 使用バージョン

今回使用するバージョンは以下とする。

- Python  3.10.7
- FastAPI 0.104.0

# 必要なパッケージのインストール

```bash
pip install python-dotenv httpx jinja2
```

# プロジェクトフォルダ構成

```
.
├── app
│   ├── routers
│   │   ├── __init__.py
│   │   └── oauth20.py
│   ├── templates
│   │   └── index.html
│   └── main.py
├── .env
└── requirements.txt
```

# app/main.py

```python
from fastapi import FastAPI
from .routers import oauth20

app = FastAPI()

app.include_router(oauth20.router)
```

# app/templates/index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Login with OAuth2.0</title>
</head>
<body>
    <div>
        <a href="{{ auth_url }}">Login with Google</a>
    </div>
</body>
</html>
```

# app/routes/oauth20.py

```python
from fastapi import APIRouter, Request, Depends, HTTPException, status, Query
from fastapi.security import OAuth2AuthorizationCodeBearer
from fastapi.templating import Jinja2Templates
from dotenv import load_dotenv
import httpx
import os

router = APIRouter()

# 環境変数の読み込み
load_dotenv(os.path.join(os.path.abspath(os.curdir), ".env"))
CLIENT_ID = os.getenv("CLIENT_ID")
CLIENT_SECRET = os.getenv("CLIENT_SECRET")
REDIRECT_URI = os.getenv("REDIRECT_URI")
AUTHORIZATION_URL = "https://accounts.google.com/o/oauth2/v2/auth"
TOKEN_URL = "https://oauth2.googleapis.com/token"

templates = Jinja2Templates(directory="app/templates")

oauth2_scheme = OAuth2AuthorizationCodeBearer(
    authorizationUrl=AUTHORIZATION_URL,
    tokenUrl=TOKEN_URL
)

@router.get("/oauth2/login")
async def login_form(request: Request):
    return templates.TemplateResponse("login.html", {
        "request": request, 
        "auth_url": (
            f"{AUTHORIZATION_URL}?response_type=code&client_id={CLIENT_ID}&"
            f"redirect_uri={REDIRECT_URI}&scope=openid%20email%20profile&"
            f"access_type=offline&prompt=consent"
        )
    })

@router.get("/oauth2/login/callback/")
async def login_callback(code: str = Query(...)):
    async with httpx.AsyncClient() as client:
        token_response = await client.post(
            TOKEN_URL,
            data={
                "grant_type": "authorization_code",
                "code": code,
                "redirect_uri": REDIRECT_URI,
                "client_id": CLIENT_ID,
                "client_secret": CLIENT_SECRET,
            },
        )
    token_response_json = token_response.json()
    if token_response.is_error:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=token_response_json,
        )
    return token_response_json
```

.envには以下を設定する。
```
CLIENT_ID: OAuth2.0のクライアントID
CLIENT_SECRET: OAuth2.0のクライアントシークレット
REDIRECT_URI: /oauth2/login/callback/ へリダイレクトするURL
```

### ログイン画面表示

![結果](https://storage.googleapis.com/zenn-user-upload/82f297e48a7d-20231027.png)

### Googleアカウントのログイン処理

![結果](https://storage.googleapis.com/zenn-user-upload/edaedc9cc4a1-20231027.png)

### ログイン結果

![結果](https://storage.googleapis.com/zenn-user-upload/63b66e1d92ba-20231027.png)

# 最後に

FastAPIを使ったOAuth 2.0認証の基本を学びました。この知識は、多くのウェブアプリケーションでの認証実装の基盤となります。特に、セキュリティに関する知識は深掘りする価値があります。今後も認証やセキュリティに関するテーマでの学びを深めていきたいと思います。

最後にお読みいただきありがとうございました！
