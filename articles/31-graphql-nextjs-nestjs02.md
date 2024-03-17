---
title: "Next.jsからGraphQLサーバーへアクセスしてみる"
emoji: "🤝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["graphql", "nestjs", "nextjs", "typescript", "apollo"]
published: false
---

# はじめに

前回GraphQLとNestJSを組み合わせて構築したGraphQLサーバーの使い方を学び、その知識を前回の記事で共有しました。今回は、その続きとして、Next.js + TypeScriptのフロントエンドからGraphQLサーバーへどのようにアクセスし、データを取得するかを学び、記事として共有します。


# 前回の内容

以下の内容になります。
前回の記事では、GraphQL + NestJSでサーバーを構築し、基本的なクエリとミューテーション、サブスクリプションを実装しました。今回は、WebSocketを必要とするサブスクリプションを除き、クエリとミューテーションの使い方に焦点を当てます。

https://zenn.dev/kou_kawa/articles/30-graphql-nextjs-nestjs

# GraphQLサーバーのスキーマ確認

GraphQLサーバーとのやりとりを開始する前に、使用可能なスキーマを確認します。
ここでは、ユーザーの作成、更新、削除を可能にする基本的なスキーマを使用します。

```graphql:schema.gql
# ----------------------------------------------------------------------
# ユーザー型定義
# ----------------------------------------------------------------------
type User {
  id: Int!
  name: String!
  email: String!
  password: String!
  createdAt: DateTime!
  updatedAt: DateTime!
}

scalar DateTime

# ----------------------------------------------------------------------
# クエリ定義
# ----------------------------------------------------------------------
type Query {
  getUsers: [User!]
}

# ----------------------------------------------------------------------
# ミューテーション定義
# ----------------------------------------------------------------------
type Mutation {
  createUser(createUserInput: CreateUserInput!): User!
  updatePassword(updateUserPasswordInput: UpdateUserPasswordInput!): User!
  deleteUser(deleteUserInput: DeleteUserInput!): User!
}

# ----------------------------------------------------------------------
# 入力定義
# ----------------------------------------------------------------------
input CreateUserInput {
  name: String!
  email: String!
  password: String!
  confirmPassword: String!
}

input UpdateUserPasswordInput {
  email: String
  password: String
  newPassword: String
  confirmPassword: String
}

input DeleteUserInput {
  email: String!
}
```

このスキーマ定義に基づいて、Next.jsアプリケーションからGraphQLクエリやミューテーションを実行する方法を次に進んで説明します。

# Next.js + TypeScriptのフロントエンド側の構築

Next.jsとTypeScriptを使ったフロントエンド開発について説明します。このセクションでは、プロジェクトのセットアップ方法、必要なパッケージのインストール、環境変数の設定、そしてデータ取得のためのクエリの実装に焦点を当てます。バリデーションやセキュリティ面は最低限とし、今回は取り扱いません。

[バージョン]
- Next.js: v14
- TypeScript: v5
- App Router使用

[参考]
GraphQLサーバーは以下を使用しています。
- NestJS
- PostgreSQL
- PrismaORM

# 必要なパッケージのインストール

GraphQLサーバーとの通信にはApollo Clientが推奨されます。Apollo Clientは、クエリ実行、キャッシュ管理、そしてReactコンポーネントとの緊密な統合を提供します。加えて、REST APIの呼び出しにはaxiosが便利です。この2つのライブラリを組み合わせることで、GraphQLとREST APIの両方をスムーズに扱うことができます。

```bash
npm install @apollo/client graphql axios
```

このコマンドにより、Apollo Clientとaxiosがプロジェクトに追加されます。

# 環境変数の定義

プロジェクトのルートにある.envファイルに以下の環境変数を設定します。
これにより、APIのエンドポイントをコード内で簡単に参照できるようになります。

```
GRAPHQL_NESTJS_URL="[GRAPHQLサーバーURL]/graphql"
NEXT_PUBLIC_API_URL="[自身のWebサーバーURL]/api"
```

# Query実装

GraphQLサーバーからデータを取得するためのクエリを実装します。
ここでは、ユーザー情報の取得を例にして説明します。

