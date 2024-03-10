---
title: "GraphQLをNestJSで使って学ぶ"
emoji: "📶"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["graphql", "nestjs"]
published: false
---

# はじめに

REST APIやWeb API以外の方法でデータを取り扱う知識を深めるために、GraphQLについて学びました。この記事では、GraphQLの基礎からサンプルコードまで載せていきます。

# GraphQLとは

APIのためのクエリ言語です。

Facebookによって開発され、2015年にオープンソースとして公開されました。REST等の従来のAPI設計手法と比較して、**データの取得方法をより柔軟にし、エンドポイントの数を減らす**ことができます。これは、クライアントが必要とするデータの構造を正確に指定し、**一回のリクエストで必要な情報だけを取得する**ことができるため、アプリケーションのパフォーマンスを向上させることができます。

# GraphQLの主な特徴

- **データの取得精度**: クライアントが必要とするデータの形状をリクエストに厳密に指定することで、過剰または不足するデータを取得することなく、必要なデータだけを取得できます。

- **単一エンドポイント**: GraphQL APIは通常、単一のエンドポイントを使用します。クライアントは、異なるタイプのクエリやミューテーション（データの変更を行う操作）をこのエンドポイントに送信することで、さまざまなデータを取得または変更できます。

- **型システム**: GraphQLは強力な型システムを持っており、APIが扱うデータの形状（型）を事前に定義します。これにより、クエリの有効性を事前に検証することができ、エラーの可能性を減らすことができます。

- **自己文書化**: GraphQLの型システムにより、APIは自己文書化される性質を持ちます。つまり、APIの構造と利用可能な操作を簡単に理解することができます。

GraphQLは、Webアプリケーションやモバイルアプリケーションなど、多様なクライアントに対応したデータ取得ニーズがある場合に特に有効です。

# APIの選択パターン

GraphQLとREST APIの間で選択する際の考慮すべき点を以下にまとめます。

## GraphQLを推奨するパターン

- **高度に柔軟なデータ取得が必要な場合**: クライアントが必要なデータを正確に指定し、不要なデータの取得を避けたい場合に適しています。

- **複数のデータソースからのデータを統合する場合**: 単一のクエリで複数のデータソースからデータを取得し、統合する必要がある複雑なアプリケーションに最適です。

- **リアルタイムデータの更新が求められる場合**: サブスクリプションを利用してリアルタイムでのデータ更新を扱うアプリケーションに適しています。

- **迅速な開発サイクルが求められる場合**: フロントエンドとバックエンドの開発が密接に連携し、迅速にプロトタイピングやイテレーションを行いたい場合に有効です。

## REST APIを推奨するパターン

- **シンプルなデータ取得や操作が中心の場合**: CRUD操作が主体で、データの構造が比較的固定されている場合に適しています。

- **HTTPキャッシングを活用したい場合**: 効率的なキャッシング戦略を通じてAPIのパフォーマンスを向上させたい場合にRESTが適しています。

- **広範なクライアントサポートが必要な場合**: RESTは広く採用されており、多様なプラットフォームやライブラリでのサポートが確立されています。

- **技術スタックがRESTに合致している場合**: 既存のシステムや開発チームの経験がRESTに適している場合、継続してRESTを使用することが合理的です。

GraphQLはデータの柔軟性と複雑な要件に対応しやすいですが、設計と実装にはより多くの考慮が必要です。一方、RESTはよりシンプルで広く理解されていますが、複雑なデータ要件には適応しづらいことがあります。

# GraphQLの操作タイプ

GraphQLでデータを操作する際には、主に3つのタイプが利用されます。

## クエリ(Query)

クエリは、データの読み取り専用の操作です。クライアントが必要とする正確なデータをデータベースから取得できるように設計されており、REST APIにおけるGETリクエストの役割を果たします。GraphQLのクエリは非常に柔軟で、クライアントが正確に必要なデータのみを指定して取得できるため、無駄なデータの取得を避けることができます。

## ミューテーション(Mutation)

ミューテーションは、データの作成、更新、削除など、サーバー上のデータを変更するための操作です。これはREST APIのPOST、PUT、DELETEリクエストに相当します。ミューテーションを通じて、クライアントはデータの変更を行い、その結果を即座に反映させることができます。

