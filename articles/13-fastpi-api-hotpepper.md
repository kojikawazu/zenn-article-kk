---
title: "FastAPIでAPIを使ってみよう①"
emoji: "🚀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["fastapi", "python"]
published: false
---

# 🎯目的

- 公開されているAPIからデータを取得してみよう
- 今回はフリーでクライアントキーを取得して実行できる「hotpepper」を使用してみた

# ホットペッパーのAPIキーを取得

以下サイトで新規登録を行い、APIキーを取得します。

https://webservice.recruit.co.jp/index.html

Powered by [ホットペッパーグルメ Webサービス](http://webservice.recruit.co.jp/)

# API構成

今回のAPI構成は以下になります。

```
GET : /hotpepper (ホットペッパーのAPIを実行し、データを取得する)
```

# プロジェクトフォルダ構成

今回のプロジェクトフォルダ構成は以下になります。

```
.
├── fastapi-env(仮想環境)
├── app
│   ├── routes
│   │   ├── __init__.py
│   │   └── hotpepper.py
│   └── main.py
├── .env
└── requirements.txt(FastAPIを使用する際に必要なPythonライブラリのリストを含んだファイル)
```

# 環境構築

環境構築を行います。

```bash
# 仮想環境の構築
python -m venv fastapi-env
# 仮想環境を使用する
fastapi-env\Scripts\activate
# 必要なモジュールをインストール
pip install fastapi uvicorn requests
# 実行してみる
uvicorn app.main:app --reload
```

# まずはAPIを叩いてみる

ラーメンで検索し、ホットペッパーのデータを取得してみます。

### ソースコード

各ソースコードは以下になります。

```python
# main.py
from fastapi import FastAPI
from .routes import hotpepper

app = FastAPI()

app.include_router(hotpepper.router)
```

```python
# routes/hotpepper.py

from fastapi import APIRouter, HTTPException 
from dotenv import load_dotenv
import requests
import os

load_dotenv(os.path.join(os.path.abspath(os.curdir), ".env"))
API_KEY = os.getenv("HOT_PEPPER_API_KEY")

# ルーティング
router = APIRouter()

# ホットペッパーAPIのエンドポイント
HOTPEPPER_URL = "https://webservice.recruit.co.jp/hotpepper/gourmet/v1/"
SEARCH_WORD = "ラーメン"
OUTPUT_FORMAT = "json"
RESPONSE_TARGET = "shop"

@router.get("/hotpepper")
def get_hotpepper_data():
    # パラメータの設定
    params = {
        "key": API_KEY,
        "keyword": SEARCH_WORD,
        "format": OUTPUT_FORMAT
    }
    
    try:
        # APIリクエストの実行
        response = requests.get(HOTPEPPER_URL, params=params)
        response.raise_for_status()

    except requests.RequestException as e:
        raise HTTPException(status_code=400, detail=f"APIリクエストに失敗しました: {e}")

    data = response.json()

    # データのバリデーション
    if "results" not in data or RESPONSE_TARGET not in data["results"]:
        raise HTTPException(status_code=500, detail="APIからの応答が不正です")

    # データを返す
    return data["results"][RESPONSE_TARGET]
```

.envファイルにホットペッパーのAPIキーを設定します。

```bash
# .env
HOT_PEPPER_API_KEY="ホットペッパーで取得したAPI"
```

### 実行結果

Postmanで実行してみます。
JSONデータが取得できたら成功です。(取得内容はこの記事には表示しないようにしてます。)

![取得結果①](https://storage.googleapis.com/zenn-user-upload/e1e17529bc8e-20231119.png)

# よりデータを限定的にしてみる

### ソースコード

各ソースコードは以下になります。

```python
# (省略)

SEARCH_WORD = "ラーメン"
OUTPUT_FORMAT = "json"
SERVICE_AREA_CODE = "SA12" # サービスエリアコードを追加
RESPONSE_TARGET = "shop"

@router.get("/hotpepper")
def get_hotpepper_data():
    # パラメータの設定
    params = {
        "key": API_KEY,
        "keyword": SEARCH_WORD,
        "format": OUTPUT_FORMAT,
        "service_area": SERVICE_AREA_CODE # サービスエリアコードを設定する
    }
    
    # (省略)

    # 店名のみ取得する
    shop_names = [shop['name'] for shop in data["results"][RESPONSE_TARGET]]

    # データを返す
    return shop_names
```

### 実行結果

こちらもPostmanで実行してみます。
神奈川でラーメンで店名のみのJSONデータが取得できたら成功です。
(取得内容はこの記事には表示しないようにしてます。)

![取得結果②](https://storage.googleapis.com/zenn-user-upload/e1e17529bc8e-20231119.png)

# 終わりに

この記事では、FastAPIを活用してホットペッパーグルメのAPIからデータを取得する方法に焦点を当てました。
他にも様々なAPIがあり、これらを組み合わせることで、多彩なアプリケーションを開発することが可能です。
今後も、様々なAPIを探求していきたいと考えております。

最後までお読みいただきありがとうございました！