## APIルート実装

Next.jsの強力なAPIルート機能を活用して、クライアントサイドではなく、サーバーサイドでGraphQLサーバーからデータをフェッチする方法を紹介します。これにより、APIのセキュリティを強化し、クライアントサイドのコードをシンプルに保つことができます。

```ts:src/app/api/query/user/route.ts
import axios from "axios";
import { NextResponse } from "next/server";

/**
 * Query(ユーザーデータ取得)
 * @returns ユーザーデータ
 */
export async function GET() {
    // GraphQLのURL
    // 基本GraphQLサーバーにアクセスするURLは同一です。
    const url = process.env.GRAPHQL_NESTJS_URL!;
    
    const config = {
        headers: {
            'Content-Type': 'application/json',
        },
    };
    
    // GraphQLクエリを設定します。
    // getUsersを実行し、ユーザーデータを取得します。
    // データの中から取得したいユーザーの属性を自由に設定します。
    // 今回は id, name, email, createdAt, updatedAt を取得します。
    const data = {
        query: `
        query getUsers {
            getUsers {
              id
              name
              email
              createdAt
              updatedAt
            }
        }`
    };

    try {
        // axiosを使用してGraphQLサーバーへのPOSTリクエストを実行
        const response = await axios.post(url, data, config);
        // レスポンスデータ確認用
        // console.log('Response: ', response.data.data.getUsers);
        
        // getUsersフィールド以下をJSON形式で返す
        return NextResponse.json(response.data.data.getUsers);
    } catch (error) {
        console.error('Error: ', error);
        return new NextResponse.json({ message: 'データの取得に失敗しました。' }, {
            status: 500,
        });
    }
}
```

このコードは、GraphQLサーバーへのPOSTリクエストを行い、getUsersクエリを実行してユーザーデータを取得します。成功すると、取得したデータをJSON形式で返します。

## ページ実装

フロントエンドでAPIルートを呼び出して、ユーザーデータを取得する方法を示します。

```ts:src/app/query/page.tsx
import React from 'react';

/**
 * Queryページ
 * @returns JSX
 */
const QueryPage = async () => {
    // APIルートを利用してGraphQLサーバーからユーザーデータを取得する
    const response = await fetch(
        `${process.env.NEXT_PUBLIC_API_URL}/query/user`,
        { cache: "no-store" },
    );

    // レスポンスデータを取り出し
    const responseData = await response.json();
    // レスポンスデータ確認用
    // console.log(responseData);

    return (
        <div>query</div>
    );
}

export default QueryPage;
```

上記のように実装するとAPIルートを通じてサーバーサイドでGraphQLクエリを実行し、その結果をフロントエンドのコンポーネントで利用する事が可能になります。

# Mutation実装(ユーザー追加)

ユーザーを追加するためのAPIルートをNext.jsで実装します。この例では、ユーザーからのリクエストを受け取り、そのデータを使用してGraphQLサーバーにユーザー追加のMutationをリクエストします。

## APIルート実装

```ts:src/app/api/mutation/add/route.ts
import axios from "axios";
import { NextResponse } from "next/server";

/**
 * Mutation[ユーザー追加]
 * @param request 
 * @returns ユーザーデータ
 */
export async function POST(request: Request) {
    // リクエストデータ取り出し
    const { name, email, password, confirmPassword } = await request.json();

    // 入力データの検証
    if (!name || !email || !password || password !== confirmPassword) {
        return new NextResponse('バリデーションエラーです。', {
            status: 500,
        });
    }

    // GraphQLのURL
    // 基本GraphQLサーバーにアクセスするURLは同一です
    const url = process.env.GRAPHQL_NESTJS_URL!;
    const config = {
        headers: {
            'Content-Type': 'application/json',
        },
    };
    // リクエストデータ設定
    const variables = {
        "createUserInput": {
            "name": name,
            "email": email,
            "password": password,
            "confirmPassword": confirmPassword,
        },
    };
    // GraphQLのMutationを設定します
    // createUserを実行し、ユーザーを追加します
    // データの中から追加したユーザーの属性を自由に設定します
    // 今回は id, name, email, createdAt, updatedAt を取得します
    const data = {
        query: `
        mutation createUser($createUserInput: CreateUserInput!) {
            createUser(createUserInput: $createUserInput) {
              id
              name
              email
              createdAt
              updatedAt
            }
          }`,
        variables: variables,
    };

    try {
        // axiosを使用してGraphQLサーバーへのPOSTリクエストを実行
        const response = await axios.post(url, data, config);
        // レスポンスデータ確認用
        // console.log('Response: ', response.data.data.createUser);

        // createUserフィールド以下をJSON形式で返す
        return NextResponse.json(response.data.data.createUser);
    } catch (error) {
        console.error('Error: ', error);
        return new NextResponse('サーバー側でエラーが発生しました', {
            status: 500,
        });
    }
}
```

