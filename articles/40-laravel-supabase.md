---
title: "LaravelでSupabaseのOAuth認証を設定する方法"
emoji: "🗝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["php", "laravel", "supabase", "github", "oauth"]
published: false
---

# はじめに

最近、Next.jsでSupabaseのOAuth（GitHub）認証を使用していました。Next.js側では、Supabaseの提供するライブラリを使用して、簡単に認証機能を実装できました。しかし、ふと疑問に思ったのは、同じことをLaravelでどのように実現するのかという点です。

今回は、LaravelでSupabaseのOAuth認証をどのように設定し、実装するのかを試してみることにしました。試した記録を記事として残します。

# Supabaseとは

オープンソースのバックエンドサービスで、開発者が迅速かつ容易にデータベース駆動型のアプリケーションを構築できるように設計されています。Supabaseは、リアルタイムのデータベース、認証、ストレージ、エッジ機能など、多くの便利な機能を提供しています。

# Supabaseの認証機能のOAuthを使う理由

Supabaseには強力な認証機能があり、OAuthを利用することで以下のようなメリットがあります。

- 1. ユーザー体験の向上
  - 簡単なログインプロセス
    - ユーザーはGoogle、GitHub、Facebookなどの既存のアカウントを使ってログインできるため、新たにアカウントを作成する手間が省けます。
  - 高速なアクセス
    - ワンクリックでのログインが可能なため、ユーザーがアプリケーションに迅速にアクセスできます。

- 2. セキュリティの向上
  - 安全な認証
    - OAuthは広く使用されており、信頼性と安全性が高い認証プロトコルです。パスワードを直接扱わないため、セキュリティリスクが減少します。
  - 二要素認証（2FA）のサポート
    - 多くのOAuthプロバイダーは二要素認証をサポートしており、セキュリティをさらに強化できます。

- 3. 開発効率の向上
  - 簡単な実装
    - Supabaseは、OAuthプロバイダーとの統合を簡単にするためのツールとドキュメントを提供しており、実装が容易です。
  - メンテナンスの削減
    - ユーザー管理と認証に関する複雑な部分をOAuthプロバイダーに任せることで、開発者は他の機能に集中できます。

- 4. ユーザーデータの一元管理
  - 統一されたユーザープロファイル
    - ユーザーが複数のサービスで同じOAuthプロバイダーを使用している場合、統一されたプロファイルデータを取得しやすくなります。
  - 自動データ更新
    - OAuthプロバイダーによっては、ユーザーのプロフィール情報が変更された際に自動的に更新されるため、最新のデータを維持できます。

- 5. マーケティングおよび分析
  - ソーシャルログインデータの活用
    - ソーシャルメディアを利用したログインを提供することで、ユーザーの興味や行動に関するデータを収集しやすくなり、マーケティング戦略の改善に役立ちます。


# プロジェクトのバージョン

本記事で使用するプロジェクトのバージョンは以下の通りです：
(今回はLaravelの構築手順は割愛します。)

Laravel: 10.x
PHP: 8.0以上
Supabase: 最新バージョン
このバージョン情報を元に、以下の設定を進めていきます。

# SupabaseとGitHubの設定

まずはSupabse側とGitHub側の設定を行います。

1. Supabaseのダッシュボードにアクセスします。
2. メニューからAuthenticationへ移動します。
3. Providersへ移動し、GitHubを選択して有効にします。
4. GitHubにアクセスし、ログインした状態にします。
5. ユーザーアイコンを選択し、Settingsを開きます。
6. Developer settingsへ移動します。
7. OAuth Appsを選択し、新しいOAuthアプリケーションを作成します。
8. フォームに必要な情報を入力し、Register applicationボタンを押します。
  - Application name:
  - Homepage URL:
  - Authorization callback URL:
9. ClientIDとClient Secretをメモします。
10. Supabaseのダッシュボードに戻り、GitHubのClient IDとClient Secretを入力して保存します。
11. SupabaseのAuthenticationのURL Configurationに移動し、Site URLとRedirect URLを入力して保存します。
  - ローカル環境の場合: http://localhost:[使用ポート]
  - デプロイ環境の場合: デプロイ先のURL