## サブスクリプション(Subscription)

サブスクリプションはリアルタイムでのデータ変更を購読する操作です。ウェブソケットを使用して実装されることが多く、指定されたデータに変更があると、その情報がサーバーからクライアントへ自動的にプッシュされます。これにより、リアルタイム通信を必要とするアプリケーション、例えばチャットアプリや、即時性が求められるデータ表示に効果を発揮します。

# GraphQLでのスキーマ定義

スキーマ定義は、GraphQL APIが取り扱うデータの型を定義することで、APIの構造を明確にします。以下はユーザーデータのCRUD操作を例にしたスキーマ定義です。

## User型の定義

ユーザー情報を表すUser型を定義し、各フィールド（id, name, email, password, createdAt, updatedAt）のデータ型を指定します。これにより、APIがどのようなユーザー情報を扱うかが明確になります。

```graphql:schema.gql
type User {
  id: Int!
  name: String!
  email: String!
  password: String!
  createdAt: DateTime!
  updatedAt: DateTime!
}
```

## Query（ユーザーデータの取得）

すべてのユーザー情報を取得するためのクエリgetUsersを定義します。このクエリを実行することで、登録されているすべてのユーザー情報が取得できます。

```graphql:schema.gql
type Query {
  getUsers: [User!]
}
```

## Mutation（ユーザーデータの追加、更新、削除）

ユーザーの追加、パスワードの変更、ユーザーの削除を行うためのMutationを定義します。

```graphql:schema.gql
type Mutation {
  # ユーザーの追加
  createUser(createUserInput: CreateUserInput!): User!
  # パスワードの変更
  updatePassword(updateUserPasswordInput: UpdateUserPasswordInput!): User!
  # ユーザーの削除
  deleteUser(deleteUserInput: DeleteUserInput!): User!
}

# ユーザーの追加(入力用)
input CreateUserInput {
  name: String!
  email: String!
  password: String!
  confirmPassword: String!
}

# パスワードの変更(入力用)
input UpdateUserPasswordInput {
  email: String
  password: String
  newPassword: String
  confirmPassword: String
}

# ユーザーの削除(入力用)
input DeleteUserInput {
  email: String!
}
```

# NestJSでGraphQLを組込み