## ページ実装

フロントエンドでAPIルートを呼び出して、ユーザーを追加する方法を示します。

```ts:src/app/mutation/add/page.tsx
import React from 'react';

/**
 * Mutation(ユーザー追加)ページ
 * @returns JSX
 */
const MutationAddPage = async () => {
    // APIルートを利用してGraphQLサーバーからユーザーを追加する
    const response = await fetch(
        `${process.env.NEXT_PUBLIC_API_URL}/mutation/add`,
        {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            cache: "no-store",
            body: JSON.stringify({
                name: "[追加名]",
                email: "[追加Eメールアドレス]",
                password: "[追加パスワード]",
                confirmPassword: "[確認用パスワード]",
            }),
        },
    );

    // レスポンスデータを取り出し
    const responseData = await response.json();
    // レスポンスデータ確認用
    // console.log(responseData);

    return (
        <div>add</div>
    );
}

export default MutationAddPage;
```

# Mutation(パスワード更新)

次にユーザーのパスワードを変更するためのAPIルートをNext.jsで実装します。この例では、ユーザーからのリクエストを受け取り、そのデータを使用してGraphQLサーバーにユーザーのパスワードを変更するMutationをリクエストします。

## APIルート実装

```ts:src/api/mutation/update/route.ts
import axios from "axios";
import { NextResponse } from "next/server";

/**
 * Mutation[パスワード更新]
 * @returns ユーザーデータ
 */
export async function PATCH(request: Request) {
    // リクエストデータ取り出し
    const { 
        email, 
        password, 
        newPassword, 
        confirmPassword 
    } = await request.json();

    // 入力データの検証
    if (!email || !password || !newPassword || newPassword !== confirmPassword) {
        return new NextResponse('バリデーションエラーです。', {
            status: 400,
        });
    }

    // GraphQLのURL
    // 基本GraphQLサーバーにアクセスするURLは同一です
    const url = process.env.GRAPHQL_NESTJS_URL!;
    const config = {
        headers: {
            'Content-Type': 'application/json',
        },
    };
    // リクエストデータ設定
    const variables = {
        "updateUserPasswordInput": {
            "email": email,
            "password": password,
            "newPassword": newPassword,
            "confirmPassword": confirmPassword,
        },
    };
    // GraphQLのMutationを設定します
    // updatePasswordを実行し、ユーザーのパスワードを更新します
    // データの中からパスワード変更したユーザーの属性を自由に設定します
    // 今回は id, name, email, createdAt, updatedAt を取得します
    const data = {
        query: `
        mutation updatePassword($updateUserPasswordInput: UpdateUserPasswordInput!) {
            updatePassword(updateUserPasswordInput: $updateUserPasswordInput) {
              id
              name
              email
              createdAt
              updatedAt
            }
          }`,
        variables: variables,
    };
    

    try {
        // axiosを使用してGraphQLサーバーへのPOSTリクエストを実行
        const response = await axios.post(url, data, config);
        // レスポンスデータ確認用
        // console.log('Response: ', response.data.data.updatePassword);

        // updatePasswordフィールド以下をJSON形式で返す
        return NextResponse.json(response.data.data.updatePassword);
    } catch (error) {
        console.error('Error: ', error);
        return new NextResponse('サーバー側でエラーが発生しました', {
            status: 500,
        });
    }
}
```