# 必要なパッケージの追加

LaravelでOAuth認証を実装するために、以下のパッケージを追加します。これにより、外部サービスとのHTTP通信を簡単に行えるようになります。

```bash
# GuzzleHTTPパッケージをインストール
composer require guzzlehttp/guzzle
# Laravel Socialiteパッケージをインストール
composer require laravel/socialite
```

- Guzzleとは
  - PHP用のHTTPクライアントで、APIとの通信を行うために広く使用されているライブラリ

- Socialiteとは
  - 各種OAuthプロバイダとの統合を容易にするためのライブラリで、GitHub、Google、Facebookなどの外部サービスとの認証をシンプルに行えるようにします。

# 各環境変数の設定

SupabaseとGitHubの認証およびデータベース接続のために、必要な環境変数を.envファイルに設定します。

## Supabase の環境変数の設定

まず、SupabaseプロジェクトのURLとAPIキーを設定します。これにより、SupabaseのAPIを利用するための認証が行えます。

```:.env
SUPABASE_URL=<SupabaseプロジェクトのURL>
SUPABASE_KEY=<SupabaseプロジェクトのAPI_KEY>
```

## PostgreSQL の環境変数の設定

次に、SupabaseのPostgreSQLデータベースへの接続情報を設定します。これにより、LaravelアプリケーションがSupabaseのデータベースと通信できるようになります。

```:.env
DB_CONNECTION=pgsql
DB_HOST=db.<unique-id>.supabase.co
DB_PORT=5432
DB_DATABASE=postgres
DB_USERNAME=postgres
DB_PASSWORD=<Supabaseのデータベースのパスワード>
```

## Supabase OAuth 設定の追加

最後に、GitHub認証のためのOAuth設定を追加します。これにより、ユーザーがGitHubアカウントを使用してログインできるようになります。

```:.env
GITHUB_CLIENT_ID=<GitHubクライアントID>
GITHUB_CLIENT_SECRET=<GitHubのクライアントシークレット>
GITHUB_REDIRECT_URI=https://[supabase]/auth/github/callback
```

# Webアプリケーション側の設定

ここでは、Laravelアプリケーションにおける設定を行います。まず、Laravel Socialiteのサービスプロバイダーを設定し、その後、GitHub認証用のサービス設定を追加します。

## Socialite サービスプロバイダーの設定

Laravelの設定ファイルにSocialiteのサービスプロバイダーとファサードを追加します。これにより、Socialiteの機能を利用できるようになります。

### サービスプロバイダーの追加

config/app.phpファイルを開き、providers配列にSocialiteサービスプロバイダーを追加します。

```php:config/app.php
'providers' => [
    // 他のサービスプロバイダーの後に追加
    Laravel\Socialite\SocialiteServiceProvider::class,
],
```

### ファサードの追加

同じくconfig/app.phpファイルのaliases配列に、Socialiteのファサードを追加します。

```php:config/app.php
'aliases' => [
    // 他のファサードの後に追加
    'Socialite' => Laravel\Socialite\Facades\Socialite::class,
],
```

これにより、アプリケーション内でSocialiteファサードを使用できるようになります。

## Socialite サービス設定の追加

次に、Socialiteを使用してGitHub認証を行うための設定を追加します。これにより、GitHub OAuthを通じてユーザー認証が行えるようになります。

config/services.phpファイルに、GitHub用の設定を追加します。

```php:config/services.php
'github' => [
    'client_id' => env('GITHUB_CLIENT_ID'),
    'client_secret' => env('GITHUB_CLIENT_SECRET'),
    'redirect' => env('GITHUB_REDIRECT_URI'),
],
```

これにより、GITHUB_CLIENT_ID、GITHUB_CLIENT_SECRET、およびGITHUB_REDIRECT_URIの環境変数を使用して、GitHub認証が設定されます。

