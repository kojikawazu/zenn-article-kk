---
title: "Google Books APIを使ってみる ～Next.js + Laravel～"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nextjs", "laravel", "javascript", "approuter", "breeze"]
published: false
---

# 始めに

暫くAWSとTerraformを触っていたので、ふとAPIを叩きたくなりました。その為、[Google Books API](https://developers.google.com/books/docs/v1/reference/volumes?hl=ja)のドキュメントを参考に、簡単なアプリケーションを開発することにしました。この記事では、そのプロセスと学んだことを共有します。

# Google Books APIの仕様

[Google Books API](https://developers.google.com/books/docs/overview?hl=ja)は、Google Booksの膨大な書籍データベースにプログラムからアクセスできるようにするWebサービスです。現時点のBooks API v1は試験運用版となっております。今回は[書籍の検索](https://developers.google.com/books/docs/v1/reference/volumes/list?hl=ja)、[書籍のIDによる検索](https://developers.google.com/books/docs/v1/reference/volumes/get?hl=ja)の機能に焦点を当てます。

## キーワードによる検索

キーワード検索は最も基本的な機能の一つです。以下のAPIエンドポイントに対してHTTP GETリクエストを送信することで、指定したキーワードに一致する書籍の一覧を取得できます。

```bash
GET https://www.googleapis.com/books/v1/volumes?q={search terms}
```

## IDによる検索

特定の書籍の詳細情報を取得するには、その書籍のvolumeIdを指定して以下のエンドポイントにリクエストします。

```bash
GET https://www.googleapis.com/books/v1/volumes/volumeId
```

## レスポンスデータ

返ってきた[書籍データ](https://developers.google.com/books/docs/v1/reference/volumes?hl=ja#resource)を元に書籍を表示しますが、ポイント部分だけ抜粋して載せていきます。

| プロパティ名 | 値           | 説明          |
|:-----------:|:------------:|:------------:|
| id                        | string     | volumeId       |
| volumeInfo.title          | string     | 書籍タイトル    |
| volumeInfo.authors[]      | list       | 編集者の名前    |
| volumeInfo.publisher      | string     | パブリッシャー  |
| volumeInfo.publishedDate  | string     | 公開日         |
| volumeInfo.description    | string     | 概要           |
| volumeInfo.categories[]   | list       | 分類           |
| volumeInfo.imageLinks.thumbnail    | string     | サムネイルのサイズ(幅 128 ピクセル以下)の画像リンク           |
| volumeInfo.previewLink    | string     | サイトでプレビューするための URL           |
| volumeInfo.description    | string     | 概要           |

# アプリの構成を考える

Google Books APIから得られる書籍データを活用し、検証用にアプリケーションを実装します。今回は直近で学んだ技術スタックの知識の深堀もかねております。(App Router、Laravel)Google Books APIだけならフロント側だけで十分ですが、プラスアルファでバックエンドも利用しました。

## 技術スタック

- [フロントエンド側]
  - Next.js 14([今回はLaravel Breeze用を使用](https://github.com/laravel/breeze-next))
  - App Router
  - TailWindCSS 3.0.22
- [バックエンド側]
  - PHP 8.1 
  - Laravel 10
  - Laravel breeze
- [DB]
  - PostgreSQL 

## アプリケーション仕様

認証周りはLaravel Breezeを使用します。検証の為、少なめでざっくりとした仕様とします。
(データベース仕様は割愛します)

- 書籍検索機能: ユーザーがキーワードを入力し、APIを通じて書籍を検索できます。
- 書籍詳細表示: 選択された書籍の詳細情報を表示します。
- レビュー機能: ユーザーが書籍に対してレビューを投稿できます。
- お気に入り機能: ユーザーが書籍をお気に入りに追加できます。

## 画面仕様

今回は3画面とします。デザインにはTailwind CSSのテンプレートを基にカスタマイズを加えます。

- 書籍検索画面
- 書籍詳細画面
- お気に入り画面

## バックエンドAPI仕様

バックエンドでは、以下のAPIエンドポイントを提供します。これらは、フロントエンドからのリクエストに基づいて適切なデータ操作を行い、結果を返します。

:::details バックエンドAPI仕様
| method | URL          | response     |概要         |
|:------:|:------------:|:------------:|:------------:|
| GET    | /favorites           | お気に入りデータ               | ログイン中のお気に入りデータの取得 |
| GET    | /favorites/{book_id}/myuser | true:存在する false:存在しない | ログイン中の{book_id}のお気に入りデータの存在有無 |
| POST   | /favorites           | 追加したお気に入りデータ        | お気に入りデータの追加          |
| GET    | /reviews/{book_id} | true:存在する false:存在しない    | ログイン中の{book_id}のレビューデータの取得 |
| POST   | /reviews             | 追加したレビューデータ          | レビューデータの追加            |
:::

# Dockerコンテナを使った開発環境の構築

開発環境をDockerコンテナで構築します。以下はdocker-compose.ymlとDockerfileの設定例です。

:::details docker-compose.yml
```yml
version: '3.8'

services:
  # Front側コンテナ(Next.js)
  nextjs:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: nextjs
    ports:
      - 3000:3000
    volumes:
      - ./frontend:/app
      - next:/frontend/app/.next
      - node_modules:/frontend/app/node_modules
    working_dir: /app
    environment:
      - NODE_ENV=development
      - NEXT_PUBLIC_API_URL=http://localhost:8000/api
    depends_on:
      - laravel

  # Back側コンテナ(Laravel)
  laravel:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: laravel
    ports:
      - 8000:8000
    volumes:
      - ./backend:/var/www/html
      - /var/www/html
    working_dir: /var/www/html
    environment:
      # -----------------------------------------
      # 任意で変更してください
      # -----------------------------------------
      - DB_CONNECTION=pgsql
      - DB_HOST=postgresql
      - DB_PORT=5432
      - DB_DATABASE=laravel
      - DB_USERNAME=admin
      - DB_PASSWORD=admin
    depends_on:
      - postgresql

  # DBコンテナ(PostgreSQL)
  postgresql:
    image: postgres:latest
    container_name: postgresql
    ports:
      - 5432:5432
    volumes:
      - ./postgresql:/var/lib/postgresql/data
    environment:
      # -----------------------------------------
      # 任意で変更してください
      # -----------------------------------------
      - POSTGRES_DB=laravel
      - POSTGRES_USER=admin
      - POSTGRES_PASSWORD=admin
      - POSTGRES_ROOT_PASSWORD=root

  # pgMyAdminコンテナ
  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin
    ports:
      - 3030:80
    environment:
      - PGADMIN_DEFAULT_EMAIL=admin@example.com
      - PGADMIN_DEFAULT_PASSWORD=admin
    depends_on:
      - postgresql
    volumes:
      - ./pgadmin:/var/lib/pgadmin

volumes:
  postgresql:
  next:
  node_modules:
  pgadmin:
```
:::

:::details Dockerfile(フロントエンド側 - Next.js)
```bash
FROM node:latest

WORKDIR /app

RUN apt-get update && apt-get install -y \
    software-properties-common \
    libzip-dev \
    zip \
    unzip \
    git \
    vim \
    iputils-ping \
    && rm -rf /var/lib/apt/lists/*

ADD ./ /app

RUN npm install

CMD npm install; \
    npm run dev
```
:::

:::details Dockerfile(バックエンド側 - Laravel)
```bash
FROM php:8.1-fpm

WORKDIR /var/www/html

# 必要なパッケージのインストール
RUN apt-get update && apt-get install -y \
    software-properties-common \
    libzip-dev \
    zip \
    unzip \
    git \
    vim \
    iputils-ping \
    default-mysql-client \
    libpq-dev \
    postgresql-client \
    && rm -rf /var/lib/apt/lists/*

# PHPの拡張機能のインストール
RUN docker-php-ext-install pdo_mysql zip pdo_pgsql pgsql

# Composerのインストール
RUN curl -sS https://getcomposer.org/installer | php; \
    mv composer.phar /usr/local/bin/composer; \
    chmod +x /usr/local/bin/composer

ADD ./ /var/www/html

# Laravelの起動
RUN composer install; \
    php artisan migrate; \
    php artisan cache:clear; \
    php artisan config:cache

CMD composer install; \
    php artisan migrate; \
    php artisan cache:clear; \
    php artisan config:cache; \
    php artisan serve --host=0.0.0.0 --port=8000
```
:::

# API Routes

Next.jsのAPI Routesを使用して、Google Books APIからデータを取得するエンドポイントを実装します。
今回は「[App RouterのRoute Handlers](https://nextjs.org/docs/app/building-your-application/routing/route-handlers)」とします。

## 書籍データリスト取得
ユーザーが検索したキーワードに基づいて書籍データリストを取得するためのAPI Routeです。デフォルト検索語として「プログラミング」を使用し、Google Books APIから関連する書籍情報を取得します。

```javascript
// src/app/api/books/route.js
import axios from 'axios';

/**
 * 書籍データリスト取得処理
 * @returns 書籍データリスト
 */
export async function GET() {
    try {
        const defaultSearch = "プログラミング";
        const response = await axios(
            encodeURI(`https://www.googleapis.com/books/v1/volumes?q=${defaultSearch}`),
        );

        const data = await response.data;
        return new Response(JSON.stringify({ data }), {
            headers: { 'Content-Type': 'application/json' },
            status: 200,
        });
    } catch (error) {
        return new Response('サーバー側でエラーが発生しました', {
            status: 500,
        });
    }
}
```

```javascript
// src/app/api/book/route.js
import axios from 'axios'

/**
 * Book取得処理(検索版)
 * @returns ブックデータリスト
 */
export async function GET(request) {
    try {
        const searchParams = request.nextUrl.searchParams;
        const search = searchParams.get('search');

        // 検索パラメータのバリデーションチェック
        if (!search || search.trim().length === 0) {
            // 検索文字列が空、または不適切な場合はエラーレスポンスを返す
            return new Response('検索が無効です', {
                status: 400,
            });
        }

        const response = await axios(
            encodeURI(`https://www.googleapis.com/books/v1/volumes?q=${search}`),
        );

        const data = await response.data;
        return new Response(JSON.stringify({ data }), {
            headers: { 'Content-Type': 'application/json' },
            status: 200,
        });
    } catch (error) {
        console.error(error);
        return new Response('サーバー側でエラーが発生しました', {
            status: 500,
        });
    }
}

```

## 書籍詳細情報取得

書籍のIDを指定して、その書籍の詳細情報を取得するAPI Routeです。クライアントからのリクエストに含まれる書籍IDを使用して、Google Books APIへの問い合わせを行います。

```javascript
// src/app/api/book/[bookId]/route.js
import axios from 'axios';

/**
 * 書籍データ取得処理(detail版)
 * @returns 書籍データ
 */
export async function GET(request, { params }) {
    try {
        const bookId = params.bookId;

        // bookIdのバリデーションチェック（例：特定の形式に一致するか確認）
        if (!/^[a-zA-Z0-9\-_]+$/.test(bookId)) {
            // 不正なbookIdの場合はエラーレスポンスを返す
            return new Response('向こうなパラメータです', {
                status: 400,
            });
        }

        const response = await axios(
            encodeURI(`https://www.googleapis.com/books/v1/volumes/${tempSearchWord}`),
        );

        const data = await response.data;
        return new Response(JSON.stringify({ data }), {
            headers: { 'Content-Type': 'application/json' },
            status: 200,
        });
    } catch (error) {
        console.error(error);
        return new Response('サーバー側でエラーが発生しました', {
            status: 500,
        });
    }
}
```

# フロントエンド側のコード実装

Next.jsを使用してフロントエンドを構築し、書籍の検索と詳細表示機能を実装します。以下は、書籍検索ページと書籍詳細ページのコンポーネントの実装例です。

## 書籍検索ページコンポーネント

書籍検索ページでは、ユーザーが入力した検索キーワードに基づき、APIを呼び出して書籍情報を取得し、表示します。

```javascript
// src/components/pafe/BookIndex.jsx
'use client'

import React, { useEffect, useState } from 'react';
import axios from 'axios';
import Loading from '@/app/(app)/Loading';
import BookList from "@/components/book/list/BookList";

/**
 * ブックIndexページコンポーネント
 * @returns JSX
 */
const BookIndex = () => {
  const [books, setBooks] = useState([]);
  const [searchWord, setSearchWord] = useState("");

  const handleSearchBook = async () => {
    const tempSearchWord = searchWord.trim().replace(/\s/g, '+');

    try {
      const response = await axios.get(`/api/book?search=${encodeURI(tempSearchWords)}`);
      
      if (response.data && response.data.data && response.data.data.items) {
        setBooks(response.data.data.items);
      } else {
        setBooks([]);
      }
      setSearchWord("");
    } catch (err) {
      console.error("サーバー側でエラーが発生しました");
    }
  };

  useEffect(() => {
    const fetchBooks = async () => {
      try {
        const response = await axios.get(`/api/books`);
        
        if (response.data && response.data.data && response.data.data.items) {
          setBooks(response.data.data.items);
        } else {
          setBooks([]);
        }
      } catch (err) {
        console.error("サーバー側でエラーが発生しました");
      }
    };

    fetchBooks();
  }, []);

  if (books.length == 0) {
    return <Loading />
  }
  return (
    <div className="py-12">
      <div className="mx-auto sm:px-6 lg:px-8">
        <div>
          <div className="flex rounded-lg shadow-sm">
            <button
              className="px-4 inline-flex items-center min-w-fit rounded-s-md border border-e-0 border-gray-200 bg-gray-50 text-sm text-gray-500 dark:bg-gray-700 dark:border-gray-700 dark:text-gray-400"
              onClick={handleSearchBook}
            >
              <span>検索</span>
            </button>
            <input
              type="text"
              onChange={e => setSearchWord(e.target.value)}
              className="py-3 px-4 pe-11 block w-full border-gray-200 shadow-sm rounded-e-lg text-sm focus:z-10 focus:border-blue-500 focus:ring-blue-500 disabled:opacity-50 disabled:pointer-events-none dark:bg-slate-900 dark:border-gray-700 dark:text-gray-400 dark:focus:ring-gray-600" />
          </div>
        </div>

        <div className="shadow-sm sm:rounded-lg">
          <div className="p-6 bg-gray-100 border-b border-gray-200">
            <BookList books={books} />
          </div>
        </div>
      </div>
    </div>
  )
}

export default BookIndex;
```

## 書籍詳細ページ

書籍IDに基づき、その書籍の詳細情報を表示するページです。お気に入りの追加やレビューの投稿機能もこのページで扱います。

```javascript
// src/components/pafe/BookDetail.jsx
'use client'

import { useEffect, useState } from 'react';
import axios from 'axios';
import Loading from '@/app/(app)/Loading';
import laravelAxios from '@/lib/laravelAxios';
import BookDetailCard from '@/components/book/card/BookDetailCard';
import BookReviewPage from '@/components/book/page/BookReviewPage';

/**
 * ブックDetailページコンポーネント
 * @returns JSX
 */
const BookDetail = ({
    bookId
}) => {
    const [book, setBook] = useState(null);
    const [isFavorite, setIsFavorite] = useState(false);

    const handleFavorite = async () => {
        try {
            const response = await laravelAxios.post(`/api/favorites`, {
                book_id: bookId,
            });
            setIsFavorite(response.data.status === 'added');
        } catch (err) {
            console.error("サーバー側でエラー発生");
            setIsFavorite(false);
        }
    }

    const fetchFavoriteMyUser = async () => {
        try {
            const response = await laravelAxios.get(`/api/favorites/${bookId}/myuser`);
            const responseData = response.data;
            setIsFavorite(responseData);
        } catch (err) {
            console.error("サーバー側でエラーが発生");
            setIsFavorite(false);
        }
    };

    useEffect(() => {
        const fetchBookDetail = async () => {
            try {
                const response = await axios.get(`/api/book/${bookId}`);
                setBook(response.data.data);
            } catch (err) {
                console.error("サーバー側でエラーが発生");
            }
        }

        fetchBookDetail();
        fetchFavoriteMyUser();
    }, [bookId]);

    if (book == null) {
        return <Loading />
    }
    return (
        <>
            <BookDetailCard
                book={book}
                isFavorite={isFavorite}
                handleFavorite={handleFavorite} />
            <hr />
            <BookReviewPage
                book={book}
            />
        </>

    )
}

export default BookDetail;
```

# バックエンド側のコード実装(おまけ)

レビュー機能とお気に入り機能のCRUD操作も含まれています。この部分はおまけとして、主にLaravelを用いたルーティングとコントローラーの実装を紹介します。

:::details ルーティング(Laravel)

バックエンドのAPIエンドポイントを設定します。ここでは、お気に入りとレビュー機能に関するルートを定義しています。全てのルートは、認証済みユーザーのみがアクセス可能です。

```php
<?php

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\FavoriteController;
use App\Http\Controllers\ReviewController;

Route::middleware(['auth:sanctum'])->get('/user', function (Request $request) {
    return $request->user();
});

Route::middleware(['auth:sanctum'])->group(function () {
    Route::post('/favorites', [FavoriteController::class, 'toggleFavorite']);
    Route::get('/favorites', [FavoriteController::class, 'index']);
    Route::get('/favorites/{book_id}/myuser', [FavoriteController::class, 'existsFavoriteByUserId']);

    Route::post('/reviews', [ReviewController::class, 'store']);
    Route::get('/reviews/{book_id}', [ReviewController::class, 'index']);
});
```
:::

:::details コントローラー(Laravel)

お気に入りとレビュー機能のロジックを扱うコントローラーの実装例です。ここでは、APIリクエストを処理し、適切なレスポンスを返します。

```php
<?php
// App/Http/Controllers/FavoriteController.php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Auth;
use App\Models\Favorite;

/**
 * お気に入りコントローラークラス
 */
class FavoriteController extends Controller
{
    public function index()
    {
        $favorites = Favorite::with('user')
            ->get();
        return response()->json($favorites);
    }

    public function toggleFavorite(Request $request) 
    {
        $validatedData = $request->validate([
            'book_id' => 'required|string',
        ]);

        $favorite = Favorite::where('user_id', Auth::id())
            ->where('book_id', $validatedData['book_id'])
            ->first();
        
        if ($favorite) {
            $favorite->delete();
            return response()->json(["status" => "removed"]);
        } else {
            $favorite = Favorite::create([
                'book_id' => $validatedData['book_id'],
                'user_id' => Auth::id(),
            ]);
            $favorite->load('user');
        }

        return response()->json(["status" => "added"]);
    }

    public function existsFavoriteByUserId($book_id) 
    {
        $validatedData = $request->validate([
            'book_id' => 'required|string',
        ]);

        $isFavorite = Favorite::where('user_id', Auth::id())
            ->where('book_id', $book_id)
            ->exists();

        return response()->json($isFavorite);
    }
}
```

```php
<?php
// App/Http/Controllers/ReviewController.php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Auth;
use App\Models\Review;

/**
 * レビューコントローラークラス
 */
class ReviewController extends Controller
{
    public function index($book_id)
    {
        $validated = $request->validate([
            'book_id' => 'required|string',
        ]);

        $reviews = Review::where('user_id', Auth::id())
            ->with('user')
            ->get();
        
        return response()->json($reviews);
    }

    public function store(Request $request)
    {
        $validatedData = $request->validate([
            'book_id' => 'required|string',
            'review' => 'required|string|max:500',
            'star' => 'required|integer|between:1,5',
        ]);

        $review = Review::create([
            'book_id' => $validatedData['book_id'],
            'review' => $validatedData['review'],
            'star' => $validatedData['star'],
            'user_id' => Auth::id(),
        ]);

        $review->load('user');

        return response()->json($review);
    }
}
```
:::

# 実装後の結果

実装を完了した後、Google Books APIからデータを取得し、アプリケーション上で動作するさまざまな機能を確認しました。

- デフォルトの検索結果表示:

![リスト表示](https://storage.googleapis.com/zenn-user-upload/0e5cec9a01b5-20240212.png)

- キーワード「猫」での検索結果:

![検索実行](https://storage.googleapis.com/zenn-user-upload/f256289c4c6d-20240212.png)

- 書籍詳細画面:

![詳細画面](https://storage.googleapis.com/zenn-user-upload/da902226b282-20240212.png)

- レビュー投稿画面:

![レビュー画面](https://storage.googleapis.com/zenn-user-upload/42438287c61c-20240212.png)

- お気に入り画面:

![お気に入り画面](https://storage.googleapis.com/zenn-user-upload/6cc34642bd78-20240212.png)

# さいごに

Google Books APIを利用して書籍データを取得し、アプリケーションで動的に表示する方法を実践しました。Google Books APIだけでなく、楽天ブックスなど他の書籍関連APIを組み合わせてみることで、よりコンテンツを提供できる可能性が広がります。
他にもApp RouterやLaravelなどのフレームワーク・ライブラリも実践的に使用してみて、その使い勝手や便利さを実感しました。今後も色んな技術スタックに挑戦していきます。

最後まで読んでいただき、ありがとうございました！

# 参考URL

https://developers.google.com/books/docs/v1/reference/bookshelves?hl=ja

https://qiita.com/Masahiro111/items/f7d6ad8280ae92717f0f

https://preline.co/

https://qiita.com/Masahiro111/items/f7d6ad8280ae92717f0f

https://tailblocks.cc/