## ページ実装

フロントエンドでAPIルートを呼び出して、ユーザーのパスワードを変更する方法を示します。

```ts:src/app/mutation/update/page.tsx
import React from 'react';

/**
 * Mutation(パスワード更新)ページ
 * @returns JSX
 */
const MutationUpdatePage = async () => {
    // APIルートを利用してGraphQLサーバーからユーザーのパスワードを更新する
    const response = await fetch(
        `${process.env.NEXT_PUBLIC_API_URL}/mutation/update`,
        {
            method: "PATCH",
            headers: { "Content-Type": "application/json" },
            cache: "no-store",
            body: JSON.stringify({
                email: "[更新したいユーザーのEメールアドレス]",
                password: "[更新したいユーザーの現パスワード]",
                newPassword: "[新パスワード]",
                confirmPassword: "[確認用パスワード]",
            }),
        },
    );

    // レスポンスデータを取り出し
    const responseData = await response.json();
    // レスポンスデータ確認用
    // console.log(responseData);

    return (
        <div>page</div>
    );
}

export default MutationUpdatePage;
```

# Mutation(ユーザー削除)

次にユーザーを削除するためのAPIルートをNext.jsで実装します。この例では、ユーザーからのリクエストを受け取り、そのデータを使用してGraphQLサーバーにユーザーを削除するMutationをリクエストします。

## APIルート実装

```ts:src/api/mutation/delete/route.ts
import axios from "axios";
import { NextResponse } from "next/server";

/**
 * Mutation[ユーザー削除]
 * @returns ユーザーデータ
 */
export async function DELETE(request: Request) {
    // リクエストデータ取り出し
    const { email } = await request.json();

    // 入力データの検証
    if (!email) {
        return new NextResponse('バリデーションエラーです。', {
            status: 400,
        });
    }

    // GraphQLのURL
    // 基本GraphQLサーバーにアクセスするURLは同一です
    const url = process.env.GRAPHQL_NESTJS_URL!;
    const config = {
        headers: {
            'Content-Type': 'application/json',
        },
    };
    // リクエストデータ設定
    const variables = {
        "deleteUserInput": {
            "email": email,
        },
    };
    // GraphQLのMutationを設定します
    // deleteUserを実行し、ユーザーを削除します
    // データの中から削除したユーザーの属性を自由に設定します
    // 今回は id, name, email, createdAt, updatedAt を取得します
    const data = {
        query: `
        mutation deleteUser($deleteUserInput: DeleteUserInput!) {
            deleteUser(deleteUserInput: $deleteUserInput) {
              id
              name
              email
              createdAt
              updatedAt
            }
        }`,
        variables: variables,
    };
    

    try {
        // axiosを使用してGraphQLサーバーへのPOSTリクエストを実行
        const response = await axios.post(url, data, config);
        // レスポンスデータ確認用
        // console.log('Response: ', response.data.data.deleteUser);
        
        // deleteUserフィールド以下をJSON形式で返す
        return NextResponse.json(response.data.data.deleteUser);
    } catch (error) {
        console.error('Error: ', error);
        return new NextResponse('サーバー側でエラーが発生しました', {
            status: 500,
        });
    }
}
```

## ページ実装

フロントエンドでAPIルートを呼び出して、ユーザーを削除する方法を示します。

```ts:src/app/mutation/delete/page.tsx
import React from 'react';

/**
 * Mutation(ユーザー削除)ページ
 * @returns JSX
 */
const MutationDeletePage = async () => {
    // APIルートを利用してGraphQLサーバーからユーザーのパスワードを更新する
    const response = await fetch(
        `${process.env.NEXT_PUBLIC_API_URL}/mutation/delete`,
        {
            method: "DELETE",
            headers: { "Content-Type": "application/json" },
            cache: "no-store",
            body: JSON.stringify({
                email: "[削除したいユーザーのEメールアドレス]",
            }),
        },
    );

    // レスポンスデータを取り出し
    const responseData = await response.json();
    // レスポンスデータ確認用
    // console.log(responseData);

    return (
        <div>update</div>
    );
}

export default MutationDeletePage;
```