# HTTPS 強制リダイレクト設定

本番環境でのセキュリティを強化するために、すべてのHTTPリクエストをHTTPSにリダイレクトする設定を行います。これにより、ユーザーのデータが暗号化され、安全に通信されます。

## ミドルウェアの追加

まず、HTTPS強制リダイレクトを行うミドルウェアを作成し、LaravelのグローバルHTTPミドルウェアスタックに追加します。

App\Http\Kernel.phpファイルを開き、protected $middleware配列に以下の行を追加します。

```php:App/Http/Kernel.php
    protected $middleware = [
        \App\Http\Middleware\TrustProxies::class,
        \Illuminate\Http\Middleware\HandleCors::class,
        \App\Http\Middleware\PreventRequestsDuringMaintenance::class,
        \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
        \App\Http\Middleware\TrimStrings::class,
        \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
        \App\Http\Middleware\ForceHttpToHttps::class, // ここに追加
    ];
```

## HTTPS強制リダイレクトミドルウェアの作成

次に、HTTPSリダイレクトを実行するミドルウェアを作成します。

App\Http\MiddlewareディレクトリにForceHttpToHttps.phpというファイルを作成し、以下のコードを追加します。

```php:App/Http/Middleware/ForceHttpToHttps.php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

/**
 * Class ForceHttpToHttps
 *
 * ミドルウェアは、リクエストがHTTPで送信された場合にHTTPSにリダイレクトします。
 * これにより、本番環境での通信を安全に保ちます。
 *
 * @package App\Http\Middleware
 */
class ForceHttpToHttps
{
    /**
     * ハンドルメソッドは、リクエストが本番環境でHTTPを使用している場合にHTTPSにリダイレクトします。
     *
     * @param \Illuminate\Http\Request $request
     * @param \Closure $next
     * @return \Symfony\Component\HttpFoundation\Response
     */
    public function handle(Request $request, Closure $next): Response
    {
        // 本番環境かつHTTPでアクセスされた場合にHTTPSへリダイレクト
        if (\App::environment(['production']) 
            && $_SERVER["HTTP_X_FORWARDED_PROTO"] != 'https') {
            return redirect()->secure($request->getRequestUri());
        }

        // リクエストを次のミドルウェアへ渡す
        return $next($request);
    }
}
```

# マイグレーションの設定

ここでは、GitHub認証をサポートするために、ユーザーテーブルに必要なカラムを追加します。これにより、ユーザーのGitHub情報をデータベースに保存できるようになります。

## 認証ユーザーのマイグレーションの追加

まず、新しいマイグレーションファイルを作成します。このファイルでは、usersテーブルにGitHub関連のフィールドを追加します。

```bash
php artisan make:migration add_github_fields_to_users_table --table=users
```

## マイグレーションファイルの編集

次に、生成されたマイグレーションファイルを編集して、必要なカラムを追加します。

database/migrations/xxxx_xx_xx_xxxxxx_add_github_fields_to_users_table.php ファイルを開き、以下のコードを追加します。

```php:database/migrations/add_github_fields_to_users_table.php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

/**
 * マイグレーションクラスは、usersテーブルにGitHub認証用のカラムを追加および削除する。
 */
return new class extends Migration
{
    /**
     * マイグレーションを実行する。このメソッドは、usersテーブルに
     * 'github_id', 'avatar', 'token', 'password'の各カラムを追加する。
     *
     * @return void
     */
    public function up(): void
    {
        Schema::table('users', function (Blueprint $table) {
            $table->string('github_id')->nullable();
            $table->string('avatar')->nullable();
            $table->string('token')->nullable();
            $table->string('password')->nullable()
        });
    }

    /**
     * マイグレーションをロールバックする。このメソッドは、usersテーブルから
     * 'github_id', 'avatar', 'token', 'password'の各カラムを削除する。
     *
     * @return void
     */
    public function down(): void
    {
        Schema::table('users', function (Blueprint $table) {
            $table->dropColumn(['github_id', 'avatar', 'token', 'password']);
        });
    }
};
```

