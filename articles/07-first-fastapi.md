---
title: "FastAPI始めました"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python", "fastapi"]
published: true
---

# 🎯目的

新たなWebアプリ開発の技術選定を行う中で、FastAPIを試用しました。そのプロセスとFastAPIの特徴を初めて構築する過程で記録します。

# FastAPIとは？

FastAPIは、**Python 3.6**+で書かれたモダンで高速なPythonのWebフレームワークです。**非同期処理**を強力にサポートしており、**高速なAPIの開発**に最適化されています。

## 主な特徴:

- **速度**: FastAPIは、Pythonで利用可能な最速のフレームワークの一つ。非同期処理により、**I/O待ち時間が大幅に削減**されます。
- **簡易性と柔軟性**: 依存性注入、セキュリティ、認証などの機能を備えた使いやすい構文を提供します。
- **データのバリデーションと自動ドキュメント生成**: Pydanticの使用により、データのバリデーションと自動的なモデルのドキュメント化が可能です。

# DjangoとFastAPIの違い

Djangoは、**バッテリー同梱**アプローチを採用した、より伝統的なフルスタックWebアプリケーションフレームワークです。一方、FastAPIはAPIの構築に特化しているが、より速度と非同期処理に焦点を当てています。

## 主な違い:

- **速度とパフォーマンス**: FastAPIは非同期処理を原生でサポートしており、通常より高速なレスポンスを提供します。
- **データのバリデーションと型の安全性**: FastAPIはPydanticを使用しており、実行時にデータ型の安全性とバリデーションを提供します。Djangoでは、フォームやシリアライザを通じてこれを行うが、同じレベルの型の安全性は提供されません。
- **自動ドキュメント生成**: FastAPIは、OpenAPIとJSON Schemaに基づいて自動的にAPIドキュメントを生成します。Djangoでは、SwaggerやRedocを使用するために追加のセットアップが必要です。

# 今回使用するバージョン
- Python 3.10.7
- FastAPI 0.104.0(現時点最新)

# 環境構築

まずはフォルダを作成し、フォルダの中でコマンドを実行します。
今回はWindows10で実行し、コマンドプロンプトで実行しています。

```bash
# FastAPI用の仮想環境の構築
python -m venv fastapi-env

# 仮想環境のアクティベーション
fastapi-env\Scripts\activate

# FastAPIとUvicornのインストール
pip install fastapi uvicorn
```

Uvicornは、非常に高速なASGI（Asynchronous Server Gateway Interface）サーバーであり、Pythonにおける非同期Webアプリケーションとフレームワークのためのインターフェースを提供します。
高速で自動リロード可能、HTTPS接続をネイティブにサポートしています。

# プロジェクトフォルダ構成

```
.
├── app
│   ├── routers
│   │  ├── __init__.py
│   │  └── hello.py
│   ├── __init__.py 
│   └── main.py
└── requirements.txt
```

### main.py

```python
from fastapi import FastAPI
from .routers import hello

# FastAPIのインスタンス化
app = FastAPI()

# helloのルーティング設定
app.include_router(hello.router)
```

### hello.py

```python
from fastapi import APIRouter

# ルーティング設定
router = APIRouter()

# GETメソッド
# レスポンスはJSON形式
@router.get("/hello")
def get_hello():
    return {"Hello": "World"}
```

### requirements.txt

プロジェクトの依存関係を以下に記載します。

```txt
fastapi
uvicorn
```

# 起動コマンド

```bash
uvicorn app.main:app --reload
```

# 結果表示

![結果表示](https://storage.googleapis.com/zenn-user-upload/675a46831fa9-20231024.png)

# 今後の展開

FastAPIの環境構築と初回の動作確認が無事に完了しました。今後は、更に深くFastAPIの機能を探求していきます。ルーティング、CRUD、認証、テスト、Dockerコンテナ、CICDの自動化の分野も深堀する予定です。

# 参考

### FastAPIの公式

https://fastapi.tiangolo.com/ja/

最後までお読みいただきありがとうございました！