# 実装後の確認

Next.jsとGraphQLを使用して構築したWebアプリケーションにおける、CRUD操作の確認方法について解説します。

## Mutation(ユーザー追加) + Query確認

まずはユーザーデータを追加してみます。以下アドレスをブラウザで実行し、Next.jsのログを確認します。

### 接続アドレス

```
http://[Webアプリケーション起動中のIPアドレス]:3000/mutation/add
```

### レスポンスデータ(例)

成功時のレスポンスは以下のようになります。

```
Response:  {
  id: 1,
  name: 'tester',
  email: 'tester@example.com',
  createdAt: '2024-03-17T06:58:49.000Z',
  updatedAt: '2024-03-17T06:58:49.000Z'
}
```

次にユーザーデータを取得し、ユーザーデータが追加されたか確認します。

### 接続アドレス

```
http://[Webアプリケーション起動中のIPアドレス]:3000/query
```

### レスポンスデータ(例)

追加されたユーザー情報が以下のように表示されれば成功です。

```
Response:  [
  {
    id: 1,
    name: 'tester',
    email: 'tester@example.com',
    createdAt: '2024-03-17T06:58:49.000Z',
    updatedAt: '2024-03-17T06:58:49.000Z'
  }
]
```

## Mutation(パスワード更新) + Query確認

次はユーザーデータのパスワードを更新してみます。以下アドレスをブラウザで実行し、Next.jsのログを確認します。
(今回はpasswordの属性も返すように一時的に修正します。終わり次第元に戻します。)

### 接続アドレス

```
http://[Webアプリケーション起動中のIPアドレス]:3000/mutation/update
```

### レスポンスデータ(例)

成功時のレスポンスは以下のようになります。

```
Response:  {
  id: 1,
  name: 'tester',
  email: 'tester@example.com',
  password: 'testertestertester',
  createdAt: '2024-03-17T06:58:49.000Z',
  updatedAt: '2024-03-17T07:05:42.000Z'
}
```

次にユーザーデータを取得し、ユーザーデータのパスワードが変更されたか確認します。
(今回はpasswordの属性も返すように一時的に修正します。終わり次第元に戻します。)

### 接続アドレス

```
http://[Webアプリケーション起動中のIPアドレス]:3000/query
```

### レスポンスデータ(例)

レスポンスデータのパスワードが変更されていたら成功となります。

```
Response:  [
  {
    id: 1,
    name: 'tester',
    email: 'tester@example.com',
    password: 'testertestertester',
    createdAt: '2024-03-17T06:58:49.000Z',
    updatedAt: '2024-03-17T07:05:42.000Z'
  }
]
```

## Mutation(ユーザー削除) + Query確認

最後にユーザーデータを削除してみます。以下アドレスをブラウザで実行し、Next.jsのログを確認します。

### 接続アドレス

```
http://[Webアプリケーション起動中のIPアドレス]:3000/mutation/delete
```

### レスポンスデータ(例)

成功時のレスポンスは以下のようになります。

```
Response:  {
  id: 1,
  name: 'tester',
  email: 'tester@example.com',
  createdAt: '2024-03-17T06:58:49.000Z',
  updatedAt: '2024-03-17T07:05:42.000Z'
}
```

次にユーザーデータを取得し、ユーザーが削除されたか確認します。

### 接続アドレス

```
http://[Webアプリケーション起動中のIPアドレス]:3000/query
```

### レスポンスデータ(例)

レスポンスデータが空になっていたら成功となります。

```
Response:  []
```

# 最後に

Next.jsとGraphQLを使って基本的なCRUD操作の実装を探求しました。今後はGraphQLサーバー側はCORS対策やセキュリティ強化、Next.js側はフォーム操作からのGraphQL操作、バリデーション強化、セキュリティ強化を図るとより良いWebアプリケーションに近づくと考えます。Apollo ClientやApollo Serverについて様々な方法があるので、より知見を深めていく所存です。今回扱わなかったサブスクリプションは今後検討していく予定です。

最後までお読みいただきありがとうございました！