NestJSを使用して、GraphQLでデータを取得するプロセスを説明します。データベースにはPostgreSQLを使用し、ORMにはPrismaを採用します。NestJSのインストール方法については、[NestJSの公式ドキュメント](https://docs.nestjs.com/)を参照してください。この記事ではPostgreSQLやPrismaの設定については扱いません。

## 必要なパッケージのインストール

GraphQLをNestJSプロジェクトに統合するために必要なパッケージをインストールします。これには、NestJS用のGraphQLパッケージ(@nestjs/graphql)、Apolloサーバー(@nestjs/apolloと@apollo/server)、GraphQL自体(graphql)が含まれます。また、データバリデーションを強化するために、class-validatorとclass-transformerもインストールします。

```bash
# GraphQLで必要なパッケージをインストール
npm i @nestjs/graphql @nestjs/apollo @apollo/server graphql
# その他パッケージをインストール
npm i class-validator class-transformer
```

## モジュール、リゾルバー、サービスの生成

プロジェクトにユーザー関連の機能を追加するため、次のコマンドを実行して、必要なモジュール、リゾルバー、サービスを生成します。

```bash
# ユーザーモジュール追加
nest g module user
# ユーザーリゾルバークラスの追加
nest g resolver user --no-spec
# ユーザーサービスクラスの追加
nest g service user --no-spec
```

これらのコマンドは、NestJSのCLIを使用して、アプリケーションの構造を効率的に構築するためのものです。--no-specオプションは、テスト用のスペックファイルを生成しないようにするために使用されます。

# NestJSプロジェクトにGraphQLを導入

NestJSのAppModuleにGraphQLModuleを導入して、GraphQLを有効化します。以下のコードは、AppModuleにGraphQLを組み込んだコードになります。ここでは、Apollo ServerをGraphQLのドライバーとして使用し、スキーマファイルの自動生成、GraphQL Playgroundの有効化を設定しています。

```ts:app.module.ts
import { Module } from '@nestjs/common';
import { GraphQLModule } from '@nestjs/graphql';
import { ApolloDriver, ApolloDriverConfig } from '@nestjs/apollo';
import { join } from 'path';
import { PrismaModule } from './prisma/prisma.module';
import { UserModule } from './user/user.module';

@Module({
  imports: [
    GraphQLModule.forRoot<ApolloDriverConfig>({
      driver: ApolloDriver,
      autoSchemaFile: join(process.cwd(), 'src/schema.gql'),
      playground: true,
    }),
    PrismaModule,
    UserModule,    
  ],
})
export class AppModule {}
```

この設定により、NestJSアプリケーションにGraphQLのサポートが追加されます。autoSchemaFileプロパティによって、スキーマファイルはプロジェクトのsrcディレクトリに自動的に生成されます。playgroundがtrueに設定されているため、開発中はGraphQL Playgroundを介してクエリを簡単にテストできます。

# リゾルバーの実装

ユーザーに関連する操作を処理するUserResolverを実装します。このリゾルバーは、ユーザーの取得、追加、パスワードの更新、削除といった処理を行います。

```ts:user.resolver.ts
import { Args, Int, Mutation, Query, Resolver } from '@nestjs/graphql';
import { User } from '@prisma/client';
import { UserService } from './user.service';
import { User as UserModel } from './models/user.model';
import { CreateUserInput } from './dto/createUser.dto';
import { UpdateUserPasswordInput } from './dto/updateUserPassword.dto';
import { DeleteUserInput } from './dto/deleteUser.dto';

/**
 * ユーザーリゾルバー
 */
@Resolver()
export class UserResolver {
    // DI
    constructor(private readonly userService: UserService) {}

    /**
     * ユーザー取得
     * @returns ユーザーデータ[]
     */
    @Query(() => [UserModel], { nullable: true })
    async getUsers(): Promise<User[]> {
        return await this.userService.getUsers();
    }

    /**
     * ユーザーの追加
     * @param createUserInput 
     * @returns ユーザーデータ
     */
    @Mutation(() => UserModel)
    async createUser(
        @Args('createUserInput') createUserInput: CreateUserInput,
    ): Promise<User> {
        const user = await this.userService.createUser(createUserInput);
        pubSub.publish('userCreated', { userCreated: user });
        return user;
    }

    /**
     * パスワードの更新
     * @param updateUserPasswordInput 
     * @returns ユーザーデータ
     */
    @Mutation(() => UserModel)
    async updatePassword(
        @Args('updateUserPasswordInput') updateUserPasswordInput: UpdateUserPasswordInput,
    ): Promise<User> {
        return await this.userService.updatePassword(updateUserPasswordInput);
    }

    /**
     * ユーザーの削除
     * @param deleteUserInput
     * @returns ユーザーデータ
     */
    @Mutation(() => UserModel)
    async deleteUser(
        @Args('deleteUserInput') deleteUserInput: DeleteUserInput,
    ): Promise<User> {
        return await this.userService.deleteUser(deleteUserInput);
    }
}
```

# サービスの実装

UserServiceは、Prismaを使用してデータベース操作を抽象化し、リゾルバーからの要求を処理します。

```ts:user.service.ts
import { Injectable, NotFoundException } from '@nestjs/common';
import { User } from '@prisma/client';
import { PrismaService } from '../prisma/prisma.service';
import { CreateUserInput } from './dto/createUser.dto';
import { UpdateUserPasswordInput } from './dto/updateUserPassword.dto';
import { DeleteUserInput } from './dto/deleteUser.dto';

/**
 * ユーザーサービス
 */
@Injectable()
export class UserService {
    // コンストラクタにDI
    constructor(private readonly prismaService: PrismaService) {}

    /**
     * ユーザー全取得
     * @returns ユーザーデータ配列
     */
    async getUsers(): Promise<User[]> {
        return await this.prismaService.user.findMany();
    }

    /**
     * ユーザー取得
     * @param email
     * @returns ユーザーデータ
     */
    async getUser(
        email: string,
    ): Promise<User> {
        return await this.prismaService.user.findUnique({
            where: { email },
        });
    }

    /**
     * ユーザー追加
     * @param createUserInput 
     * @returns ユーザーデータ
     */
    async createUser(
        createUserInput: CreateUserInput,
    ): Promise<User> {
        const {name, email, password} = createUserInput;
        
        return await this.prismaService.user.create({
            data: {
                name,
                email,
                password,
            }
        });
    }

    /**
     * パスワードの更新
     * @param updateUserPasswordInput 
     * @returns ユーザーデータ
     */
    async updatePassword(
        updateUserPasswordInput: UpdateUserPasswordInput,
    ): Promise<User> {
        const {email, password, newPassword} = updateUserPasswordInput;
        
        // ユーザー存在有無確認
        const user = await this.getUser(email);
        // パスワード確認
        if (user.password !== password) {
            throw new NotFoundException('パスワードが一致しませんでした');
        }
        
        // パスワード更新
        return await this.prismaService.user.update({
            data: {
                password: newPassword
            },
            where: { email },
        });
    }

    /**
     * ユーザー削除
     * @param deleteUserInput
     * @returns ユーザーデータ
     */
    async deleteUser(
        deleteUserInput: DeleteUserInput,
    ): Promise<User> {
        const {email} = deleteUserInput;
        
        return await this.prismaService.user.delete({
            where: {email},
        });
    }
}
```

# モデルの実装

GraphQLで使用するユーザーモデルを定義します。このモデルはGraphQLスキーマで使用され、クライアントに公開されるデータの構造を定義します。

```ts:user.model.ts
import { Field, Int, ObjectType } from "@nestjs/graphql";

/**
 * ユーザーモデル
 */
@ObjectType()
export class User {

    @Field(() => Int)
    id: number;

    @Field()
    name: string;

    @Field()
    email: string;

    @Field()
    password: string;

    @Field()
    createdAt: Date;

    @Field()
    updatedAt: Date;
}
```

# DTOの実装

データ転送オブジェクト（DTO）は、クライアントからサーバーへデータを送信する際の構造を定義します。これにはバリデーションルールも含まれ、不正なデータの送信を防ぎます。

:::details ユーザー追加用のDTO
```ts:createUser.dto.ts
import { Field, InputType } from "@nestjs/graphql";
import { IsEmail, IsNotEmpty, MaxLength, MinLength } from "class-validator";
import { IsMatch } from "../decorator/customDecorator";

/**
 * ユーザー作成[DTO]
 */
@InputType()
export class CreateUserInput {

    @Field()
    @IsNotEmpty()
    name: string;

    @Field()
    @IsEmail()
    email: string;

    @Field()
    @MinLength(8)
    @MaxLength(20)
    password: string;

    @Field()
    @MinLength(8)
    @MaxLength(20)
    @IsMatch('password', { message: 'Password and confirm password do not match' })
    confirmPassword: string;
}
```
:::

:::details パスワード更新用のDTO
```ts:updateUserPassword.dto.ts
import { Field, InputType, Int } from "@nestjs/graphql";
import { IsEmail, IsNotEmpty, IsOptional, isNotEmpty } from "class-validator";
import { IsMatch } from "../decorator/customDecorator";

/**
 * ユーザー更新[DTO]
 */
@InputType()
export class UpdateUserPasswordInput {

    @Field({ nullable: true })
    @IsNotEmpty()
    @IsEmail()
    @IsOptional()
    email?: string;

    @Field({ nullable: true })
    @IsNotEmpty()
    @IsOptional()
    password?: string;

    @Field({ nullable: true })
    @IsNotEmpty()
    @IsOptional()
    newPassword?: string;

    @Field({ nullable: true })
    @IsNotEmpty()
    @IsOptional()
    @IsMatch('newPassword', { message: 'NewPassword and confirm newPassword do not match' })
    confirmPassword?: string;
}
```
:::

:::details パスワード削除用のDTO
```ts:deleteUser.dto.ts
import { Field, InputType } from "@nestjs/graphql";
import { IsEmail, IsNotEmpty, IsOptional } from "class-validator";

/**
 * ユーザー削除[DTO]
 */
@InputType()
export class DeleteUserInput {

    @Field()
    @IsEmail()
    @IsNotEmpty()
    @IsOptional()
    email: string;
}
```
:::

# カスタムデコレータの実装

入力値の一致を確認するカスタムデコレータを実装します。
これは、例えばパスワードとパスワード確認フィールドが一致するかを検証する際に使用します。

:::details カスタムデコレータ
```ts:customDecorator.ts
import { registerDecorator, ValidationOptions, ValidationArguments } from 'class-validator';

/**
 * 一致するか(デコレータ)
 * @param property 
 * @param validationOptions 
 * @returns 
 */
export function IsMatch(property: string, validationOptions?: ValidationOptions) {
  return function (object: Object, propertyName: string) {
    registerDecorator({
      name: 'isMatch',
      target: object.constructor,
      propertyName: propertyName,
      constraints: [property],
      options: validationOptions,
      validator: {
        validate(value: any, args: ValidationArguments) {
          const [relatedPropertyName] = args.constraints;
          const relatedValue = (args.object as any)[relatedPropertyName];
          return value === relatedValue;
        },
        defaultMessage(args: ValidationArguments) {
          return `${propertyName} and ${args.constraints[0]} don't match`;
        },
      },
    });
  };
}
```
:::

# バリデーションの全体適用

NestJSアプリケーションにおいてバリデーションを全体で有効にするには、main.tsファイルにValidationPipeをグローバルスコープで適用します。これにより、アプリケーションに送信される全ての入力データに対してバリデーションが行われ、データの整合性が保たれます。

:::details main.tsへ追加する設定
```ts:main.ts
import { NestFactory } from '@nestjs/core';
import { ValidationPipe } from '@nestjs/common';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.enableCors();
  app.useGlobalPipes(new ValidationPipe()); // ここを追加
  await app.listen(3000);
}
bootstrap();
```
:::

# サーバーの起動とGraphQLサーバーの確認

開発環境でサーバーを起動するには、以下のコマンドを使用します。

```bash
npm run start:dev
```

これにより、src/schema.gqlファイルが自動的に生成され、GraphQLサーバーが起動します。schema.gqlはGraphQLのスキーマ情報を含むファイルで、APIで利用可能なクエリやミューテーションの定義が含まれます。

# GraphQL Playgroundの使用

GraphQL Playgroundは、開発中のGraphQL APIと対話するための強力なIDE（統合開発環境）です。NestJSでGraphQLサーバーを起動した後、以下の手順でAPIの動作を確認できます。

## ユーザーの追加

新しいユーザーをシステムに追加するには、左側のエディタに適切なGraphQLミューテーションを入力し、実行ボタン（右矢印）をクリックします。正しく追加されると、追加されたユーザーの情報が右側に表示されます。

![ユーザーの追加](https://storage.googleapis.com/zenn-user-upload/b57bbe63c843-20240310.png)

## ユーザー情報の取得

システム内のユーザー情報を取得するには、ユーザーの取得に関するクエリを左側のエディタに入力し、実行ボタンをクリックします。正しいクエリを実行すると、保存されているユーザー情報が表示されます。

![ユーザーデータの取得](https://storage.googleapis.com/zenn-user-upload/585401e3a8d8-20240310.png)

## パスワードの更新

ユーザーのパスワードを更新するには、パスワード更新のミューテーションを入力し、実行ボタンをクリックします。成功すると、更新されたユーザー情報が返されます。セキュリティ上の理由から、パスワード情報は通常、レスポンスに含めるべきではありません。

![パスワードの変更](https://storage.googleapis.com/zenn-user-upload/6f9a4cd4bb9b-20240310.png)

## ユーザーの削除

ユーザーを削除するには、削除関連のミューテーションを入力し、実行ボタンをクリックします。削除が成功すると、そのユーザーのIDが返されます。

![ユーザーの削除](https://storage.googleapis.com/zenn-user-upload/563e91b75078-20240310.png)

# サブスクリプションの導入

リアルタイムのデータ変更をフロントエンドに通知するために、NestJSとGraphQLでサブスクリプションを利用します。サブスクリプションを有効にするための設定は、app.module.tsのGraphQLModuleの設定内で行います。

## AppModuleでの設定

AppModuleのGraphQLModule設定にinstallSubscriptionHandlers: trueを追加し、サブスクリプションのサポートを有効にします。これにより、WebSocket経由でのリアルタイム通信が可能になります。

```ts:app.module.ts
@Module({
  imports: [
    GraphQLModule.forRoot<ApolloDriverConfig>({
      driver: ApolloDriver,
      autoSchemaFile: join(process.cwd(), 'src/schema.gql'),
      playground: true,
      installSubscriptionHandlers: true, // サブスクリプションハンドラのインストール
    }),
    PrismaModule,
    UserModule,    
  ],
})
export class AppModule {}
```

## ユーザーリゾルバーの変更

リゾルバー内でPubSubインスタンスを使用して、特定のイベントが発生したときにサブスクリプションを通じてデータを配信します。
例として、新しいユーザーが作成されたときにこれを発行する処理を追加します。

```ts:user.resolver.ts
import { Args, Int, Mutation, Query, Resolver, Subscription } from '@nestjs/graphql';
import { User } from '@prisma/client';
import { PubSub } from 'graphql-subscriptions';
import { UserService } from './user.service';
// (省略)

