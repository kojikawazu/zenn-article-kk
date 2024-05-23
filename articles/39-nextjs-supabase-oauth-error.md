---
title: "Supabaseでの認証ユーザー追加時にデータ挿入エラーの解決方法"
emoji: "💡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nextjs", "typescript", "supabase", "github", "oauth"]
published: true
---

# はじめに

Supabaseを使って認証ユーザーを追加し、そのユーザーの情報を別のテーブルに同時に挿入する機能を実装しようとしたところ、エラーに遭遇しました。このエラーを解決するための内容を記録として残します。

# やろうとしたこと

Next.jsの画面からサインインボタンを押下するとSupabaseのOAuth(GitHub)認証を利用し、サインインします。その後、サインイン成功をトリガーにProfileテーブルにデータの追加を試みました。

# 発生したエラー

Next.jsのWebアプリケーションを起動し、ブラウザからサインインを実行したところ、サーバーエラー(500)となりました。

```:URLに表示された内容を抜粋
#error=server_error&error_code=500&error_description=Database+error+saving+new+user
```

# 実行手順内容

手順は以下となります。

1. SupabaseのAuthenticationのProvidersにGitHubを有効化
2. GitHub側のDeveloper settingsのOAuth AppsにSupabase認証用を作成し、
   Supabase側に登録
3. Next.jsでOAuth認証によるサインイン成功
4. 次はSupabaseのTable EditorでProfileテーブルを追加
5. Supabaseの認証ユーザーでForeign keys作成
6. **DatabaseのFunctionsでProfileにデータを追加する関数作成**
7. **TriggersでOAuth認証でサインインするとProfileにもデータを追加するトリガーを作成**

```sql:TriggersをSQL Editorで実行した内容
create trigger create_new_profile_for_user
after insert on auth.users for each row
execute function create_profile_for_users();
```

8. ブラウザでNext.jsのWebアプリケーションにアクセスし、サインイン実行

# 原因はなに？

インターネットで調査したところ、このサイトが一番の近道でした。

https://github.com/orgs/supabase/discussions/21247

問題の原因は、認証を管理するためのデータベースロール (supabase_auth_admin) が、authスキーマ外のテーブルと対話しようとした際に発生する**権限エラー**でした。具体的には、auth.usersテーブルにトリガーを追加し、そのトリガーがauthスキーマ外のテーブルにデータを挿入しようとする場合に、supabase_auth_adminが必要な権限を持っていないためエラーが発生します。

# 解決方法はなに？

この問題を解決するための方法は以下の通りです。SupabaseのSQL Editorで以下の手順を実行します。

1. トリガーの削除：

- ダッシュボードのトリガーセクションでauthスキーマに含まれるトリガーをすべて削除します。これには、CASCADE修飾子を使用してトリガー関数を削除する方法が含まれます。

```sql
DROP FUNCTION <function name>() CASCADE;
```

2. セキュリティ定義者修飾子付き関数の再作成：

- 削除したトリガー関数をセキュリティ定義者修飾子を付けて再作成します。これにより、その関数は関数を作成したデータベースユーザー（通常はpostgresロール）の権限を使用して実行されます。

```sql
create function public.handle_new_user()
returns trigger as $$
begin
  insert into public.profiles (id, full_name, avatar_url)
  values (new.id, new.raw_user_meta_data->>'full_name', new.raw_user_meta_data->>'avatar_url');
  return new;
end;
$$ language plpgsql security definer;
```

このコードは、public.handle_new_userというトリガー関数を作成し、その関数にsecurity definer修飾子を付けています。これにより、この関数は関数を作成したユーザーの権限を使用して実行されます。通常、これはpostgresロールであるため、必要な権限を持つことができます。

3. トリガーの再作成：

- 再作成した関数を使用してauth.usersテーブルにトリガーを再作成します。このトリガーは、新しいユーザーが作成された際に外部テーブルにデータを挿入することができます。

```sql
create trigger on_auth_user_created
  after insert on auth.users
  for each row execute procedure public.handle_new_user();
```

この手順を実行することで、supabase_auth_adminが外部テーブルに対して必要な操作を行う際の権限エラーを回避できます。

## 実際にエラーが解消されたか試してみる

それでは解消されたか見てみます。サインインボタンを押下し、サーバーエラー(500)にならずにサインアウトボタンに変化すれば成功です。以下は開発中の画面になります。

![サインイン前](https://storage.googleapis.com/zenn-user-upload/99191b36e6e2-20240523.png)

![サインイン後](https://storage.googleapis.com/zenn-user-upload/97ac503052af-20240523.png)

***成功！！***

Supabase側の認証ユーザーやProfileデータの追加も成功してました。

# 終わりに

今回のエラーを無事に解消できてすっきりしました。同じような問題に直面したとき、このエラー解消の過程をアウトプットとして記事に残すことも大事だなと感じました。引き続き開発を進めていきます。

最後まで読んでいただき、ありがとうございました！

# URL

https://github.com/orgs/supabase/discussions/21247