## マイグレーションの実行

マイグレーションファイルを編集した後、マイグレーションを実行して、Supabaseのデータベースにテーブルを反映させます。

```bash
php artisan migrate
```

これにより、usersテーブルに必要なカラムが追加され、GitHub認証に必要な情報が保存できるようになります。

# Routing の設定

まず、GitHub認証に関するルーティングを設定します。これにより、ユーザーがGitHubを通じてログインする際のエンドポイントが定義されます。

```php:routes/web.php
<?php

use App\Http\Controllers\Auth\GitHubController;
use Illuminate\Support\Facades\Route;

// GitHub認証ページへのリダイレクト
Route::get('auth/github', [GitHubController::class, 'redirectToProvider']);
// GitHubからのコールバック処理
Route::get('auth/v1/callback', [GitHubController::class, 'handleProviderCallback']);
// ログアウト処理
Route::get('logout', [GitHubController::class, 'logout']);
```

# Controller の設定

次に、GitHub認証を処理するコントローラーを設定します。ここでは、ユーザーをGitHub認証ページにリダイレクトし、GitHubからの認証情報を処理してユーザーをログインさせます。

## GitHubController の作成

App\Http\Controllers\AuthディレクトリにGitHubController.phpファイルを作成し、以下のコードを追加します。

```php:App/Http/Controllers/Auth/GitHubController.php
<?php

namespace App\Http\Controllers\Auth;

use Laravel\Socialite\Facades\Socialite;
use App\Http\Controllers\Controller;
use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Str;
use Illuminate\Support\Facades\Log;
use GuzzleHttp\Client;

/**
 * GitHub認証を処理するコントローラ
 */
class GitHubController extends Controller
{
    /**
     * GitHubの認証ページにユーザーをリダイレクトします。
     *
     * @return \Illuminate\Http\Response
     */
    public function redirectToProvider()
    {
        // Socialiteを使用してGitHubの認証ページにリダイレクトする
        return Socialite::driver('github')->redirect();
    }

    /**
     * GitHubからユーザー情報を取得します。
     *
     * @return \Illuminate\Http\Response
     */
    public function handleProviderCallback()
    {
        try {
            // GitHubからユーザー情報を取得
            $githubUser = Socialite::driver('github')->stateless()->user();
            
            // ユーザーが既に存在するかチェック
            $user = User::where('github_id', $githubUser->id)
                        ->orWhere('email', $githubUser->email)
                        ->first();

            if ($user) {
                // 既存ユーザーがGitHub IDを持っていない場合は更新
                if (is_null($user->github_id)) {
                    $user->github_id = $githubUser->id;
                    $user->save();
                }
                
                Auth::login($user, true);
            } else {
                // 新規ユーザーを作成してログイン
                $user = User::create([
                    'name' => $githubUser->name,
                    'email' => $githubUser->email,
                    'github_id' => $githubUser->id,
                    'avatar' => $githubUser->avatar,
                    'password' => bcrypt(Str::random(16)),
                ]);

                Auth::login($user, true);
            }

            // ユーザーがログインしていることを確認するためのログ
            Log::info('User logged in', ['user' => $user, 'session' => session()->all()]);

            // ホームページにリダイレクト
            return redirect('/');
        } catch (\Exception $e) {
            // エラーログを記録し、認証失敗時にリダイレクト
            Log::error('Authentication failed', ['error' => $e->getMessage()]);
            return redirect('/')->withErrors(['login' => 'Authentication failed.']);
        }
    }

    /**
     * ユーザーをLaravelからログアウトさせます。
     *
     * @return \Illuminate\Http\Response
     */
    public function logout(Request $request)
    {
        try {
            Log::info('Starting logout process');

            // Laravelからのログアウト処理
            Auth::logout();
            $request->session()->invalidate();
            $request->session()->regenerateToken();
    
            Log::info('User logged out successfully');
    
            return redirect('/')->with('status', 'Logout successful');
        } catch (\Exception $e) {
            Log::error('Logout failed', ['error' => $e->getMessage()]);
            return redirect('/')->withErrors('Logout failed: ' . $e->getMessage());
        }
    }
}
```