const pubSub = new PubSub(); // 追加

/**
 * ユーザーリゾルバー
 */
@Resolver(of => UserModel)
export class UserResolver {
    // (省略)

    /**
     * ユーザーの追加
     * @param createUserInput 
     * @returns ユーザーデータ
     */
    @Mutation(() => UserModel)
    async createUser(
        @Args('createUserInput') createUserInput: CreateUserInput,
    ): Promise<User> {
        const user = await this.userService.createUser(createUserInput);
        pubSub.publish('userCreated', { userCreated: user }); // 追加
        return user;
    }

    // 追加
    /**
     * サブスクリプション
     * @returns 
     */
    @Subscription(returns => UserModel)
    userCreated() {
        return pubSub.asyncIterator('userCreated');
    }
}
```

この設定により、クライアントはuserCreatedサブスクリプションを購読することができ、新しいユーザーが追加されるたびにリアルタイムでその情報を受け取ることが可能になります。これにより、リアルタイムでのデータ更新が求められるアプリケーションにおいて、ユーザーエクスペリエンスを向上させることができます。

# サブスクリプションの実行

## サブスクリプションの準備

GraphQL Playgroundで新しいタブを開き、「サブスクリプションの実行」用として、以下のサブスクリプションクエリを入力します。これにより、指定したイベント（この例ではユーザーが作成されるイベント）の通知を受け取る準備が整います。

![サブスクリプションの実行](https://storage.googleapis.com/zenn-user-upload/7118634e8a37-20240310.png)

## データの送信

別のタブを開いて、「ユーザーの追加」のMutationクエリを実行します。これは新しいユーザーをシステムに追加するクエリです。

![ユーザーの追加2](https://storage.googleapis.com/zenn-user-upload/b1336267e807-20240310.png)

## サブスクリプションによる通知の確認

ユーザーの追加操作を行った後、先に準備したサブスクリプションのタブに戻ります。新しく追加されたユーザーの情報がリアルタイムで通知されていることを確認できます。

![ユーザーの通知](https://storage.googleapis.com/zenn-user-upload/583cea259f96-20240310.png)

# さいごに

GraphQLを用いたデータ通信の基本から応用までを学びました。フロントエンドからGraphQLサーバーへのクエリ送信により、必要なデータを効率的にフェッチし、アプリケーションのパフォーマンスを最適化できます。柔軟なデータ取得、強力な型システム、自己文書化能力など、GraphQLが提供する特徴は魅力的です。使用する条件によって、REST APIとGraphQLを使い分けられるように知見を深めていく所存です。

最後までお読みいただき、ありがとうございました！

# 参考URL

https://docs.nestjs.com/

https://docs.nestjs.com/graphql/quick-start