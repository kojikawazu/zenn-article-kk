---
title: "デザインパターン備忘録(前半)"
emoji: "🖌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["java", "typescript", "python" "デザインパターン"]
published: true
---

# はじめに

ChatGPT-4が使用できるようになりました。私も早速使ってみましたが、以前のバージョンに比べてスピードと精度の向上を実感しました。これを機に、デザインパターンについての記事を書いてみることにしました。この記事を通じて勉強にもなり、後で見返すための資料としても活用できるようにしたいと思います。サンプルコードには、Java、TypeScript、Pythonのプログラミング言語を使用しています。

今回は記事を2つに分けることになりました。
[後半の記事はこちら](https://zenn.dev/kou_kawa/articles/36-design-pattern-02)

# デザインパターン一覧

- 生成に関するパターン
  - Abstract Factory（アブストラクトファクトリー）
  - Builder（ビルダー）
  - Factory Method（ファクトリーメソッド）
  - Prototype（プロトタイプ）
  - Singleton（シングルトン）
- Structural Patterns（構造に関するパターン）
  - Adapter（アダプター）
  - Bridge（ブリッジ）
  - Composite（コンポジット）
  - Decorator（デコレーター）
  - Facade（ファサード）
  - Flyweight（フライウェイト）
  - Proxy（プロキシ）
- Behavioral Patterns（振る舞いに関するパターン）
  - Chain of Responsibility（責任の連鎖）
  - Command（コマンド）
  - Interpreter（インタープリター）
  - Iterator（イテレーター）
  - Mediator（メディエーター）
  - Memento（メメント）
  - Observer（オブザーバー）
  - State（ステート）
  - Strategy（ストラテジー）
  - Template Method（テンプレートメソッド）
  - Visitor（ビジター）

# 使い分けと適用例

- Abstract Factory（アブストラクトファクトリー）パターン
  - 使い分け: 具体的なクラスに依存せずに、一連の関連オブジェクトを生成する必要がある場合。
  - 適用例: GUIライブラリで異なるテーマのウィジェットを生成する場合。
- Builder（ビルダー）パターン
  - 使い分け: 複雑なオブジェクトの生成手順を明確に分けたい場合や、生成過程を柔軟にしたい場合。
  - 適用例: 複数のステップを経て生成されるドキュメントオブジェクト。
- Factory Method（ファクトリーメソッド）パターン
  - 使い分け: 生成するオブジェクトのクラスをサブクラスに任せたい場合。
  - 適用例: プラグインシステムで異なるプラグインオブジェクトを生成する場合。
- Prototype（プロトタイプ）パターン
  - 使い分け: オブジェクトの生成コストが高い場合や、生成するオブジェクトの型を変更したくない場合。
  - 適用例: 複雑なオブジェクトの複製（例：グラフィカルオブジェクト）。
- Singleton（シングルトン）パターン
  - 使い分け: アプリケーション全体で共有されるリソース（例：設定情報やログ管理）に使用。
  - 適用例: ログシステム、設定管理。
- Adapter（アダプター）パターン
  - 使い分け: 既存のクラスを変更せずに新しいインターフェースに適合させたい場合。
  - 適用例: 旧システムと新システムの統合。
- Bridge（ブリッジ）パターン
  - 使い分け: 抽象部分と実装部分を独立して拡張したい場合。
  - 適用例: 複数のプラットフォームに対応するGUIツールキット。
- Composite（コンポジット）パターン
  - 使い分け: 再帰的な構造を持つデータを扱う場合。
  - 適用例: ファイルシステムのディレクトリ構造。
- Decorator（デコレーター）パターン
  - 使い分け: オブジェクトのクラスを変更せずに、動的に機能を追加したい場合。
  - 適用例: GUIウィジェットの拡張。
- Facade（ファサード）パターン
  - 使い分け: サブシステムの複雑さを隠蔽し、使いやすいインターフェースを提供したい場合。
  - 適用例: 大規模ライブラリの簡素化されたインターフェース。
- Flyweight（フライウェイト）パターン
  - 使い分け: 多数の同種オブジェクトを効率的に扱いたい場合。
  - 適用例: テキストエディタでの文字オブジェクトの共有。
- Proxy（プロキシ）パターン
  - 使い分け: オブジェクトへのアクセス制御、遅延初期化、リモートアクセスなどに使用。
  - 適用例: 仮想プロキシ、リモートプロキシ、保護プロキシ。
- Chain of Responsibility（責任の連鎖）パターン
  - 使い分け: リクエストを処理するオブジェクトを動的に決定したい場合。
  - 適用例: イベント処理システム、エラーハンドリング。
- Command（コマンド）パターン
  - 使い分け: ユーザー操作の履歴管理、取り消し、再実行を実装したい場合。
  - 適用例: テキストエディタのUndo/Redo機能。
- Interpreter（インタープリター）パターン
  - 使い分け: 簡単な文法の解析や実行を行いたい場合。
  - 適用例: 数式評価、構成ファイルのパース。
- Iterator（イテレーター）パターン
  - 使い分け: コレクションの要素に対して一貫した方法でアクセスしたい場合。
  - 適用例: コレクションフレームワーク、ファイルシステムトラバース。
- Mediator（メディエーター）パターン
  - 使い分け: 多数のオブジェクトが相互に通信する必要がある場合。
  - 適用例: GUIコンポーネントの相互作用、チャットシステム。
- Memento（メメント）パターン
  - 使い分け: オブジェクトの状態を保存し、復元する必要がある場合。
  - 適用例: テキストエディタのUndo機能、ゲームのセーブポイント。
- Observer（オブザーバー）パターン
  - 使い分け: オブジェクトの状態変化を他のオブジェクトに通知したい場合。
  - 適用例: GUIイベントシステム、データバインディング。
- State（ステート）パターン
  - 使い分け: オブジェクトの状態に応じた振る舞いを実装したい場合。
  - 適用例: ユーザー認証システム（ログイン、ログアウト）、メディアプレイヤーの再生状態。
- Strategy（ストラテジー）パターン
  - 使い分け: 動的にアルゴリズムを変更したい場合。
  - 適用例: ソートアルゴリズム、支払い方法の選択。
- Template Method（テンプレートメソッド）
  - 使い分け: アルゴリズムの骨組みを共通化し、具体的な処理をサブクラスで実装したい場合。
  - 適用例: データ処理パイプライン、レポート生成。
- Visitor（ビジター）パターン
  - 使い分け: オブジェクト構造を変更せずに、新しい操作を追加したい場合。
  - 適用例: 構文解析ツール、ドキュメント処理。


# Abstract Factory（アブストラクトファクトリー）

Abstract Factory パターンは、関連するオブジェクトのファミリーを作成するインターフェースを提供し、具体的なクラスを指定せずにオブジェクトの生成を行うデザインパターンです。これにより、クライアントコードはどの具体的なクラスが生成されるかを知らずに済みます。

## メリット

- インターフェースの一貫性

クライアントが異なる製品のファミリーを作成する方法を知らずにすむため、一貫したインターフェースを提供します。

- 具体的なクラスに依存しない

クライアントコードが具体的なクラスに依存せず、抽象的なインターフェースを通じてオブジェクトを生成するため、柔軟性が高まります。

- コードの変更に強い

具体的なクラスの変更がクライアントコードに影響を与えないため、新しい製品ファミリーを追加するのが容易です。

- 一貫した製品の組み合わせ

異なる製品が互いに一貫した方法で組み合わされることを保証できます。

## デメリット

- 複雑性の増加

抽象ファクトリーの導入により、コードの構造が複雑になる可能性があります。

- 新しい製品の追加が困難

製品のファミリーに新しい型を追加することが難しい場合があります。これには、抽象ファクトリーインターフェースやそのすべての具体的な実装を変更する必要があります。

- 初期設定のコスト

初期設定に時間とリソースが必要であり、特に小規模なプロジェクトではオーバーヘッドになる可能性があります。

## 適用場面

- 相互に関連するオブジェクトのファミリーを生成する必要がある場合

GUIライブラリなど、複数の相互依存するコンポーネント（ボタン、テキストボックスなど）を生成する場合。

- クライアントが具体的なクラスに依存せずにオブジェクトを生成する必要がある場合

データベースアクセス層で異なるデータベース（MySQL、PostgreSQL、SQLiteなど）に対応するための接続オブジェクトを生成する場合。

- 製品の一貫性を保ちたい場合

複数のテーマやスタイルを持つUIコンポーネントを扱う場合、特定のテーマに一致するコンポーネントを生成するために使用。

## 実際の適用例

- GUIツールキット

異なるプラットフォーム（Windows、macOS、Linux）に対して、一貫したUIコンポーネント（ボタン、テキストフィールド、チェックボックスなど）を提供する場合。

- データベース接続

複数のデータベースタイプ（例えば、MySQL、PostgreSQL、Oracle）をサポートするアプリケーションで、データベース接続を生成するためのファクトリーを使用。

- プラグインシステム

異なるプラグインセット（例えば、エディタープラグイン、レンダラープラグイン）を動的にロードする必要がある場合。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
interface Button {
    void paint();
}

interface Checkbox {
    void paint();
}

class WinButton implements Button {
    public void paint() {
        System.out.println("Rendering a button in Windows style.");
    }
}

class MacButton implements Button {
    public void paint() {
        System.out.println("Rendering a button in MacOS style.");
    }
}

class WinCheckbox implements Checkbox {
    public void paint() {
        System.out.println("Rendering a checkbox in Windows style.");
    }
}

class MacCheckbox implements Checkbox {
    public void paint() {
        System.out.println("Rendering a checkbox in MacOS style.");
    }
}

interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

class WinFactory implements GUIFactory {
    public Button createButton() {
        return new WinButton();
    }
    public Checkbox createCheckbox() {
        return new WinCheckbox();
    }
}

class MacFactory implements GUIFactory {
    public Button createButton() {
        return new MacButton();
    }
    public Checkbox createCheckbox() {
        return new MacCheckbox();
    }
}
```

```java
public class Application {
    private Button button;
    private Checkbox checkbox;

    public Application(GUIFactory factory) {
        button = factory.createButton();
        checkbox = factory.createCheckbox();
    }

    public void paint() {
        button.paint();
        checkbox.paint();
    }

    public static void main(String[] args) {
        GUIFactory factory;
        String osName = System.getProperty("os.name").toLowerCase();
        if (osName.contains("win")) {
            factory = new WinFactory();
        } else {
            factory = new MacFactory();
        }
        Application app = new Application(factory);
        app.paint();
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface Button {
    paint(): void;
}

interface Checkbox {
    paint(): void;
}

class WinButton implements Button {
    paint() {
        console.log("Rendering a button in Windows style.");
    }
}

class MacButton implements Button {
    paint() {
        console.log("Rendering a button in MacOS style.");
    }
}

class WinCheckbox implements Checkbox {
    paint() {
        console.log("Rendering a checkbox in Windows style.");
    }
}

class MacCheckbox implements Checkbox {
    paint() {
        console.log("Rendering a checkbox in MacOS style.");
    }
}

interface GUIFactory {
    createButton(): Button;
    createCheckbox(): Checkbox;
}

class WinFactory implements GUIFactory {
    createButton(): Button {
        return new WinButton();
    }
    createCheckbox(): Checkbox {
        return new WinCheckbox();
    }
}

class MacFactory implements GUIFactory {
    createButton(): Button {
        return new MacButton();
    }
    createCheckbox(): Checkbox {
        return new MacCheckbox();
    }
}
```

```typescript
class Application {
    private button: Button;
    private checkbox: Checkbox;

    constructor(factory: GUIFactory) {
        this.button = factory.createButton();
        this.checkbox = factory.createCheckbox();
    }

    paint() {
        this.button.paint();
        this.checkbox.paint();
    }
}

let factory: GUIFactory;
const osName = navigator.platform.toLowerCase();
if (osName.includes('win')) {
    factory = new WinFactory();
} else {
    factory = new MacFactory();
}

const app = new Application(factory);
app.paint();
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class Button(ABC):
    @abstractmethod
    def paint(self):
        pass

class Checkbox(ABC):
    @abstractmethod
    def paint(self):
        pass

class WinButton(Button):
    def paint(self):
        print("Rendering a button in Windows style.")

class MacButton(Button):
    def paint(self):
        print("Rendering a button in MacOS style.")

class WinCheckbox(Checkbox):
    def paint(self):
        print("Rendering a checkbox in Windows style.")

class MacCheckbox(Checkbox):
    def paint(self):
        print("Rendering a checkbox in MacOS style.")

class GUIFactory(ABC):
    @abstractmethod
    def create_button(self):
        pass

    @abstractmethod
    def create_checkbox(self):
        pass

class WinFactory(GUIFactory):
    def create_button(self):
        return WinButton()

    def create_checkbox(self):
        return WinCheckbox()

class MacFactory(GUIFactory):
    def create_button(self):
        return MacButton()

    def create_checkbox(self):
        return MacCheckbox()

class Application:
    def __init__(self, factory: GUIFactory):
        self.button = factory.create_button()
        self.checkbox = factory.create_checkbox()

    def paint(self):
        self.button.paint()
        self.checkbox.paint()
```

```python
import platform

if platform.system().lower().startswith('win'):
    factory = WinFactory()
else:
    factory = MacFactory()

app = Application(factory)
app.paint()
```
:::

# Builder（ビルダー）パターン

## メリット

- 複雑なオブジェクト生成を簡素化

複雑なオブジェクトの生成手順を分割して管理し、コードの可読性とメンテナンス性を向上させます。

- 可読性の向上

メソッドチェーンを使ってオブジェクトを構築するため、コードが直感的で読みやすくなります。

- 不変オブジェクトの生成

最後に一度だけビルドすることで、不変（immutable）なオブジェクトを簡単に生成できます。

- オブジェクト生成の柔軟性

必要なパーツだけを指定してオブジェクトを生成することができるため、柔軟性が高まります。

- テストのしやすさ

オブジェクト生成の各ステップを個別にテストできるため、ユニットテストが容易になります。

## デメリット

- 複雑性の増加

シンプルなオブジェクトには不必要なオーバーヘッドとなる可能性があります。

- コードの冗長性

ビルダーとそのメソッドを追加することで、コードが冗長になる場合があります。

- 初期設定のコスト

小規模プロジェクトや単純なオブジェクトには導入コストが高く感じられることがあります。

## 採用場面

- 複雑なオブジェクトの生成が必要な場合

複数のフィールドや設定が必要なオブジェクトを生成する際に、ビルダーを使うと生成プロセスが分かりやすくなります。

- 可変長引数のコンストラクタが必要な場合

コンストラクタに多くの引数を持つオブジェクトを生成する際、ビルダーを使うことで必要な引数のみを設定できます。

- 不変オブジェクトの生成が重要な場合

ビルダーを使うことで、不変オブジェクトを生成しやすくなります。

- ステップごとのオブジェクト生成が必要な場合

複数のステップでオブジェクトを構築する際に、各ステップを分けて管理できるため、複雑な生成プロセスを簡素化できます。

## 実際の適用例

- 複雑なGUIコンポーネントの構築

複数のオプションや設定が必要な複雑なGUIコンポーネント（例えば、ダイアログウィンドウやカスタムウィジェット）を生成する際に、ビルダーを使用して柔軟に構成する。

- HTTPリクエストの構築

複雑なHTTPリクエスト（例えば、ヘッダー、パラメータ、認証情報などを含む）を構築する際に、ビルダーを使用して必要な要素だけを設定し、直感的にリクエストを生成する。

- データベース接続の設定

複数のデータベース接続パラメータ（ホスト、ポート、ユーザー名、パスワード、データベース名など）を指定する必要がある場合に、ビルダーを使用して簡単に構成する。

- APIクライアントの設定

APIクライアントを構築する際に、必要な設定（例えば、エンドポイント、認証トークン、タイムアウト、再試行ポリシーなど）をビルダーを使って柔軟に設定する。

- レポート生成

複数のセクションやパートを持つレポート（例えば、タイトル、ヘッダー、フッター、コンテンツセクション）を生成する際に、ビルダーを使用して各パートを段階的に追加する。

- ドメインオブジェクトの構築

多くの属性を持つ複雑なドメインオブジェクト（例えば、ユーザープロフィール、注文情報、製品詳細）を生成する際に、ビルダーを使用して必要な属性を設定し、オブジェクトを一度に生成する。

- 設定ファイルのパース

複数のオプションや設定が含まれる設定ファイルをパースし、オブジェクトとして表現する際に、ビルダーを使用して柔軟に構成する。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
public class House {
    private String foundation;
    private String structure;
    private String roof;
    private String interior;

    private House(HouseBuilder builder) {
        this.foundation = builder.foundation;
        this.structure = builder.structure;
        this.roof = builder.roof;
        this.interior = builder.interior;
    }

    public static class HouseBuilder {
        private String foundation;
        private String structure;
        private String roof;
        private String interior;

        public HouseBuilder setFoundation(String foundation) {
            this.foundation = foundation;
            return this;
        }

        public HouseBuilder setStructure(String structure) {
            this.structure = structure;
            return this;
        }

        public HouseBuilder setRoof(String roof) {
            this.roof = roof;
            return this;
        }

        public HouseBuilder setInterior(String interior) {
            this.interior = interior;
            return this;
        }

        public House build() {
            return new House(this);
        }
    }

    @Override
    public String toString() {
        return "House{" +
                "foundation='" + foundation + '\'' +
                ", structure='" + structure + '\'' +
                ", roof='" + roof + '\'' +
                ", interior='" + interior + '\'' +
                '}';
    }
}
```

```java
public class BuilderPatternExample {
    public static void main(String[] args) {
        House house = new House.HouseBuilder()
                .setFoundation("Concrete")
                .setStructure("Wood")
                .setRoof("Tiles")
                .setInterior("Modern")
                .build();

        System.out.println(house);
    }
}
```
:::

:::details TypeScriptコード
```typescript
class House {
    foundation: string;
    structure: string;
    roof: string;
    interior: string;

    constructor(builder: HouseBuilder) {
        this.foundation = builder.foundation;
        this.structure = builder.structure;
        this.roof = builder.roof;
        this.interior = builder.interior;
    }

    public static get Builder() {
        return new HouseBuilder();
    }
}

class HouseBuilder {
    foundation: string;
    structure: string;
    roof: string;
    interior: string;

    setFoundation(foundation: string): HouseBuilder {
        this.foundation = foundation;
        return this;
    }

    setStructure(structure: string): HouseBuilder {
        this.structure = structure;
        return this;
    }

    setRoof(roof: string): HouseBuilder {
        this.roof = roof;
        return this;
    }

    setInterior(interior: string): HouseBuilder {
        this.interior = interior;
        return this;
    }

    build(): House {
        return new House(this);
    }
}
```

```typescript
const house = House.Builder
    .setFoundation('Concrete')
    .setStructure('Wood')
    .setRoof('Tiles')
    .setInterior('Modern')
    .build();

console.log(house);
```
:::

:::details Pythonコード
```python
# Product
class House:
    def __init__(self, builder):
        self.foundation = builder.foundation
        self.structure = builder.structure
        self.roof = builder.roof
        self.interior = builder.interior

    def __str__(self):
        return f'House(foundation={self.foundation}, structure={self.structure}, roof={self.roof}, interior={self.interior})'


class HouseBuilder:
    def __init__(self):
        self.foundation = None
        self.structure = None
        self.roof = None
        self.interior = None

    def set_foundation(self, foundation):
        self.foundation = foundation
        return self

    def set_structure(self, structure):
        self.structure = structure
        return self

    def set_roof(self, roof):
        self.roof = roof
        return self

    def set_interior(self, interior):
        self.interior = interior
        return self

    def build(self):
        return House(self)
```

```python
if __name__ == "__main__":
    house = (HouseBuilder()
             .set_foundation("Concrete")
             .set_structure("Wood")
             .set_roof("Tiles")
             .set_interior("Modern")
             .build())

    print(house)
```
:::

# Factory Method（ファクトリーメソッド）

## メリット

- オブジェクト生成の詳細を隠蔽

クライアントコードがオブジェクト生成の詳細を知らずに済むため、コードの依存関係が減少します。

- 新しいクラスの追加が容易

新しい具体的なクラスを追加しても、クライアントコードを変更する必要がないため、拡張性が向上します。

- サブクラスによる拡張が可能

具象クラスの生成をサブクラスに委譲することで、柔軟性が高まります。

- コードの再利用性向上

オブジェクト生成コードを集中管理することで、コードの再利用性が向上します。

## デメリット

- コードの複雑性の増加

クラス階層が増えるため、コードが複雑になる可能性があります。

- クラス数の増加

具象クラスごとにファクトリーメソッドを実装する必要があるため、クラス数が増加することがあります。

- 初期設定のコスト

シンプルなオブジェクト生成には、ファクトリーメソッドの導入が過剰な場合があります。

## 採用場面

- 具象クラスを事前に知らずにオブジェクトを生成する必要がある場合

実行時に生成する具体的なクラスが決定される場合に有効です。

- クラスのインスタンス化が複雑であり、再利用可能なコードにしたい場合

複雑な初期化手順を持つオブジェクトの生成をカプセル化する場合に有効です。

- オブジェクト生成の詳細が頻繁に変更される場合

新しいタイプのオブジェクトが頻繁に追加される場合に有効です。

## 実際の適用例

- GUIツールキット

異なるプラットフォーム（Windows、macOS、Linux）に対して、一貫したUIコンポーネント（ボタン、テキストフィールド、チェックボックスなど）を提供する場合。

- データベース接続

複数のデータベースタイプ（例えば、MySQL、PostgreSQL、Oracle）をサポートするアプリケーションで、データベース接続を生成するためのファクトリーを使用。

- プラグインシステム

異なるプラグインセット（例えば、エディタープラグイン、レンダラープラグイン）を動的にロードする必要がある場合。

- ドキュメント生成

異なる形式のドキュメント（例えば、PDF、HTML、テキスト）を生成するアプリケーションで、形式ごとに適切なドキュメント生成クラスを選択する場合。

- 通知システム

メール、SMS、プッシュ通知など、異なる通知手段に応じて異なる通知クラスを生成する場合。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
interface Animal {
    void speak();
}

class Dog implements Animal {
    @Override
    public void speak() {
        System.out.println("Woof!");
    }
}

class Cat implements Animal {
    @Override
    public void speak() {
        System.out.println("Meow!");
    }
}

abstract class AnimalFactory {
    public abstract Animal createAnimal();

    public void makeSound() {
        Animal animal = createAnimal();
        animal.speak();
    }
}

class DogFactory extends AnimalFactory {
    @Override
    public Animal createAnimal() {
        return new Dog();
    }
}

class CatFactory extends AnimalFactory {
    @Override
    public Animal createAnimal() {
        return new Cat();
    }
}
```

```java
public class FactoryMethodExample {
    public static void main(String[] args) {
        AnimalFactory dogFactory = new DogFactory();
        dogFactory.makeSound();

        AnimalFactory catFactory = new CatFactory();
        catFactory.makeSound();
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface Animal {
    speak(): void;
}

class Dog implements Animal {
    speak(): void {
        console.log("Woof!");
    }
}

class Cat implements Animal {
    speak(): void {
        console.log("Meow!");
    }
}

abstract class AnimalFactory {
    abstract createAnimal(): Animal;

    makeSound(): void {
        const animal = this.createAnimal();
        animal.speak();
    }
}

class DogFactory extends AnimalFactory {
    createAnimal(): Animal {
        return new Dog();
    }
}

class CatFactory extends AnimalFactory {
    createAnimal(): Animal {
        return new Cat();
    }
}
```

```typescript
const dogFactory = new DogFactory();
dogFactory.makeSound();

const catFactory = new CatFactory();
catFactory.makeSound();
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class Animal(ABC):
    @abstractmethod
    def speak(self):
        pass

class Dog(Animal):
    def speak(self):
        print("Woof!")

class Cat(Animal):
    def speak(self):
        print("Meow!")

class AnimalFactory(ABC):
    @abstractmethod
    def create_animal(self):
        pass

    def make_sound(self):
        animal = self.create_animal()
        animal.speak()

class DogFactory(AnimalFactory):
    def create_animal(self):
        return Dog()

class CatFactory(AnimalFactory):
    def create_animal(self):
        return Cat()
```

```python
if __name__ == "__main__":
    dog_factory = DogFactory()
    dog_factory.make_sound()

    cat_factory = CatFactory()
    cat_factory.make_sound()
```
:::

# Prototype（プロトタイプ）パターン

## メリット

- オブジェクトの複製が簡単

複雑な初期化手順を経ずに、既存のオブジェクトを効率的に複製できます。

- 柔軟性の向上

オブジェクトのクラスを指定せずに複製できるため、柔軟性が高まります。

- パフォーマンスの向上

複製によって新しいオブジェクトを生成する方が、新規にインスタンスを作成するよりも高速である場合があります。

- 実行時のクラス判定が不要

複製するオブジェクトのクラスを意識することなく、新しいインスタンスを生成できます。

## デメリット

- 複製の実装が複雑になることがある

複雑なオブジェクトや、深い階層構造を持つオブジェクトの複製は、実装が難しくなることがあります。

- 変更の影響が広がる可能性

プロトタイプとして使用するオブジェクトに変更が加わると、その影響が複製されたオブジェクトにも波及する可能性があります。

- クローンメソッドの実装が必要

全てのクラスにクローンメソッドを実装する必要があり、設計の負担が増えることがあります。

## 採用場面

- 複雑なオブジェクトの初期化が必要な場合

複雑な初期化手順を経て生成されるオブジェクトを簡単に複製したい場合に有効です。

- ランタイムにオブジェクトを動的に生成する場合

実行時に新しいインスタンスを動的に生成する必要がある場合に有効です。

- 同一のオブジェクトを複数生成する場合

同一のオブジェクトを大量に生成する必要がある場合に、プロトタイプパターンを使うと効率的です。

- インスタンス生成のコストが高い場合

新しいインスタンスの生成コストが高く、既存のインスタンスを複製する方が効率的な場合に有効です。

## 実際の適用例

- 設定オブジェクトの複製

複雑な初期化を必要とする設定オブジェクトを複製することで、新しい設定インスタンスを効率的に生成。

- ゲームのエンティティ生成

ゲームで使用するエンティティ（キャラクター、アイテムなど）をプロトタイプとして保持し、必要に応じて複製して生成。

- ユーザーインターフェースコンポーネントの生成

UIコンポーネントのプロトタイプを複製することで、新しいコンポーネントを効率的に生成。

- ドキュメントテンプレートの生成

ドキュメントのテンプレートをプロトタイプとして保持し、新しいドキュメントをテンプレートから生成。

- グラフィカルオブジェクトの複製

グラフィカルなオブジェクト（例えば、図形やアイコン）をプロトタイプとして保持し、必要に応じて複製。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
import java.util.HashMap;
import java.util.Map;

interface Animal extends Cloneable {
    Animal clone();
}

class Dog implements Animal {
    @Override
    public Dog clone() {
        try {
            return (Dog) super.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
            return null;
        }
    }

    @Override
    public String toString() {
        return "Dog";
    }
}

class Cat implements Animal {
    @Override
    public Cat clone() {
        try {
            return (Cat) super.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
            return null;
        }
    }

    @Override
    public String toString() {
        return "Cat";
    }
}

class AnimalRegistry {
    private Map<String, Animal> registry = new HashMap<>();

    public AnimalRegistry() {
        registry.put("Dog", new Dog());
        registry.put("Cat", new Cat());
    }

    public Animal getAnimal(String type) {
        return registry.get(type).clone();
    }
}
```

```java
public class PrototypePatternExample {
    public static void main(String[] args) {
        AnimalRegistry registry = new AnimalRegistry();

        Animal dog1 = registry.getAnimal("Dog");
        Animal dog2 = registry.getAnimal("Dog");
        Animal cat1 = registry.getAnimal("Cat");

        System.out.println(dog1); // Output: Dog
        System.out.println(dog2); // Output: Dog
        System.out.println(cat1); // Output: Cat

        System.out.println(dog1 == dog2); // Output: false
        System.out.println(dog1.equals(dog2)); // Output: false
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface Animal {
    clone(): Animal;
}

class Dog implements Animal {
    clone(): Dog {
        return Object.create(this);
    }

    toString(): string {
        return "Dog";
    }
}

class Cat implements Animal {
    clone(): Cat {
        return Object.create(this);
    }

    toString(): string {
        return "Cat";
    }
}

class AnimalRegistry {
    private registry: { [key: string]: Animal } = {};

    constructor() {
        this.registry["Dog"] = new Dog();
        this.registry["Cat"] = new Cat();
    }

    getAnimal(type: string): Animal {
        return this.registry[type].clone();
    }
}
```

```typescript
const registry = new AnimalRegistry();

const dog1 = registry.getAnimal("Dog");
const dog2 = registry.getAnimal("Dog");
const cat1 = registry.getAnimal("Cat");

console.log(dog1.toString()); // Output: Dog
console.log(dog2.toString()); // Output: Dog
console.log(cat1.toString()); // Output: Cat

console.log(dog1 === dog2); // Output: false
console.log(dog1 == dog2); // Output: false
```
:::

:::details Pythonコード
```python
import copy
from abc import ABC, abstractmethod

class Animal(ABC):
    @abstractmethod
    def clone(self):
        pass

class Dog(Animal):
    def clone(self):
        return copy.deepcopy(self)

    def __str__(self):
        return "Dog"

class Cat(Animal):
    def clone(self):
        return copy.deepcopy(self)

    def __str__(self):
        return "Cat"

class AnimalRegistry:
    def __init__(self):
        self._registry = {
            "Dog": Dog(),
            "Cat": Cat()
        }

    def get_animal(self, type):
        return self._registry[type].clone()
```

```python
if __name__ == "__main__":
    registry = AnimalRegistry()

    dog1 = registry.get_animal("Dog")
    dog2 = registry.get_animal("Dog")
    cat1 = registry.get_animal("Cat")

    print(dog1)  # Output: Dog
    print(dog2)  # Output: Dog
    print(cat1)  # Output: Cat

    print(dog1 is dog2)  # Output: False
    print(dog1 == dog2)  # Output: False
```
:::

# Singleton（シングルトン）パターン

## メリット

- インスタンスの一意性を保証

クラスのインスタンスが一つだけであることを保証し、グローバルにアクセスできるようにします。

- リソースの節約

唯一のインスタンスを共有することで、リソースの浪費を防ぎます。特に、重い初期化が必要なオブジェクトに対して有効です。

- アクセスポイントの統一

グローバルなアクセスポイントを提供することで、他のクラスやモジュールからのアクセスを容易にします。

- 初期化制御

インスタンスの生成時に複雑な初期化処理が必要な場合、その制御をシングルトンに任せることができます。

## デメリット

- グローバルな状態の共有

グローバルにアクセス可能な状態を持つため、状態の変更が他の部分に予期しない影響を与える可能性があります。

- テストが難しくなる

シングルトンはグローバルな状態を持つため、ユニットテストでのモックやスタブの差し替えが難しくなります。

- スレッドセーフの確保が必要

マルチスレッド環境で使用する場合、スレッドセーフにするための追加の実装が必要です。

- 設計の柔軟性の欠如

一度シングルトンを導入すると、そのクラスの拡張や変更が難しくなり、設計の柔軟性が失われる可能性があります。

## 採用場面

- グローバルな設定管理

アプリケーション全体で共有する設定や構成情報を管理する場合に適しています。

- ログ管理

アプリケーション全体で一貫したログ出力を行うために、ログ管理クラスをシングルトンとして設計します。

- データベース接続の管理

データベース接続の管理や接続プールを一元管理するために使用します。

- キャッシュの管理

アプリケーション全体で共有されるキャッシュを管理する場合に適しています。

- ハードウェアアクセスの管理

ハードウェアリソース（例えば、プリンタやサウンドカード）へのアクセスを一元管理する場合に使用します。

## 実際の適用例

- 設定管理

アプリケーション全体で共通の設定情報を管理し、各モジュールが同じ設定情報にアクセスする。

- ログ管理

アプリケーション全体で一貫したログ出力を行うために、シングルトンのログ管理クラスを使用。

- データベース接続

データベースへの接続を一元管理し、各モジュールが同じ接続インスタンスを共有する。

- キャッシュ管理

計算結果やデータベースクエリ結果をキャッシュし、全体で再利用する。

- プリンタ管理

プリンタへのアクセスを一元管理し、複数のモジュールからのプリント要求をシリアライズする。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
public class Singleton {
    private static Singleton instance = new Singleton();

    private Singleton() {
        // Initialization code here
    }

    public static Singleton getInstance() {
        return instance;
    }

    public void showMessage() {
        System.out.println("Hello from Singleton!");
    }

    public static void main(String[] args) {
        Singleton singleton = Singleton.getInstance();
        singleton.showMessage();
    }
}
```
:::

:::details TypeScriptコード
```typescript
class Singleton {
    private static instance: Singleton = new Singleton();

    private constructor() {
        // Initialization code here
    }

    public static getInstance(): Singleton {
        return Singleton.instance;
    }

    public showMessage(): void {
        console.log("Hello from Singleton!");
    }
}

const singleton = Singleton.getInstance();
singleton.showMessage();
```
:::

:::details Pythonコード
```python
class Singleton:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super(Singleton, cls).__new__(cls)
        return cls._instance

    def show_message(self):
        print("Hello from Singleton!")

if __name__ == "__main__":
    singleton = Singleton()
    singleton.show_message()
```
:::

# Adapter（アダプター）パターン

## メリット

- 互換性の向上

既存のクラスを変更せずに新しいインターフェースに適合させることができ、互換性を高めます。

- 再利用性の向上

既存のコードやクラスを再利用できるため、新しいコードの記述を最小限に抑えられます。

- クライアントコードの変更を最小限に抑える

クライアントコードを変更せずに新しい機能を追加できるため、既存のシステムに対する影響を最小限に抑えられます。

- 柔軟性の向上

異なるインターフェースを持つクラスを適応させることで、システムの柔軟性が向上します。

## デメリット

- 複雑性の増加

アダプタークラスの追加により、コードの複雑性が増すことがあります。

- オーバーヘッドの増加

アダプターのレイヤーが追加されるため、性能に若干の影響を与えることがあります。

- 理解の難しさ

設計やアーキテクチャが複雑になると、アダプターの役割を理解するのが難しくなることがあります。

## 採用場面

- 既存クラスの再利用

新しいインターフェースを持つシステムに、既存のクラスを再利用する必要がある場合。

- 異なるインターフェースを持つクラスの統合

異なるインターフェースを持つクラスを統一されたインターフェースで使用したい場合。

- クラスの拡張

クラスを変更せずに機能を追加したい場合。

- 複雑なサブシステムの簡素化

複雑なサブシステムを簡単に使えるようにするためのインターフェースを提供したい場合。

## 実際の適用例

- サードパーティライブラリの統合

異なるインターフェースを持つサードパーティライブラリを統一したインターフェースで利用するためのアダプター。

- レガシーシステムとの統合

古いシステムを新しいシステムに統合する際に、互換性を持たせるためのアダプター。

- データ変換

異なるフォーマットのデータを統一したフォーマットで扱うためのアダプター。

- GUIコンポーネント

異なるUIフレームワークのコンポーネントを統一したインターフェースで使用するためのアダプター。

- ファイルシステムの抽象化

異なるファイルシステム（ローカルファイルシステム、クラウドストレージなど）を統一したインターフェースで扱うためのアダプター。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
interface MediaPlayer {
    void play(String audioType, String fileName);
}

class AdvancedMediaPlayer {
    void playVlc(String fileName) {
        System.out.println("Playing vlc file. Name: " + fileName);
    }

    void playMp4(String fileName) {
        System.out.println("Playing mp4 file. Name: " + fileName);
    }
}

class MediaAdapter implements MediaPlayer {
    AdvancedMediaPlayer advancedMediaPlayer;

    public MediaAdapter(String audioType) {
        if (audioType.equalsIgnoreCase("vlc")) {
            advancedMediaPlayer = new AdvancedMediaPlayer();
        } else if (audioType.equalsIgnoreCase("mp4")) {
            advancedMediaPlayer = new AdvancedMediaPlayer();
        }
    }

    @Override
    public void play(String audioType, String fileName) {
        if (audioType.equalsIgnoreCase("vlc")) {
            advancedMediaPlayer.playVlc(fileName);
        } else if (audioType.equalsIgnoreCase("mp4")) {
            advancedMediaPlayer.playMp4(fileName);
        }
    }
}
```

```java
public class AudioPlayer implements MediaPlayer {
    MediaAdapter mediaAdapter;

    @Override
    public void play(String audioType, String fileName) {
        if (audioType.equalsIgnoreCase("mp3")) {
            System.out.println("Playing mp3 file. Name: " + fileName);
        } else if (audioType.equalsIgnoreCase("vlc") || audioType.equalsIgnoreCase("mp4")) {
            mediaAdapter = new MediaAdapter(audioType);
            mediaAdapter.play(audioType, fileName);
        } else {
            System.out.println("Invalid media. " + audioType + " format not supported");
        }
    }

    public static void main(String[] args) {
        AudioPlayer audioPlayer = new AudioPlayer();

        audioPlayer.play("mp3", "beyond_the_horizon.mp3");
        audioPlayer.play("mp4", "alone.mp4");
        audioPlayer.play("vlc", "far_far_away.vlc");
        audioPlayer.play("avi", "mind_me.avi");
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface MediaPlayer {
    play(audioType: string, fileName: string): void;
}

class AdvancedMediaPlayer {
    playVlc(fileName: string): void {
        console.log(`Playing vlc file. Name: ${fileName}`);
    }

    playMp4(fileName: string): void {
        console.log(`Playing mp4 file. Name: ${fileName}`);
    }
}

class MediaAdapter implements MediaPlayer {
    advancedMediaPlayer: AdvancedMediaPlayer;

    constructor(audioType: string) {
        this.advancedMediaPlayer = new AdvancedMediaPlayer();
    }

    play(audioType: string, fileName: string): void {
        if (audioType === 'vlc') {
            this.advancedMediaPlayer.playVlc(fileName);
        } else if (audioType === 'mp4') {
            this.advancedMediaPlayer.playMp4(fileName);
        }
    }
}
```

```typescript
class AudioPlayer implements MediaPlayer {
    mediaAdapter: MediaAdapter;

    play(audioType: string, fileName: string): void {
        if (audioType === 'mp3') {
            console.log(`Playing mp3 file. Name: ${fileName}`);
        } else if (audioType === 'vlc' || audioType === 'mp4') {
            this.mediaAdapter = new MediaAdapter(audioType);
            this.mediaAdapter.play(audioType, fileName);
        } else {
            console.log(`Invalid media. ${audioType} format not supported`);
        }
    }
}

const audioPlayer = new AudioPlayer();
audioPlayer.play('mp3', 'beyond_the_horizon.mp3');
audioPlayer.play('mp4', 'alone.mp4');
audioPlayer.play('vlc', 'far_far_away.vlc');
audioPlayer.play('avi', 'mind_me.avi');
```
:::

:::details Pythonコード
```python
class MediaPlayer:
    def play(self, audio_type: str, file_name: str):
        pass

class AdvancedMediaPlayer:
    def play_vlc(self, file_name: str):
        print(f"Playing vlc file. Name: {file_name}")

    def play_mp4(self, file_name: str):
        print(f"Playing mp4 file. Name: {file_name}")

class MediaAdapter(MediaPlayer):
    def __init__(self, audio_type: str):
        self.advanced_media_player = AdvancedMediaPlayer()

    def play(self, audio_type: str, file_name: str):
        if audio_type == "vlc":
            self.advanced_media_player.play_vlc(file_name)
        elif audio_type == "mp4":
            self.advanced_media_player.play_mp4(file_name)
```

```python
class AudioPlayer(MediaPlayer):
    def __init__(self):
        self.media_adapter = None

    def play(self, audio_type: str, file_name: str):
        if audio_type == "mp3":
            print(f"Playing mp3 file. Name: {file_name}")
        elif audio_type in ["vlc", "mp4"]:
            self.media_adapter = MediaAdapter(audio_type)
            self.media_adapter.play(audio_type, file_name)
        else:
            print(f"Invalid media. {audio_type} format not supported")

if __name__ == "__main__":
    audio_player = AudioPlayer()
    audio_player.play("mp3", "beyond_the_horizon.mp3")
    audio_player.play("mp4", "alone.mp4")
    audio_player.play("vlc", "far_far_away.vlc")
    audio_player.play("avi", "mind_me.avi")
```
:::

# Bridge（ブリッジ）パターン

## メリット

- 抽象部分と実装部分の分離

抽象部分と実装部分を分離することで、両者を独立して変更できるようにします。これにより、コードの柔軟性と保守性が向上します。

- クラスの爆発的増加を防止

抽象部分と実装部分を分離することで、派生クラスの数が爆発的に増加するのを防ぎます。

- 拡張性の向上

新しい抽象部分や実装部分を追加する際に、既存のコードに影響を与えることなく拡張できます。

- ランタイムでの実装の切り替え

実行時に実装部分を切り替えることが容易になります。これにより、柔軟なシステム設計が可能になります。

- 異なるプラットフォームへの対応

抽象部分を変更せずに、異なるプラットフォームごとに実装部分を作成できるため、プラットフォーム依存のコードを減らすことができます。

## デメリット

- 複雑性の増加

抽象部分と実装部分を分離するための追加のクラスとインターフェースが必要となり、コードが複雑になる可能性があります。

- 初期設定のコスト

パターンを正しく設計するための初期設定に時間がかかる場合があります。

## 採用場面

- 異なるプラットフォームに対応する場合

抽象部分は変更せずに、異なるプラットフォーム向けの実装を提供する必要がある場合。

- 機能の拡張が頻繁に行われる場合

新しい機能や実装が頻繁に追加される場合に、抽象部分と実装部分を独立して変更できるようにする。

- 複雑なオブジェクト構造を持つ場合

複数の抽象部分と実装部分が必要な複雑なオブジェクト構造を持つ場合に有効。

- ランタイムで実装を切り替える必要がある場合

実行時に異なる実装を切り替える必要がある場合に適しています。

## 実際の適用例

- GUIフレームワーク

異なるOSごとに異なる描画API（Windows、macOS、Linux）を使用するGUIフレームワークで、抽象部分（UI要素）と実装部分（描画API）を分離。

- デバイスコントローラー

異なるデバイス（プリンター、スキャナー、カメラ）に対する共通の操作インターフェースを提供し、それぞれのデバイスごとに異なる実装を持つ。

- データベース接続

抽象部分としてのデータベース操作インターフェースと、具体的なデータベース（MySQL、PostgreSQL、Oracle）ごとの接続と操作の実装部分を分離。

- メッセージ送信システム

抽象部分としてのメッセージ送信インターフェースを持ち、実装部分でメール送信、SMS送信、プッシュ通知などの具体的な送信方法を提供。

- レポート生成システム

抽象部分としてのレポート生成インターフェースを持ち、具体的な実装部分でPDF、HTML、Excelなどの異なる形式のレポートを生成。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
interface DrawAPI {
    void drawCircle(int radius, int x, int y);
}

class RedCircle implements DrawAPI {
    @Override
    public void drawCircle(int radius, int x, int y) {
        System.out.println("Drawing Circle[ color: red, radius: " + radius + ", x: " + x + ", y: " + y + "]");
    }
}

class GreenCircle implements DrawAPI {
    @Override
    public void drawCircle(int radius, int x, int y) {
        System.out.println("Drawing Circle[ color: green, radius: " + radius + ", x: " + x + ", y: " + y + "]");
    }
}

abstract class Shape {
    protected DrawAPI drawAPI;

    protected Shape(DrawAPI drawAPI) {
        this.drawAPI = drawAPI;
    }

    public abstract void draw();
}

class Circle extends Shape {
    private int x, y, radius;

    public Circle(int x, int y, int radius, DrawAPI drawAPI) {
        super(drawAPI);
        this.x = x;
        this.y = y;
        this.radius = radius;
    }

    @Override
    public void draw() {
        drawAPI.drawCircle(radius, x, y);
    }
}
```

```java
public class BridgePatternExample {
    public static void main(String[] args) {
        Shape redCircle = new Circle(100, 100, 10, new RedCircle());
        Shape greenCircle = new Circle(100, 100, 10, new GreenCircle());

        redCircle.draw();
        greenCircle.draw();
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface DrawAPI {
    drawCircle(radius: number, x: number, y: number): void;
}

class RedCircle implements DrawAPI {
    drawCircle(radius: number, x: number, y: number): void {
        console.log(`Drawing Circle[ color: red, radius: ${radius}, x: ${x}, y: ${y} ]`);
    }
}

class GreenCircle implements DrawAPI {
    drawCircle(radius: number, x: number, y: number): void {
        console.log(`Drawing Circle[ color: green, radius: ${radius}, x: ${x}, y: ${y} ]`);
    }
}

abstract class Shape {
    protected drawAPI: DrawAPI;

    protected constructor(drawAPI: DrawAPI) {
        this.drawAPI = drawAPI;
    }

    public abstract draw(): void;
}

class Circle extends Shape {
    private x: number;
    private y: number;
    private radius: number;

    constructor(x: number, y: number, radius: number, drawAPI: DrawAPI) {
        super(drawAPI);
        this.x = x;
        this.y = y;
        this.radius = radius;
    }

    public draw(): void {
        this.drawAPI.drawCircle(this.radius, this.x, this.y);
    }
}
```

```typescript
const redCircle = new Circle(100, 100, 10, new RedCircle());
const greenCircle = new Circle(100, 100, 10, new GreenCircle());

redCircle.draw();
greenCircle.draw();
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class DrawAPI(ABC):
    @abstractmethod
    def draw_circle(self, radius: int, x: int, y: int):
        pass

class RedCircle(DrawAPI):
    def draw_circle(self, radius: int, x: int, y: int):
        print(f"Drawing Circle[ color: red, radius: {radius}, x: {x}, y: {y} ]")

class GreenCircle(DrawAPI):
    def draw_circle(self, radius: int, x: int, y: int):
        print(f"Drawing Circle[ color: green, radius: {radius}, x: {x}, y: {y} ]")

class Shape(ABC):
    def __init__(self, draw_api: DrawAPI):
        self.draw_api = draw_api

    @abstractmethod
    def draw(self):
        pass

class Circle(Shape):
    def __init__(self, x: int, y: int, radius: int, draw_api: DrawAPI):
        super().__init__(draw_api)
        self.x = x
        self.y = y
        self.radius = radius

    def draw(self):
        self.draw_api.draw_circle(self.radius, self.x, self.y)
```

```python
if __name__ == "__main__":
    red_circle = Circle(100, 100, 10, RedCircle())
    green_circle = Circle(100, 100, 10, GreenCircle())

    red_circle.draw()
    green_circle.draw()
```
:::

# Composite（コンポジット）パターン

## メリット

- ツリー構造の表現

オブジェクトをツリー構造で表現でき、部分-全体の階層構造を自然に扱えます。

- 一貫性のある操作

個々のオブジェクトとコンポジット（複合オブジェクト）を同一視して操作できるため、一貫したインターフェースで処理が可能です。

- 柔軟な再帰処理

再帰的な構造を持つデータを扱う場合、再帰的な操作が簡単に実装できます。

- コードのシンプル化

個々のオブジェクトとコンポジットを同じように扱うことで、クライアントコードがシンプルになります。

- 拡張性の向上

新しいコンポーネントを追加する際に、既存のコードに影響を与えることなく拡張できます。

## デメリット

- 設計の複雑性

シンプルな構造には過剰な設計となり、システム全体の複雑性が増すことがあります。

- 型安全性の低下

コンポジットパターンはオブジェクトの統一された扱いを重視するため、特定の型の操作が難しくなることがあります。

- パフォーマンスの問題

再帰的な構造を持つため、大量の要素を操作する際にパフォーマンスが低下することがあります。

## 採用場面

- 階層構造のデータを扱う場合

階層構造やツリー構造を持つデータを扱う場合に有効です。

- 部分-全体の関係を表現する場合

部品と全体を同一視して操作する必要がある場合に適しています。

- 再帰的な操作が必要な場合

再帰的な操作を簡単に実装する必要がある場合に有効です。

- 一貫したインターフェースを提供する場合

個々のオブジェクトとコンポジットオブジェクトに対して一貫したインターフェースを提供する必要がある場合に適しています。

## 実際の適用例

- ファイルシステム

ディレクトリとファイルをツリー構造で表現し、ディレクトリ内の要素に対して一貫した操作（例えば、表示や削除）を行う。

- グラフィカルユーザーインターフェース（GUI）

ウィンドウ、パネル、ボタンなどのGUIコンポーネントをツリー構造で表現し、コンポーネントに対して一貫した操作（例えば、描画やイベント処理）を行う。

- 組織構造

組織内の部門やチームをツリー構造で表現し、各部門やチームに対して一貫した操作（例えば、リストアップや報告）を行う。

- グラフィック描画システム

図形（例えば、線、円、四角形）とそれらの集合をツリー構造で表現し、一貫した描画操作を行う。

- メニューシステム

メニューとサブメニューをツリー構造で表現し、ユーザーインターフェースで一貫した操作（例えば、選択や表示）を行う。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
import java.util.ArrayList;
import java.util.List;

abstract class Employee {
    protected String name;
    protected String position;

    public Employee(String name, String position) {
        this.name = name;
        this.position = position;
    }

    public abstract void showDetails();
}

class Developer extends Employee {
    public Developer(String name, String position) {
        super(name, position);
    }

    @Override
    public void showDetails() {
        System.out.println(name + " is a " + position);
    }
}

class Manager extends Employee {
    private List<Employee> employees = new ArrayList<>();

    public Manager(String name, String position) {
        super(name, position);
    }

    public void addEmployee(Employee employee) {
        employees.add(employee);
    }

    public void removeEmployee(Employee employee) {
        employees.remove(employee);
    }

    @Override
    public void showDetails() {
        System.out.println(name + " is a " + position);
        for (Employee employee : employees) {
            employee.showDetails();
        }
    }
}
```

```java
public class CompositePatternExample {
    public static void main(String[] args) {
        Developer dev1 = new Developer("John Doe", "Frontend Developer");
        Developer dev2 = new Developer("Jane Smith", "Backend Developer");

        Manager manager1 = new Manager("Alice Johnson", "Project Manager");
        manager1.addEmployee(dev1);
        manager1.addEmployee(dev2);

        Developer dev3 = new Developer("Mike Brown", "DevOps Engineer");
        Manager manager2 = new Manager("Bob White", "CTO");
        manager2.addEmployee(manager1);
        manager2.addEmployee(dev3);

        manager2.showDetails();
    }
}
```
:::

:::details TypeScriptコード
```typescript
abstract class Employee {
    protected name: string;
    protected position: string;

    constructor(name: string, position: string) {
        this.name = name;
        this.position = position;
    }

    abstract showDetails(): void;
}

class Developer extends Employee {
    constructor(name: string, position: string) {
        super(name, position);
    }

    showDetails(): void {
        console.log(`${this.name} is a ${this.position}`);
    }
}

class Manager extends Employee {
    private employees: Employee[] = [];

    constructor(name: string, position: string) {
        super(name, position);
    }

    addEmployee(employee: Employee): void {
        this.employees.push(employee);
    }

    removeEmployee(employee: Employee): void {
        const index = this.employees.indexOf(employee);
        if (index > -1) {
            this.employees.splice(index, 1);
        }
    }

    showDetails(): void {
        console.log(`${this.name} is a ${this.position}`);
        for (const employee of this.employees) {
            employee.showDetails();
        }
    }
}
```

```typescript
const dev1 = new Developer("John Doe", "Frontend Developer");
const dev2 = new Developer("Jane Smith", "Backend Developer");

const manager1 = new Manager("Alice Johnson", "Project Manager");
manager1.addEmployee(dev1);
manager1.addEmployee(dev2);

const dev3 = new Developer("Mike Brown", "DevOps Engineer");
const manager2 = new Manager("Bob White", "CTO");
manager2.addEmployee(manager1);
manager2.addEmployee(dev3);

manager2.showDetails();
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class Employee(ABC):
    def __init__(self, name: str, position: str):
        self.name = name
        self.position = position

    @abstractmethod
    def show_details(self):
        pass

class Developer(Employee):
    def show_details(self):
        print(f"{self.name} is a {self.position}")

class Manager(Employee):
    def __init__(self, name: str, position: str):
        super().__init__(name, position)
        self.employees = []

    def add_employee(self, employee: Employee):
        self.employees.append(employee)

    def remove_employee(self, employee: Employee):
        self.employees.remove(employee)

    def show_details(self):
        print(f"{self.name} is a {self.position}")
        for employee in self.employees:
            employee.show_details()
```

```python
if __name__ == "__main__":
    dev1 = Developer("John Doe", "Frontend Developer")
    dev2 = Developer("Jane Smith", "Backend Developer")

    manager1 = Manager("Alice Johnson", "Project Manager")
    manager1.add_employee(dev1)
    manager1.add_employee(dev2)

    dev3 = Developer("Mike Brown", "DevOps Engineer")
    manager2 = Manager("Bob White", "CTO")
    manager2.add_employee(manager1)
    manager2.add_employee(dev3)

    manager2.show_details()
```
:::

# Decorator（デコレーター）パターン

## メリット

- 機能の追加が容易

基本機能に対して動的に新しい機能を追加できるため、柔軟性が高まります。

- オブジェクトの変更が不要

デコレーターは元のオブジェクトを変更せずに機能を追加するため、既存のコードを壊すことなく拡張できます。

- コンポーネントの組み合わせ

複数のデコレーターを組み合わせて使用することで、複雑な機能を構築できます。

- オープン/クローズド原則の遵守

クラスの拡張に対してオープンであり、修正に対してクローズドな設計を実現します。

- 動的な機能の切り替え

実行時に動的にデコレーターを追加または削除することで、機能を切り替えることができます。

## デメリット

- 複雑性の増加

デコレーターが多くなると、コードの理解が難しくなることがあります。

- デバッグの難しさ

デコレーターが多重に適用されると、デバッグが難しくなることがあります。

- メモリ使用量の増加

デコレーターを多用すると、メモリ使用量が増加する可能性があります。

## 採用場面

- 動的に機能を追加したい場合

既存のオブジェクトに対して動的に機能を追加したい場合に有効です。

- コンポーネントの組み合わせが必要な場合

機能を小さなコンポーネントに分割し、それらを組み合わせて複雑な機能を構築する場合に適しています。

- オープン/クローズド原則を遵守したい場合

新しい機能を追加する際に、既存のコードを変更せずに拡張したい場合に有効です。

- クラスの数を抑えたい場合

継承によるクラスの増加を避け、デコレーターを使用して機能を追加する場合に適しています。

## 実際の適用例

- GUIウィジェット

基本的なウィジェットに対してスクロールバーやボーダーを動的に追加するために使用。

- ストリーム処理

基本的なストリーム（入力ストリーム、出力ストリーム）に対してバッファリングやフィルタリング機能を追加するために使用。

- テキストフォーマッティング

基本的なテキストに対して太字、斜体、下線などのスタイルを動的に追加するために使用。

- ログ機能の追加

基本的な機能に対してログ出力機能を追加するために使用。

- 入力検証

基本的な入力フィールドに対して検証機能（必須チェック、フォーマットチェック）を動的に追加するために使用。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
interface Shape {
    void draw();
}

class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing Circle");
    }
}

abstract class ShapeDecorator implements Shape {
    protected Shape decoratedShape;

    public ShapeDecorator(Shape decoratedShape) {
        this.decoratedShape = decoratedShape;
    }

    public void draw() {
        decoratedShape.draw();
    }
}

class RedShapeDecorator extends ShapeDecorator {
    public RedShapeDecorator(Shape decoratedShape) {
        super(decoratedShape);
    }

    @Override
    public void draw() {
        decoratedShape.draw();
        setRedBorder(decoratedShape);
    }

    private void setRedBorder(Shape decoratedShape) {
        System.out.println("Border Color: Red");
    }
}

class GreenShapeDecorator extends ShapeDecorator {
    public GreenShapeDecorator(Shape decoratedShape) {
        super(decoratedShape);
    }

    @Override
    public void draw() {
        decoratedShape.draw();
        setGreenBorder(decoratedShape);
    }

    private void setGreenBorder(Shape decoratedShape) {
        System.out.println("Border Color: Green");
    }
}
```

```java
public class DecoratorPatternExample {
    public static void main(String[] args) {
        Shape circle = new Circle();

        Shape redCircle = new RedShapeDecorator(new Circle());
        Shape greenCircle = new GreenShapeDecorator(new Circle());

        System.out.println("Normal Circle:");
        circle.draw();

        System.out.println("\nRed Decorated Circle:");
        redCircle.draw();

        System.out.println("\nGreen Decorated Circle:");
        greenCircle.draw();
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface Shape {
    draw(): void;
}

class Circle implements Shape {
    draw(): void {
        console.log("Drawing Circle");
    }
}

abstract class ShapeDecorator implements Shape {
    protected decoratedShape: Shape;

    constructor(decoratedShape: Shape) {
        this.decoratedShape = decoratedShape;
    }

    draw(): void {
        this.decoratedShape.draw();
    }
}

class RedShapeDecorator extends ShapeDecorator {
    constructor(decoratedShape: Shape) {
        super(decoratedShape);
    }

    draw(): void {
        super.draw();
        this.setRedBorder(this.decoratedShape);
    }

    private setRedBorder(decoratedShape: Shape): void {
        console.log("Border Color: Red");
    }
}

class GreenShapeDecorator extends ShapeDecorator {
    constructor(decoratedShape: Shape) {
        super(decoratedShape);
    }

    draw(): void {
        super.draw();
        this.setGreenBorder(this.decoratedShape);
    }

    private setGreenBorder(decoratedShape: Shape): void {
        console.log("Border Color: Green");
    }
}
```

```typescript
const circle = new Circle();

const redCircle = new RedShapeDecorator(new Circle());
const greenCircle = new GreenShapeDecorator(new Circle());

console.log("Normal Circle:");
circle.draw();

console.log("\nRed Decorated Circle:");
redCircle.draw();

console.log("\nGreen Decorated Circle:");
greenCircle.draw();
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def draw(self):
        pass

class Circle(Shape):
    def draw(self):
        print("Drawing Circle")

class ShapeDecorator(Shape):
    def __init__(self, decorated_shape: Shape):
        self.decorated_shape = decorated_shape

    def draw(self):
        self.decorated_shape.draw()

class RedShapeDecorator(ShapeDecorator):
    def draw(self):
        super().draw()
        self.set_red_border(self.decorated_shape)

    def set_red_border(self, decorated_shape: Shape):
        print("Border Color: Red")

class GreenShapeDecorator(ShapeDecorator):
    def draw(self):
        super().draw()
        self.set_green_border(self.decorated_shape)

    def set_green_border(self, decorated_shape: Shape):
        print("Border Color: Green")
```

```python
if __name__ == "__main__":
    circle = Circle()

    red_circle = RedShapeDecorator(Circle())
    green_circle = GreenShapeDecorator(Circle())

    print("Normal Circle:")
    circle.draw()

    print("\nRed Decorated Circle:")
    red_circle.draw()

    print("\nGreen Decorated Circle:")
    green_circle.draw()
```
:::

# Facade（ファサード）

## メリット

- システムの複雑さを隠蔽

複雑なサブシステムの詳細を隠し、シンプルで使いやすいインターフェースを提供します。

- 使いやすさの向上

クライアントが複雑なサブシステムと直接やり取りする必要がなくなり、使いやすさが向上します。

- コードの分離

クライアントコードとサブシステムコードの依存関係を減らし、コードの分離を促進します。

- メンテナンスの容易さ

サブシステムの変更がファサードインターフェースの背後に隠れるため、クライアントコードへの影響を最小限に抑えます。

- システムの統一

サブシステム間の統一されたインターフェースを提供し、システム全体の一貫性を向上させます。

## デメリット

- ファサード自体が複雑になる可能性

サブシステムが非常に複雑な場合、ファサードの設計が難しくなり、ファサード自体が複雑になる可能性があります。

- 柔軟性の低下

ファサードを使用すると、サブシステムの柔軟な使用が制限されることがあります。

- 過度な依存

クライアントがファサードに過度に依存すると、サブシステムの詳細を直接利用する必要が出た場合に対応が難しくなることがあります。

## 採用場面

- 複雑なサブシステムの簡素化

複雑なサブシステムをシンプルなインターフェースで隠蔽し、クライアントが簡単に使用できるようにする場合。

- 異なるシステムの統合

異なるシステムやモジュールを統一されたインターフェースで利用する必要がある場合。

- レガシーシステムとの互換性

レガシーシステムをモダンなインターフェースで利用できるようにする場合。

- クライアントコードの簡素化

クライアントコードを簡素化し、サブシステムの詳細を隠蔽する必要がある場合。

## 実際の適用例

- メディア変換ライブラリ

複数の異なる形式のメディアファイル（音楽、動画、画像）の変換を行うライブラリを、シンプルなインターフェースで提供。

- データベース操作ライブラリ

複数のデータベース操作（CRUD操作、トランザクション管理など）をシンプルなインターフェースで提供。

- 支払いゲートウェイ

異なる支払いプロバイダ（PayPal、Stripe、Squareなど）を統一されたインターフェースで利用できるようにする。

- ホームオートメーションシステム

照明、暖房、セキュリティなどの異なるサブシステムを統一されたインターフェースで操作できるようにする。

- レガシーシステムのラッピング

古いシステムの複雑な操作を新しいシステムで簡単に利用できるようにするためのラッパーを提供。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
class CPU {
    public void freeze() {
        System.out.println("CPU is frozen");
    }

    public void jump(long position) {
        System.out.println("CPU jumps to position " + position);
    }

    public void execute() {
        System.out.println("CPU is executing");
    }
}

class Memory {
    public void load(long position, byte[] data) {
        System.out.println("Memory is loading data at position " + position);
    }
}

class HardDrive {
    public byte[] read(long lba, int size) {
        System.out.println("HardDrive is reading " + size + " bytes at LBA " + lba);
        return new byte[size];
    }
}

class Computer {
    private CPU cpu;
    private Memory memory;
    private HardDrive hardDrive;

    public Computer() {
        this.cpu = new CPU();
        this.memory = new Memory();
        this.hardDrive = new HardDrive();
    }

    public void startComputer() {
        cpu.freeze();
        memory.load(0, hardDrive.read(0, 1024));
        cpu.jump(0);
        cpu.execute();
    }
}
```

```java
public class FacadePatternExample {
    public static void main(String[] args) {
        Computer computer = new Computer();
        computer.startComputer();
    }
}
```
:::

:::details TypeScriptコード
```typescript
class CPU {
    freeze(): void {
        console.log("CPU is frozen");
    }

    jump(position: number): void {
        console.log(`CPU jumps to position ${position}`);
    }

    execute(): void {
        console.log("CPU is executing");
    }
}

class Memory {
    load(position: number, data: Uint8Array): void {
        console.log(`Memory is loading data at position ${position}`);
    }
}

class HardDrive {
    read(lba: number, size: number): Uint8Array {
        console.log(`HardDrive is reading ${size} bytes at LBA ${lba}`);
        return new Uint8Array(size);
    }
}

class Computer {
    private cpu: CPU;
    private memory: Memory;
    private hardDrive: HardDrive;

    constructor() {
        this.cpu = new CPU();
        this.memory = new Memory();
        this.hardDrive = new HardDrive();
    }

    startComputer(): void {
        this.cpu.freeze();
        this.memory.load(0, this.hardDrive.read(0, 1024));
        this.cpu.jump(0);
        this.cpu.execute();
    }
}
```

```typescript
const computer = new Computer();
computer.startComputer();
```
:::

:::details Pythonコード
```python
class CPU:
    def freeze(self):
        print("CPU is frozen")

    def jump(self, position):
        print(f"CPU jumps to position {position}")

    def execute(self):
        print("CPU is executing")

class Memory:
    def load(self, position, data):
        print(f"Memory is loading data at position {position}")

class HardDrive:
    def read(self, lba, size):
        print(f"HardDrive is reading {size} bytes at LBA {lba}")
        return bytearray(size)

class Computer:
    def __init__(self):
        self.cpu = CPU()
        self.memory = Memory()
        self.hard_drive = HardDrive()

    def start_computer(self):
        self.cpu.freeze()
        self.memory.load(0, self.hard_drive.read(0, 1024))
        self.cpu.jump(0)
        self.cpu.execute()
```

```python
if __name__ == "__main__":
    computer = Computer()
    computer.start_computer()
```
:::

# Flyweight（フライウェイト）パターン

## メリット

- メモリ使用量の削減

多くの細かいオブジェクトを共有して使用するため、メモリの使用量を大幅に削減できます。

- パフォーマンスの向上

メモリの節約により、システム全体のパフォーマンスが向上します。

- 再利用性の向上

オブジェクトを共有することで、同じデータを持つオブジェクトの再利用性が向上します。

- 一貫性の保持

共有オブジェクトを使用することで、データの一貫性を保つことができます。

## デメリット

- 複雑性の増加

フライウェイトパターンを適用するためには、状態を内部状態と外部状態に分けて管理する必要があり、設計が複雑になります。

- 初期設定のコスト

オブジェクトの共有を管理するための工夫が必要であり、初期設定に時間がかかることがあります。

- コードの可読性の低下

共有オブジェクトと外部状態の管理が増えるため、コードの可読性が低下する可能性があります。

## 採用場面

- 大量の同種オブジェクトが必要な場合

同じデータを持つ多数のオブジェクトを生成する必要がある場合に有効です。

- メモリ使用量が問題となる場合

メモリリソースが限られている環境で、メモリ使用量を最小化したい場合。

- データの一貫性を保つ必要がある場合

同じデータを持つオブジェクトを一貫して使用する必要がある場合に有効です。

## 実際の適用例

- テキストエディタ

文字ごとにオブジェクトを生成するのではなく、共有オブジェクトを使用してメモリを節約。

- グラフィックシステム

複数のオブジェクト（例えば、円や線）を共有し、メモリ使用量を削減。

- ゲーム開発

同じ特性を持つ多くのキャラクターやオブジェクトを共有し、メモリの使用を最小化。

- データベース接続

データベース接続オブジェクトを共有し、リソースの使用を最小化。

- 設定情報の共有

アプリケーション全体で使用される設定情報オブジェクトを共有し、一貫性を保つ。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
import java.util.HashMap;
import java.util.Map;

interface Shape {
    void draw();
}

class Circle implements Shape {
    private String color;
    private int x;
    private int y;
    private int radius;

    public Circle(String color) {
        this.color = color;
    }

    public void setX(int x) {
        this.x = x;
    }

    public void setY(int y) {
        this.y = y;
    }

    public void setRadius(int radius) {
        this.radius = radius;
    }

    @Override
    public void draw() {
        System.out.println("Drawing Circle [Color: " + color + ", x: " + x + ", y: " + y + ", radius: " + radius + "]");
    }
}

class ShapeFactory {
    private static final Map<String, Shape> circleMap = new HashMap<>();

    public static Shape getCircle(String color) {
        Circle circle = (Circle) circleMap.get(color);

        if (circle == null) {
            circle = new Circle(color);
            circleMap.put(color, circle);
            System.out.println("Creating circle of color : " + color);
        }
        return circle;
    }
}
```

```java
public class FlyweightPatternExample {
    private static final String[] colors = {"Red", "Green", "Blue", "White", "Black"};

    public static void main(String[] args) {
        for (int i = 0; i < 20; ++i) {
            Circle circle = (Circle) ShapeFactory.getCircle(getRandomColor());
            circle.setX(getRandomX());
            circle.setY(getRandomY());
            circle.setRadius(100);
            circle.draw();
        }
    }

    private static String getRandomColor() {
        return colors[(int) (Math.random() * colors.length)];
    }

    private static int getRandomX() {
        return (int) (Math.random() * 100);
    }

    private static int getRandomY() {
        return (int) (Math.random() * 100);
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface Shape {
    draw(): void;
}

class Circle implements Shape {
    private color: string;
    private x: number;
    private y: number;
    private radius: number;

    constructor(color: string) {
        this.color = color;
    }

    setX(x: number): void {
        this.x = x;
    }

    setY(y: number): void {
        this.y = y;
    }

    setRadius(radius: number): void {
        this.radius = radius;
    }

    draw(): void {
        console.log(`Drawing Circle [Color: ${this.color}, x: ${this.x}, y: ${this.y}, radius: ${this.radius}]`);
    }
}

class ShapeFactory {
    private static circleMap: { [key: string]: Circle } = {};

    static getCircle(color: string): Shape {
        let circle = this.circleMap[color];

        if (!circle) {
            circle = new Circle(color);
            this.circleMap[color] = circle;
            console.log(`Creating circle of color : ${color}`);
        }
        return circle;
    }
}
```

```typescript
const colors: string[] = ["Red", "Green", "Blue", "White", "Black"];

function getRandomColor(): string {
    return colors[Math.floor(Math.random() * colors.length)];
}

function getRandomX(): number {
    return Math.floor(Math.random() * 100);
}

function getRandomY(): number {
    return Math.floor(Math.random() * 100);
}

for (let i = 0; i < 20; ++i) {
    const circle = ShapeFactory.getCircle(getRandomColor()) as Circle;
    circle.setX(getRandomX());
    circle.setY(getRandomY());
    circle.setRadius(100);
    circle.draw();
}
```
:::

:::details Pythonコード
```python
import random

class Shape:
    def draw(self):
        pass

class Circle(Shape):
    def __init__(self, color):
        self.color = color
        self.x = 0
        self.y = 0
        self.radius = 0

    def set_x(self, x):
        self.x = x

    def set_y(self, y):
        self.y = y

    def set_radius(self, radius):
        self.radius = radius

    def draw(self):
        print(f"Drawing Circle [Color: {self.color}, x: {self.x}, y: {self.y}, radius: {self.radius}]")

class ShapeFactory:
    circle_map = {}

    @staticmethod
    def get_circle(color):
        circle = ShapeFactory.circle_map.get(color)

        if circle is None:
            circle = Circle(color)
            ShapeFactory.circle_map[color] = circle
            print(f"Creating circle of color : {color}")
        return circle
```

```python
colors = ["Red", "Green", "Blue", "White", "Black"]

def get_random_color():
    return random.choice(colors)

def get_random_x():
    return random.randint(0, 100)

def get_random_y():
    return random.randint(0, 100)

if __name__ == "__main__":
    for _ in range(20):
        circle = ShapeFactory.get_circle(get_random_color())
        circle.set_x(get_random_x())
        circle.set_y(get_random_y())
        circle.set_radius(100)
        circle.draw()
```
:::

# Proxy（プロキシ）パターン

## メリット

- アクセス制御

プロキシを通じてオブジェクトへのアクセスを制御することができます。これにより、認証や権限管理を容易に実装できます。

- 遅延初期化

実際のオブジェクトの生成や初期化を遅延させることで、リソースの使用を最適化します。必要になるまでオブジェクトを生成しないため、パフォーマンスが向上します。

- ロギングや監視

プロキシを介してオブジェクトの操作をログに記録したり、監視することができます。デバッグやメトリクス収集に役立ちます。

- 分散オブジェクトの利用

リモートオブジェクトのアクセスを抽象化し、分散システムの一部として扱うことができます。ネットワーク通信の詳細を隠蔽します。

- キャッシング

プロキシを通じてオブジェクトの結果をキャッシュすることで、パフォーマンスを向上させることができます。頻繁にアクセスされるデータの再計算を防ぎます。

## デメリット

- オーバーヘッド

プロキシを介することで、呼び出しに余分なオーバーヘッドが発生します。特に高頻度の操作ではパフォーマンスに影響が出ることがあります。

- 設計の複雑化

プロキシパターンを導入することで、システムの設計が複雑になり、コードの可読性が低下する可能性があります。

- デバッグの難しさ

プロキシを介した操作が多重になると、デバッグが難しくなることがあります。特に、問題の原因を特定するのが困難になります。

## 採用場面

- アクセス制御が必要な場合

認証や権限管理を実装する必要がある場合。

- リソースの最適化が必要な場合

オブジェクトの生成や初期化を遅延させ、リソースの使用を最適化したい場合。

- ロギングや監視が必要な場合

オブジェクトの操作をログに記録したり、監視する必要がある場合。

- 分散システムの一部として利用する場合

リモートオブジェクトのアクセスを抽象化し、分散システムの一部として扱う必要がある場合。

- キャッシングが必要な場合

頻繁にアクセスされるデータをキャッシュしてパフォーマンスを向上させたい場合。

## 実際の適用例

- 仮想プロキシ

実際のオブジェクトの生成を遅延させ、必要になるまでリソースを節約する。

- リモートプロキシ

リモートオブジェクトへのアクセスを抽象化し、ネットワーク通信の詳細を隠蔽する。

- 保護プロキシ

アクセス制御を行い、権限のないユーザーからオブジェクトへのアクセスを防ぐ。

- スマートプロキシ

オブジェクトの操作を監視し、追加の処理（ロギングやキャッシングなど）を行う。

- キャッシングプロキシ

オブジェクトの結果をキャッシュし、頻繁にアクセスされるデータの再計算を防ぐ。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
interface Image {
    void display();
}

class RealImage implements Image {
    private String fileName;

    public RealImage(String fileName) {
        this.fileName = fileName;
        loadFromDisk(fileName);
    }

    private void loadFromDisk(String fileName) {
        System.out.println("Loading " + fileName);
    }

    @Override
    public void display() {
        System.out.println("Displaying " + fileName);
    }
}

class ProxyImage implements Image {
    private RealImage realImage;
    private String fileName;

    public ProxyImage(String fileName) {
        this.fileName = fileName;
    }

    @Override
    public void display() {
        if (realImage == null) {
            realImage = new RealImage(fileName);
        }
        realImage.display();
    }
}
```

```java
public class ProxyPatternExample {
    public static void main(String[] args) {
        Image image = new ProxyImage("test_image.jpg");

        // Image will be loaded from disk
        image.display();
        System.out.println("");

        // Image will not be loaded from disk
        image.display();
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface Image {
    display(): void;
}

class RealImage implements Image {
    private fileName: string;

    constructor(fileName: string) {
        this.fileName = fileName;
        this.loadFromDisk(fileName);
    }

    private loadFromDisk(fileName: string): void {
        console.log(`Loading ${fileName}`);
    }

    display(): void {
        console.log(`Displaying ${this.fileName}`);
    }
}

class ProxyImage implements Image {
    private realImage: RealImage | null = null;
    private fileName: string;

    constructor(fileName: string) {
        this.fileName = fileName;
    }

    display(): void {
        if (this.realImage === null) {
            this.realImage = new RealImage(this.fileName);
        }
        this.realImage.display();
    }
}
```

```typescript
const image: Image = new ProxyImage("test_image.jpg");

// Image will be loaded from disk
image.display();
console.log("");

// Image will not be loaded from disk
image.display();
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class Image(ABC):
    @abstractmethod
    def display(self):
        pass

class RealImage(Image):
    def __init__(self, file_name):
        self.file_name = file_name
        self.load_from_disk(file_name)

    def load_from_disk(self, file_name):
        print(f"Loading {file_name}")

    def display(self):
        print(f"Displaying {self.file_name}")

class ProxyImage(Image):
    def __init__(self, file_name):
        self.file_name = file_name
        self.real_image = None

    def display(self):
        if self.real_image is None:
            self.real_image = RealImage(self.file_name)
        self.real_image.display()
```

```python
if __name__ == "__main__":
    image = ProxyImage("test_image.jpg")

    # Image will be loaded from disk
    image.display()
    print("")

    # Image will not be loaded from disk
    image.display()
```
:::

# 後半へ

今回は記事を2つに分けることになりました。まさか文字数制限に引っかかるとは思いませんでしたが、続きは次の記事でご覧いただけます。

[次の記事はこちら](https://zenn.dev/kou_kawa/articles/36-design-pattern-02)

最後までお読みいただき、ありがとうございました！