# Blade の設定

最後に、GitHub認証のリンクを表示するためのBladeテンプレートを設定します。これにより、ユーザーがGitHubアカウントを使用してログインおよびログアウトできるようになります。

## Bladeテンプレートの編集

resources/views/welcome.blade.phpファイルを開き、以下のコードを追加または編集します。このテンプレートは、ユーザーの認証状態に応じて異なるメッセージとリンクを表示します。

```php:routes/resources/views/welcome.blade.php
<div class="sm:fixed sm:top-0 sm:right-0 p-6 text-right z-10">
    @auth
        <!-- ユーザーがログインしている場合に表示 -->
        <p>{{ Auth::user()->name }} is logged in</p>
        <a href="{{ url('logout') }}"
            class="font-semibold text-gray-600 hover:text-gray-900 dark:text-gray-400 dark:hover:text-white focus:outline focus:outline-2 focus:rounded-sm focus:outline-red-500">
            Logout with GitHub
        </a>
    @else
        <!-- ユーザーがログインしていない場合に表示 -->
        <p>Not logged in</p>
        <a href="{{ url('auth/github') }}"
            class="font-semibold text-gray-600 hover:text-gray-900 dark:text-gray-400 dark:hover:text-white focus:outline focus:outline-2 focus:rounded-sm focus:outline-red-500">
            Login with GitHub
        </a>
    @endauth
</div>
```

# 実際にOAuth認証を試してみる

まずは、ローカル環境またはデプロイした環境（例：Heroku）でサインインとログアウトを試してみましょう。

## サインインの手順

1. ブラウザでアプリケーションのURLにアクセスします。
2. サインインボタンをクリックします。
3. GitHubの認証ページにリダイレクトされるので、GitHubアカウントでログインします。
4. ログインが成功すると、アプリケーションに戻り、ユーザー名が表示されます。

以下は、各ステップのスクリーンショットです。

- サインイン前

![サインイン前](https://storage.googleapis.com/zenn-user-upload/69ec93de0c07-20240527.png)

- サインイン後

サインインが成功すると、ユーザーのGitHubアカウント名が表示されます。

![サインイン後](https://storage.googleapis.com/zenn-user-upload/a69f89ea20c5-20240527.png)

## ログアウトの手順

1. ユーザー名の横にあるログアウトボタンをクリックします。
2. ログアウトが成功すると、サインイン前の画面に戻ります。
 
- ログアウト後

ログアウト後の画面です。「Login with GitHub」ボタンが再び表示されます。

![ログアウト後](https://storage.googleapis.com/zenn-user-upload/69ec93de0c07-20240527.png)

## データベース側の確認

Supabaseのデータベース側で、ユーザー情報が正しく保存されていることを確認します。ユーザーがGitHubで認証した後、usersテーブルにGitHub ID、アバター、トークンなどの情報が保存されています。

![データベースの確認](https://storage.googleapis.com/zenn-user-upload/129213dd5114-20240527.png)

# 終わりに

SupabaseのOAuth認証（GitHub）をLaravelと連携する方法を学びました。SupabaseとGitHubの設定、必要なパッケージのインストール、環境変数の設定、マイグレーション、ルーティングとコントローラーの設定、そしてBladeテンプレートの編集までの手順を紹介しました。

これにより、GitHubを使ったユーザー認証がLaravelアプリケーションで実現できました。SupabaseのOAuth認証を活用することで、ユーザーはGitHubアカウントでログインすることができます。。今後、Supabaseをより深く活用し、さらなる機能拡張を図っていきたいと考えています。

最後まで読んでいただき、ありがとうございました！

# URL

- Supabase

https://supabase.com/

- 認証機能の解説

https://kinsta.com/jp/blog/laravel-authentication/

- HTTPSリダイレクト

https://qiita.com/kakudaisuke/items/bdbe1b1985e73cb3299f