---
title: "デザインパターン備忘録"
emoji: "🖌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["java", "typescript", "python"]
published: false
---

# はじめに

ChatGPT-4が使用できるようになりました。私も早速使ってみましたが、以前のバージョンに比べてスピードと精度の向上を実感しました。これを機に、デザインパターンについての記事を書いてみることにしました。この記事を通じて勉強にもなり、後で見返すための資料としても活用できるようにしたいと思います。サンプルコードには、Java、TypeScript、Pythonのプログラミング言語を使用しています。

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

# Chain of Responsibility（責任の連鎖）パターン

## メリット

- 柔軟なリクエスト処理

リクエストを処理するオブジェクトを動的に変更できるため、柔軟なリクエスト処理が可能です。

- コードの分離

リクエストを処理する各オブジェクトを独立して設計できるため、責任を分離できます。

- 可読性と保守性の向上

処理ロジックをチェーン内の各オブジェクトに分散させることで、コードの可読性と保守性が向上します。

- 新しい処理の追加が容易

新しい処理を追加する際に、既存のコードを変更する必要がないため、拡張性が高まります。

- 動的な処理の組み立て

実行時に処理の順序や内容を動的に組み替えることができるため、柔軟なシステム設計が可能です。

## デメリット

- デバッグの難しさ

チェーンが長くなると、リクエストがどのオブジェクトで処理されたのかを追跡するのが難しくなります。

- 処理の遅延

リクエストがチェーン内のすべてのオブジェクトを通過する可能性があるため、処理が遅延することがあります。

- 責任の所在が不明確

リクエストがどのオブジェクトで処理されるかが明確でないため、責任の所在が不明確になることがあります。

- チェーンの管理

チェーンの管理が複雑になることがあり、適切な順序でオブジェクトを配置する必要があります。

## 採用場面

- 複数のオブジェクトがリクエストを処理できる場合

リクエストを処理できるオブジェクトが複数あり、各オブジェクトが特定の条件を満たす場合に処理を行う場合に有効です。

- リクエストの処理順序を動的に変更したい場合

リクエストの処理順序を実行時に動的に変更する必要がある場合に適しています。

- 責任を分離したい場合

各オブジェクトが特定の責任を持ち、リクエスト処理のロジックを分離したい場合に有効です。

- 新しい処理を簡単に追加したい場合

新しい処理を追加する際に、既存のコードに影響を与えずに拡張できる場合に適しています。

## 実際の適用例

- エラーハンドリング

異なるレベルのエラーハンドラー（例えば、ファイルエラー、ネットワークエラー、システムエラー）がリクエストを順に処理する。

- イベント処理システム

GUIアプリケーションでのイベント処理（例えば、ボタンのクリック、キーボード入力）を順に処理する。

- 認証と認可

Webアプリケーションでのリクエスト処理チェーン（例えば、認証、認可、ログ記録）を順に実行する。

- ログ記録

異なるログレベル（例えば、デバッグ、情報、警告、エラー）に基づいてログを処理する。

- ワークフロー管理

ビジネスプロセスや注文処理の各ステップをチェーンで処理する。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
abstract class Logger {
    public static int INFO = 1;
    public static int DEBUG = 2;
    public static int ERROR = 3;

    protected int level;
    protected Logger nextLogger;

    public void setNextLogger(Logger nextLogger) {
        this.nextLogger = nextLogger;
    }

    public void logMessage(int level, String message) {
        if (this.level <= level) {
            write(message);
        }
        if (nextLogger != null) {
            nextLogger.logMessage(level, message);
        }
    }

    abstract protected void write(String message);
}

class ConsoleLogger extends Logger {
    public ConsoleLogger(int level) {
        this.level = level;
    }

    @Override
    protected void write(String message) {
        System.out.println("Standard Console::Logger: " + message);
    }
}

class FileLogger extends Logger {
    public FileLogger(int level) {
        this.level = level;
    }

    @Override
    protected void write(String message) {
        System.out.println("File::Logger: " + message);
    }
}

class ErrorLogger extends Logger {
    public ErrorLogger(int level) {
        this.level = level;
    }

    @Override
    protected void write(String message) {
        System.out.println("Error Console::Logger: " + message);
    }
}
```

```java
public class ChainPatternExample {
    private static Logger getChainOfLoggers() {
        Logger errorLogger = new ErrorLogger(Logger.ERROR);
        Logger fileLogger = new FileLogger(Logger.DEBUG);
        Logger consoleLogger = new ConsoleLogger(Logger.INFO);

        errorLogger.setNextLogger(fileLogger);
        fileLogger.setNextLogger(consoleLogger);

        return errorLogger;
    }

    public static void main(String[] args) {
        Logger loggerChain = getChainOfLoggers();

        loggerChain.logMessage(Logger.INFO, "This is an information.");
        loggerChain.logMessage(Logger.DEBUG, "This is a debug level information.");
        loggerChain.logMessage(Logger.ERROR, "This is an error information.");
    }
}
```
:::

:::details TypeScriptコード
```typescript
abstract class Logger {
    public static INFO: number = 1;
    public static DEBUG: number = 2;
    public static ERROR: number = 3;

    protected level: number;
    protected nextLogger: Logger;

    public setNextLogger(nextLogger: Logger): void {
        this.nextLogger = nextLogger;
    }

    public logMessage(level: number, message: string): void {
        if (this.level <= level) {
            this.write(message);
        }
        if (this.nextLogger) {
            this.nextLogger.logMessage(level, message);
        }
    }

    abstract protected write(message: string): void;
}

class ConsoleLogger extends Logger {
    constructor(level: number) {
        super();
        this.level = level;
    }

    protected write(message: string): void {
        console.log("Standard Console::Logger: " + message);
    }
}

class FileLogger extends Logger {
    constructor(level: number) {
        super();
        this.level = level;
    }

    protected write(message: string): void {
        console.log("File::Logger: " + message);
    }
}

class ErrorLogger extends Logger {
    constructor(level: number) {
        super();
        this.level = level;
    }

    protected write(message: string): void {
        console.log("Error Console::Logger: " + message);
    }
}
```

```typescript
function getChainOfLoggers(): Logger {
    const errorLogger = new ErrorLogger(Logger.ERROR);
    const fileLogger = new FileLogger(Logger.DEBUG);
    const consoleLogger = new ConsoleLogger(Logger.INFO);

    errorLogger.setNextLogger(fileLogger);
    fileLogger.setNextLogger(consoleLogger);

    return errorLogger;
}

const loggerChain = getChainOfLoggers();

loggerChain.logMessage(Logger.INFO, "This is an information.");
loggerChain.logMessage(Logger.DEBUG, "This is a debug level information.");
loggerChain.logMessage(Logger.ERROR, "This is an error information.");
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class Logger(ABC):
    INFO = 1
    DEBUG = 2
    ERROR = 3

    def __init__(self, level):
        self.level = level
        self.next_logger = None

    def set_next_logger(self, next_logger):
        self.next_logger = next_logger

    def log_message(self, level, message):
        if self.level <= level:
            self.write(message)
        if self.next_logger is not None:
            self.next_logger.log_message(level, message)

    @abstractmethod
    def write(self, message):
        pass

class ConsoleLogger(Logger):
    def write(self, message):
        print("Standard Console::Logger: " + message)

class FileLogger(Logger):
    def write(self, message):
        print("File::Logger: " + message)

class ErrorLogger(Logger):
    def write(self, message):
        print("Error Console::Logger: " + message)
```

```python
def get_chain_of_loggers():
    error_logger = ErrorLogger(Logger.ERROR)
    file_logger = FileLogger(Logger.DEBUG)
    console_logger = ConsoleLogger(Logger.INFO)

    error_logger.set_next_logger(file_logger)
    file_logger.set_next_logger(console_logger)

    return error_logger

if __name__ == "__main__":
    logger_chain = get_chain_of_loggers()

    logger_chain.log_message(Logger.INFO, "This is an information.")
    logger_chain.log_message(Logger.DEBUG, "This is a debug level information.")
    logger_chain.log_message(Logger.ERROR, "This is an error information.")
```
:::

# Command（コマンド）パターン

## メリット

- 操作のカプセル化

コマンドをオブジェクトとしてカプセル化することで、操作そのものを遅延実行したり、キューイングしたり、ログに保存したりできます。

- 拡張性の向上

新しいコマンドを追加する際に、既存のコードに影響を与えることなく拡張できます。

- 一貫性のある操作管理

一貫したインターフェースを通じて操作を実行するため、クライアントコードが簡潔で保守しやすくなります。

- 操作の取り消し（Undo）や再実行（Redo）の実装が容易

コマンドオブジェクトに取り消し操作を実装することで、複雑なUndo/Redo機能を容易に実現できます。

- 操作のログ保存

コマンドをログに保存することで、操作の履歴を保持し、再実行することができます。

## デメリット

- コードの複雑化

コマンドごとにクラスを作成するため、コードが増加し、設計が複雑になる可能性があります。

- オーバーヘッドの増加

コマンドオブジェクトの生成や管理に追加のオーバーヘッドが発生します。

- シンプルな操作には過剰

単純な操作に対しては、コマンドパターンの導入が過剰であり、不要な複雑性を導入することがあります。

## 採用場面

- 操作の取り消しや再実行が必要な場合

ユーザーインターフェースでのUndo/Redo機能を実装する際に有効です。

- 操作をキューイングしたい場合

操作をキューに入れて順番に実行したい場合に適しています。

- 操作のログ保存が必要な場合

操作の履歴を保存し、後で再実行したり分析したりする必要がある場合。

- 操作のパラメータ化が必要な場合

操作をパラメータとして扱い、動的に構成したい場合に適しています。

- 複雑な操作を簡単に実行したい場合

複雑な操作を簡単に実行し、拡張や保守が容易になる場合。

## 実際の適用例

- テキストエディタ

ユーザーが行った編集操作（例えば、文字の挿入、削除、スタイルの変更）をコマンドとして記録し、Undo/Redo機能を実装する。

- グラフィックエディタ

図形の描画、移動、削除などの操作をコマンドとして記録し、ユーザーが操作を取り消したり再実行したりできるようにする。

- トランザクションシステム

データベースの操作をコマンドとして記録し、トランザクションの管理やログの保存を行う。

- ゲーム開発

プレイヤーのアクション（例えば、移動、攻撃、アイテム使用）をコマンドとして記録し、アクションの再生や取り消しを行う。

- タスクスケジューラ

タスクをコマンドとしてキューに追加し、順番に実行する。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
interface Command {
    void execute();
}

class Light {
    public void turnOn() {
        System.out.println("Light is ON");
    }

    public void turnOff() {
        System.out.println("Light is OFF");
    }
}

class LightOnCommand implements Command {
    private Light light;

    public LightOnCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.turnOn();
    }
}

class LightOffCommand implements Command {
    private Light light;

    public LightOffCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.turnOff();
    }
}

class RemoteControl {
    private Command command;

    public void setCommand(Command command) {
        this.command = command;
    }

    public void pressButton() {
        command.execute();
    }
}
```

```java
public class CommandPatternExample {
    public static void main(String[] args) {
        Light light = new Light();
        Command lightOn = new LightOnCommand(light);
        Command lightOff = new LightOffCommand(light);

        RemoteControl remote = new RemoteControl();

        remote.setCommand(lightOn);
        remote.pressButton();

        remote.setCommand(lightOff);
        remote.pressButton();
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface Command {
    execute(): void;
}

class Light {
    turnOn(): void {
        console.log("Light is ON");
    }

    turnOff(): void {
        console.log("Light is OFF");
    }
}

class LightOnCommand implements Command {
    private light: Light;

    constructor(light: Light) {
        this.light = light;
    }

    execute(): void {
        this.light.turnOn();
    }
}

class LightOffCommand implements Command {
    private light: Light;

    constructor(light: Light) {
        this.light = light;
    }

    execute(): void {
        this.light.turnOff();
    }
}

class RemoteControl {
    private command: Command;

    setCommand(command: Command): void {
        this.command = command;
    }

    pressButton(): void {
        this.command.execute();
    }
}
```

```typescript
const light = new Light();
const lightOn = new LightOnCommand(light);
const lightOff = new LightOffCommand(light);

const remote = new RemoteControl();

remote.setCommand(lightOn);
remote.pressButton();

remote.setCommand(lightOff);
remote.pressButton();
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class Command(ABC):
    @abstractmethod
    def execute(self):
        pass

class Light:
    def turn_on(self):
        print("Light is ON")

    def turn_off(self):
        print("Light is OFF")

class LightOnCommand(Command):
    def __init__(self, light):
        self.light = light

    def execute(self):
        self.light.turn_on()

class LightOffCommand(Command):
    def __init__(self, light):
        self.light = light

    def execute(self):
        self.light.turn_off()

class RemoteControl:
    def __init__(self):
        self.command = None

    def set_command(self, command):
        self.command = command

    def press_button(self):
        self.command.execute()
```

```python
if __name__ == "__main__":
    light = Light()
    light_on = LightOnCommand(light)
    light_off = LightOffCommand(light)

    remote = RemoteControl()

    remote.set_command(light_on)
    remote.press_button()

    remote.set_command(light_off)
    remote.press_button()
```
:::

# Interpreter（インタープリター）パターン

## メリット

- 簡単な文法の構文解析

シンプルな言語の文法を解析するのが容易です。新しい言語やプロトコルの実装が比較的簡単に行えます。

- コードの可読性向上

文法をクラスに分けて設計することで、コードの可読性が向上します。各クラスが特定の文法規則を表現するため、コードの構造が明確になります。

- 拡張性の向上

文法ルールを追加する場合、新しいクラスを追加するだけで済むため、既存のコードに影響を与えることなく拡張できます。

- 再利用性の向上

共通の文法ルールを複数の言語で再利用することができます。

- 柔軟性の向上

複雑な文法規則を持つ言語やプロトコルにも対応可能で、文法規則の変更が容易です。

## デメリット

- パフォーマンスの低下

文法解析のために多くのクラスをインスタンス化するため、パフォーマンスが低下することがあります。特に複雑な文法を扱う場合は顕著です。

- 設計の複雑さ

文法規則ごとにクラスを設計する必要があるため、コードが複雑になりがちです。

- 実行時のメモリ消費

多くのオブジェクトを生成するため、メモリ消費量が増加します。特に、大量の文法規則を扱う場合は注意が必要です。

- 適用範囲の限定

複雑な文法を持つプログラミング言語やプロトコルには適さない場合があります。そのような場合、他のパーサジェネレータやコンパイラツールの使用が推奨されます。

## 採用場面

- 簡単な言語やプロトコルの実装

シンプルなスクリプト言語やデータ記述言語の実装に適しています。

- データ解析

特定の形式で記述されたデータの解析に使用します。

- DSL（ドメイン特化言語）の実装

特定のドメインに特化した言語の実装に適しています。

- テキスト処理

テキストのパースや変換を行う処理に適しています。

## 実際の適用例

- 構成ファイルのパース

アプリケーションの設定ファイル（例えば、INIファイルやJSONファイル）のパースに使用。

- テンプレートエンジン

テンプレート言語の実装に使用し、テンプレートを解析してデータを埋め込む。

- SQLクエリパーサ

SQLクエリを解析して実行計画を生成する。

- 数式評価

数式を解析して結果を計算する。

- ビジネスルールエンジン

ビジネスルールを記述するためのDSLを実装し、ルールを解析して実行する。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
import java.util.Map;
import java.util.HashMap;

interface Expression {
    boolean interpret(Map<String, Boolean> context);
}

class Variable implements Expression {
    private String name;

    public Variable(String name) {
        this.name = name;
    }

    @Override
    public boolean interpret(Map<String, Boolean> context) {
        return context.getOrDefault(name, false);
    }
}

class AndExpression implements Expression {
    private Expression expr1;
    private Expression expr2;

    public AndExpression(Expression expr1, Expression expr2) {
        this.expr1 = expr1;
        this.expr2 = expr2;
    }

    @Override
    public boolean interpret(Map<String, Boolean> context) {
        return expr1.interpret(context) && expr2.interpret(context);
    }
}

class OrExpression implements Expression {
    private Expression expr1;
    private Expression expr2;

    public OrExpression(Expression expr1, Expression expr2) {
        this.expr1 = expr1;
        this.expr2 = expr2;
    }

    @Override
    public boolean interpret(Map<String, Boolean> context) {
        return expr1.interpret(context) || expr2.interpret(context);
    }
}
```

```java
public class InterpreterPatternExample {
    public static void main(String[] args) {
        Expression expr1 = new Variable("x");
        Expression expr2 = new Variable("y");
        Expression andExpr = new AndExpression(expr1, expr2);
        Expression orExpr = new OrExpression(expr1, expr2);

        Map<String, Boolean> context = new HashMap<>();
        context.put("x", true);
        context.put("y", false);

        System.out.println("x AND y: " + andExpr.interpret(context)); // Output: false
        System.out.println("x OR y: " + orExpr.interpret(context));   // Output: true
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface Expression {
    interpret(context: { [key: string]: boolean }): boolean;
}

class Variable implements Expression {
    private name: string;

    constructor(name: string) {
        this.name = name;
    }

    interpret(context: { [key: string]: boolean }): boolean {
        return context[this.name] || false;
    }
}

class AndExpression implements Expression {
    private expr1: Expression;
    private expr2: Expression;

    constructor(expr1: Expression, expr2: Expression) {
        this.expr1 = expr1;
        this.expr2 = expr2;
    }

    interpret(context: { [key: string]: boolean }): boolean {
        return this.expr1.interpret(context) && this.expr2.interpret(context);
    }
}

class OrExpression implements Expression {
    private expr1: Expression;
    private expr2: Expression;

    constructor(expr1: Expression, expr2: Expression) {
        this.expr1 = expr1;
        this.expr2 = expr2;
    }

    interpret(context: { [key: string]: boolean }): boolean {
        return this.expr1.interpret(context) || this.expr2.interpret(context);
    }
}
```

```typescript
const expr1: Expression = new Variable("x");
const expr2: Expression = new Variable("y");
const andExpr: Expression = new AndExpression(expr1, expr2);
const orExpr: Expression = new OrExpression(expr1, expr2);

const context: { [key: string]: boolean } = { x: true, y: false };

console.log("x AND y: " + andExpr.interpret(context)); // Output: false
console.log("x OR y: " + orExpr.interpret(context));   // Output: true
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class Expression(ABC):
    @abstractmethod
    def interpret(self, context):
        pass

class Variable(Expression):
    def __init__(self, name):
        self.name = name

    def interpret(self, context):
        return context.get(self.name, False)

class AndExpression(Expression):
    def __init__(self, expr1, expr2):
        self.expr1 = expr1
        self.expr2 = expr2

    def interpret(self, context):
        return self.expr1.interpret(context) and self.expr2.interpret(context)

class OrExpression(Expression):
    def __init__(self, expr1, expr2):
        self.expr1 = expr1
        self.expr2 = expr2

    def interpret(self, context):
        return self.expr1.interpret(context) or self.expr2.interpret(context)
```

```python
if __name__ == "__main__":
    expr1 = Variable("x")
    expr2 = Variable("y")
    and_expr = AndExpression(expr1, expr2)
    or_expr = OrExpression(expr1, expr2)

    context = {"x": True, "y": False}

    print("x AND y:", and_expr.interpret(context))  # Output: False
    print("x OR y:", or_expr.interpret(context))    # Output: True
```
:::

# Iterator（イテレーター）パターン

## メリット

- アクセスの統一

コレクションの内部構造を隠蔽し、統一された方法でコレクションの要素にアクセスできます。

- 複数の同時トラバース

複数のイテレーターを使って、同じコレクションを独立してトラバースすることができます。

- シンプルなインターフェース

コレクションの要素に対する操作を単純化し、次の要素へのアクセスや操作をシンプルに実装できます。

- 責務の分離

コレクション自体とコレクションをトラバースするロジックを分離することで、クラスの責務を明確に分けることができます。

- 拡張性

新しい種類のコレクションが追加されても、イテレーターのインターフェースを実装するだけで同じ方法でトラバースできるようになります。

## デメリット

- 複雑性の増加

小さなコレクションや単純なデータ構造に対しては、イテレーターを導入することで逆に複雑性が増すことがあります。

- パフォーマンスのオーバーヘッド

イテレーターを利用する際のメソッド呼び出しによって、直接アクセスに比べて若干のパフォーマンスオーバーヘッドが発生することがあります。

- 一時オブジェクトの生成

イテレーターを生成することで、一時オブジェクトが生成され、メモリ消費が増加することがあります。

## 採用場面

- 複雑なデータ構造のトラバース

複雑なデータ構造（例えば、ツリー、グラフ、複雑なリスト構造）を統一した方法でトラバースしたい場合。

- 抽象データ型の実装

異なる種類のコレクションに対して、同じインターフェースでアクセスしたい場合。

- 複数のトラバース方法

コレクションのトラバース方法が複数あり、それらを容易に切り替えたい場合。

- 複数のコレクションの統一管理

異なるコレクションを同じ方法で操作し、一貫したトラバースを提供したい場合。

## 実際の適用例

- コレクションフレームワーク

Javaのjava.util.IteratorやC#のSystem.Collections.IEnumeratorのように、コレクションをトラバースするための標準インターフェースを提供。

- ファイルシステムトラバース

ファイルシステムのディレクトリ構造をトラバースするためのイテレーターを実装。

- データベースカーソル

データベースのクエリ結果をトラバースするためのイテレーター。

- グラフィックオブジェクト

グラフィックオブジェクトのコンポジット構造をトラバースするためのイテレーター。

- パーサ

構文解析ツールで使用されるイテレーター。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
import java.util.ArrayList;
import java.util.List;

interface Container {
    Iterator getIterator();
}

class NameRepository implements Container {
    private List<String> names = new ArrayList<>();

    public NameRepository() {
        names.add("Robert");
        names.add("John");
        names.add("Julie");
        names.add("Lora");
    }

    @Override
    public Iterator getIterator() {
        return new NameIterator();
    }

    private class NameIterator implements Iterator {
        int index;

        @Override
        public boolean hasNext() {
            return index < names.size();
        }

        @Override
        public Object next() {
            if (this.hasNext()) {
                return names.get(index++);
            }
            return null;
        }
    }
}

interface Iterator {
    boolean hasNext();
    Object next();
}
```

```java
public class IteratorPatternExample {
    public static void main(String[] args) {
        NameRepository namesRepository = new NameRepository();

        for (Iterator iter = namesRepository.getIterator(); iter.hasNext(); ) {
            String name = (String) iter.next();
            System.out.println("Name: " + name);
        }
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface Iterator<T> {
    hasNext(): boolean;
    next(): T;
}

interface Container<T> {
    getIterator(): Iterator<T>;
}

class NameRepository implements Container<string> {
    private names: string[] = ["Robert", "John", "Julie", "Lora"];

    getIterator(): Iterator<string> {
        return new NameIterator(this.names);
    }
}

class NameIterator implements Iterator<string> {
    private index: number = 0;

    constructor(private names: string[]) {}

    hasNext(): boolean {
        return this.index < this.names.length;
    }

    next(): string {
        if (this.hasNext()) {
            return this.names[this.index++];
        }
        return null;
    }
}
```

```typescript
const namesRepository = new NameRepository();

for (let iterator = namesRepository.getIterator(); iterator.hasNext(); ) {
    const name = iterator.next();
    console.log("Name: " + name);
}
```
:::

:::details Pythonコード
```python
from typing import List, Iterator as TypingIterator

class Iterator(ABC):
    @abstractmethod
    def has_next(self) -> bool:
        pass

    @abstractmethod
    def next(self):
        pass

class Container(ABC):
    @abstractmethod
    def get_iterator(self) -> Iterator:
        pass

class NameRepository(Container):
    def __init__(self):
        self.names = ["Robert", "John", "Julie", "Lora"]

    def get_iterator(self) -> Iterator:
        return NameIterator(self.names)

class NameIterator(Iterator):
    def __init__(self, names: List[str]):
        self.index = 0
        self.names = names

    def has_next(self) -> bool:
        return self.index < len(self.names)

    def next(self):
        if self.has_next():
            result = self.names[self.index]
            self.index += 1
            return result
        return None
```

```python
if __name__ == "__main__":
    names_repository = NameRepository()

    iterator = names_repository.get_iterator()
    while iterator.has_next():
        name = iterator.next()
        print("Name:", name)
```
:::

# Mediator（メディエーター）パターン

## メリット

- オブジェクト間の依存関係を低減

オブジェクト同士が直接やり取りする代わりにメディエーターを介して通信するため、オブジェクト間の結合度が低くなります。

- コードの可読性向上

相互作用がメディエーターに集中するため、各オブジェクトのコードがシンプルで読みやすくなります。

- 集中管理

メディエーターが全ての相互作用を管理するため、複雑な通信ロジックを一箇所に集約でき、管理が容易になります。

- 拡張性の向上

新しいオブジェクトや相互作用を追加する際に、既存のオブジェクトに影響を与えることなく拡張が可能です。

- デバッグの容易化

相互作用がメディエーターに集中しているため、通信ロジックのデバッグが容易になります。

## デメリット

- メディエーターの複雑化

メディエーターが複雑なロジックを扱うと、メディエーター自体が巨大化し、保守が難しくなることがあります。

- パフォーマンスの低下

すべての通信をメディエーターが仲介するため、大量の通信が発生するとパフォーマンスに影響を与えることがあります。

- 単一障害点

メディエーターが故障すると、全ての通信が停止するため、システムの信頼性がメディエーターに依存します。

## 採用場面

- 複雑なオブジェクト間の通信

多くのオブジェクトが複雑に相互作用するシステムで、オブジェクト間の通信を整理する必要がある場合。

- オブジェクト間の低結合が重要な場合

オブジェクト間の結合度を下げて、変更が容易になるようにしたい場合。

- 集中管理が必要な場合

複雑なロジックを一箇所で集中管理し、システム全体の相互作用を簡素化したい場合。

## 実際の適用例

- GUIフレームワーク

複数のGUIコンポーネント（ボタン、テキストフィールド、リストなど）の相互作用をメディエーターが管理し、各コンポーネントが互いに直接通信しないようにする。

- チャットシステム

各ユーザーがメッセージを送受信する際に、メディエーターがメッセージを中継し、各ユーザーが直接通信しないようにする。

- 航空管制システム

各航空機がメディエーターを介して互いに通信し、衝突を回避するための情報を共有する。

- モジュール間通信

複数のモジュールがメディエーターを介して相互作用し、直接の依存関係を避ける。

- ワークフロー管理

複数の業務プロセスがメディエーターを介して連携し、ワークフローを管理する。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
import java.util.ArrayList;
import java.util.List;

interface ChatMediator {
    void sendMessage(String message, User user);
    void addUser(User user);
}

class ChatMediatorImpl implements ChatMediator {
    private List<User> users;

    public ChatMediatorImpl() {
        this.users = new ArrayList<>();
    }

    @Override
    public void addUser(User user) {
        this.users.add(user);
    }

    @Override
    public void sendMessage(String message, User user) {
        for (User u : this.users) {
            // message should not be received by the user sending it
            if (u != user) {
                u.receive(message);
            }
        }
    }
}

abstract class User {
    protected ChatMediator mediator;
    protected String name;

    public User(ChatMediator mediator, String name) {
        this.mediator = mediator;
        this.name = name;
    }

    public abstract void send(String message);
    public abstract void receive(String message);
}

class UserImpl extends User {
    public UserImpl(ChatMediator mediator, String name) {
        super(mediator, name);
    }

    @Override
    public void send(String message) {
        System.out.println(this.name + " Sending Message: " + message);
        mediator.sendMessage(message, this);
    }

    @Override
    public void receive(String message) {
        System.out.println(this.name + " Received Message: " + message);
    }
}
```

```java
public class MediatorPatternExample {
    public static void main(String[] args) {
        ChatMediator mediator = new ChatMediatorImpl();

        User user1 = new UserImpl(mediator, "User1");
        User user2 = new UserImpl(mediator, "User2");
        User user3 = new UserImpl(mediator, "User3");
        User user4 = new UserImpl(mediator, "User4");

        mediator.addUser(user1);
        mediator.addUser(user2);
        mediator.addUser(user3);
        mediator.addUser(user4);

        user1.send("Hi All");
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface ChatMediator {
    sendMessage(message: string, user: User): void;
    addUser(user: User): void;
}

class ChatMediatorImpl implements ChatMediator {
    private users: User[] = [];

    addUser(user: User): void {
        this.users.push(user);
    }

    sendMessage(message: string, user: User): void {
        for (const u of this.users) {
            if (u !== user) {
                u.receive(message);
            }
        }
    }
}

abstract class User {
    protected mediator: ChatMediator;
    protected name: string;

    constructor(mediator: ChatMediator, name: string) {
        this.mediator = mediator;
        this.name = name;
    }

    abstract send(message: string): void;
    abstract receive(message: string): void;
}

class UserImpl extends User {
    constructor(mediator: ChatMediator, name: string) {
        super(mediator, name);
    }

    send(message: string): void {
        console.log(`${this.name} Sending Message: ${message}`);
        this.mediator.sendMessage(message, this);
    }

    receive(message: string): void {
        console.log(`${this.name} Received Message: ${message}`);
    }
}
```

```typescript
const mediator = new ChatMediatorImpl();

const user1 = new UserImpl(mediator, "User1");
const user2 = new UserImpl(mediator, "User2");
const user3 = new UserImpl(mediator, "User3");
const user4 = new UserImpl(mediator, "User4");

mediator.addUser(user1);
mediator.addUser(user2);
mediator.addUser(user3);
mediator.addUser(user4);

user1.send("Hi All");
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class ChatMediator(ABC):
    @abstractmethod
    def send_message(self, message: str, user: 'User'):
        pass

    @abstractmethod
    def add_user(self, user: 'User'):
        pass

class ChatMediatorImpl(ChatMediator):
    def __init__(self):
        self.users = []

    def add_user(self, user: 'User'):
        self.users.append(user)

    def send_message(self, message: str, user: 'User'):
        for u in self.users:
            if u != user:
                u.receive(message)

class User(ABC):
    def __init__(self, mediator: ChatMediator, name: str):
        self.mediator = mediator
        self.name = name

    @abstractmethod
    def send(self, message: str):
        pass

    @abstractmethod
    def receive(self, message: str):
        pass

class UserImpl(User):
    def send(self, message: str):
        print(f"{self.name} Sending Message: {message}")
        self.mediator.send_message(message, self)

    def receive(self, message: str):
        print(f"{self.name} Received Message: {message}")
```

```python
if __name__ == "__main__":
    mediator = ChatMediatorImpl()

    user1 = UserImpl(mediator, "User1")
    user2 = UserImpl(mediator, "User2")
    user3 = UserImpl(mediator, "User3")
    user4 = UserImpl(mediator, "User4")

    mediator.add_user(user1)
    mediator.add_user(user2)
    mediator.add_user(user3)
    mediator.add_user(user4)

    user1.send("Hi All")
```
:::

# Observer（オブザーバー）パターン

## メリット

- 疎結合

オブザーバーと被観測者（サブジェクト）は疎結合であり、互いに独立して変更可能です。被観測者がオブザーバーの具体的な実装を知らなくても動作します。

- 拡張性

新しいオブザーバーを簡単に追加でき、既存のコードを変更することなく機能を拡張できます。

- リアルタイム更新

状態が変わると即座に通知されるため、リアルタイムでのデータ更新が可能です。

- メンテナンスの容易さ

各オブザーバーが独自に動作するため、個別にテストやメンテナンスが可能です。

## デメリット

- 複雑性の増加

多数のオブザーバーが存在する場合、通知処理が複雑になることがあります。

- パフォーマンスの問題

多数のオブザーバーに対して頻繁に通知を行うと、パフォーマンスが低下する可能性があります。

- 通知の順序

オブザーバーの通知順序が保証されない場合、結果が予測しにくくなることがあります。

- メモリリークのリスク

登録されたオブザーバーが適切に解除されないと、メモリリークの原因となることがあります。

##  採用場面

- イベント駆動型システム

システム内のイベントに応じて、複数のオブザーバーがリアクションを取る必要がある場合。

- 分散システム

状態の変化を複数の異なるコンポーネントに通知する必要がある場合。

- リアルタイムデータフィード

データの変更をリアルタイムに複数のビューやコンポーネントに反映させる必要がある場合。

- 設定変更の反映

設定が変更された際に、それを利用する複数のコンポーネントに通知する必要がある場合。

## 実際の適用例

- GUIフレームワーク

ボタンのクリックやテキストボックスの変更など、ユーザーインターフェースのイベントを監視し、対応するハンドラが実行される。

- イベントバスシステム

イベントを発行し、それを購読しているリスナーに通知する仕組み。

- モデルビューアーキテクチャ（MVC）

モデルの状態が変更された際に、ビューが自動的に更新される。

- リアルタイムデータフィード

株価や天気情報など、リアルタイムのデータフィードを複数のクライアントに通知する。

- ログシステム

アプリケーション内で発生する様々なイベントをキャプチャして、ログとして保存する。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
import java.util.ArrayList;
import java.util.List;

class Subject {
    private List<Observer> observers = new ArrayList<>();
    private int state;

    public int getState() {
        return state;
    }

    public void setState(int state) {
        this.state = state;
        notifyAllObservers();
    }

    public void attach(Observer observer) {
        observers.add(observer);
    }

    public void notifyAllObservers() {
        for (Observer observer : observers) {
            observer.update();
        }
    }
}

abstract class Observer {
    protected Subject subject;
    public abstract void update();
}

class BinaryObserver extends Observer {

    public BinaryObserver(Subject subject) {
        this.subject = subject;
        this.subject.attach(this);
    }

    @Override
    public void update() {
        System.out.println("Binary String: " + Integer.toBinaryString(subject.getState()));
    }
}

class HexObserver extends Observer {

    public HexObserver(Subject subject) {
        this.subject = subject;
        this.subject.attach(this);
    }

    @Override
    public void update() {
        System.out.println("Hex String: " + Integer.toHexString(subject.getState()).toUpperCase());
    }
}
```

```java
public class ObserverPatternExample {
    public static void main(String[] args) {
        Subject subject = new Subject();

        new HexObserver(subject);
        new BinaryObserver(subject);

        System.out.println("First state change: 15");
        subject.setState(15);

        System.out.println("Second state change: 10");
        subject.setState(10);
    }
}
```
:::

:::details TypeScriptコード
```typescript
class Subject {
    private observers: Observer[] = [];
    private state: number;

    getState(): number {
        return this.state;
    }

    setState(state: number): void {
        this.state = state;
        this.notifyAllObservers();
    }

    attach(observer: Observer): void {
        this.observers.push(observer);
    }

    notifyAllObservers(): void {
        for (const observer of this.observers) {
            observer.update();
        }
    }
}

abstract class Observer {
    protected subject: Subject;

    constructor(subject: Subject) {
        this.subject = subject;
        this.subject.attach(this);
    }

    abstract update(): void;
}

class BinaryObserver extends Observer {
    update(): void {
        console.log("Binary String: " + this.subject.getState().toString(2));
    }
}

class HexObserver extends Observer {
    update(): void {
        console.log("Hex String: " + this.subject.getState().toString(16).toUpperCase());
    }
}
```

```typescript
const subject = new Subject();

new HexObserver(subject);
new BinaryObserver(subject);

console.log("First state change: 15");
subject.setState(15);

console.log("Second state change: 10");
subject.setState(10);
```
:::

:::details Pythonコード
```python
class Subject:
    def __init__(self):
        self._observers = []
        self._state = None

    def get_state(self):
        return self._state

    def set_state(self, state):
        self._state = state
        self.notify_all_observers()

    def attach(self, observer):
        self._observers.append(observer)

    def notify_all_observers(self):
        for observer in self._observers:
            observer.update()

class Observer:
    def __init__(self, subject):
        self.subject = subject
        self.subject.attach(self)

    def update(self):
        pass

class BinaryObserver(Observer):
    def update(self):
        print("Binary String: " + bin(self.subject.get_state())[2:])

class HexObserver(Observer):
    def update(self):
        print("Hex String: " + hex(self.subject.get_state())[2:].upper())
```

```python
if __name__ == "__main__":
    subject = Subject()

    HexObserver(subject)
    BinaryObserver(subject)

    print("First state change: 15")
    subject.set_state(15)

    print("Second state change: 10")
    subject.set_state(10)
```
:::

# State（ステート）パターン

## メリット

- 状態遷移の管理が簡単

状態ごとにクラスを分けることで、状態遷移を明確に管理できるため、コードの可読性が向上します。

- オープン/クローズド原則の遵守

新しい状態を追加する際に、既存のコードを変更せずに拡張できるため、オープン/クローズド原則を守ります。

- 複雑な状態遷移の簡素化

状態ごとに異なる振る舞いを持たせることができ、複雑な状態遷移を簡素化できます。

- 責務の分離

各状態の振る舞いを独立したクラスに分離することで、クラスの責務を明確に分けることができます。

- テストの容易さ

各状態を個別にテストできるため、ユニットテストが容易になります。

## デメリット

- クラスの増加

各状態をクラスとして実装するため、クラスの数が増加します。

- 初期設定のコスト

状態パターンを適用するための初期設定に時間がかかることがあります。

- シンプルな状態管理には過剰

シンプルな状態管理には、状態パターンの導入が過剰であり、コードの複雑性を不必要に増加させる可能性があります。

## 採用場面

- オブジェクトの振る舞いが状態に依存する場合

オブジェクトの振る舞いがその状態に応じて異なる場合に有効です。

- 複雑な状態遷移を管理する場合

状態遷移が複雑で、各状態ごとに異なるロジックを実装する必要がある場合に適しています。

- 状態ごとの振る舞いが頻繁に変更される場合

状態ごとの振る舞いが頻繁に変更される場合、状態パターンを使用することで、変更の影響を最小限に抑えることができます。

- 責務の分離が求められる場合

各状態の振る舞いを独立したクラスに分離することで、クラスの責務を明確に分けたい場合に適しています。

## 実際の適用例

- ユーザー認証システム

ユーザーの認証状態（ログイン、ログアウト、認証待ち）を管理し、各状態に応じた処理を実装。

- オーダープロセス

注文の状態（新規注文、処理中、発送済み、キャンセル）を管理し、各状態に応じた処理を実装。

- メディアプレイヤー

メディアプレイヤーの再生状態（再生、停止、一時停止、早送り）を管理し、各状態に応じた操作を実装。

- ワークフローシステム

ワークフローの各ステップ（開始、進行中、完了、エラー）を管理し、各ステップに応じた処理を実装。

- トラフィックライトシステム

信号機の状態（赤、黄、緑）を管理し、各状態に応じた動作を実装。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
interface State {
    void doAction(Context context);
}

class StartState implements State {
    public void doAction(Context context) {
        System.out.println("Player is in start state");
        context.setState(this);
    }

    public String toString() {
        return "Start State";
    }
}

class StopState implements State {
    public void doAction(Context context) {
        System.out.println("Player is in stop state");
        context.setState(this);
    }

    public String toString() {
        return "Stop State";
    }
}

class Context {
    private State state;

    public Context() {
        state = null;
    }

    public void setState(State state) {
        this.state = state;
    }

    public State getState() {
        return state;
    }
}
```

```java
public class StatePatternExample {
    public static void main(String[] args) {
        Context context = new Context();

        StartState startState = new StartState();
        startState.doAction(context);

        System.out.println(context.getState().toString());

        StopState stopState = new StopState();
        stopState.doAction(context);

        System.out.println(context.getState().toString());
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface State {
    doAction(context: Context): void;
}

class StartState implements State {
    doAction(context: Context): void {
        console.log("Player is in start state");
        context.setState(this);
    }

    toString(): string {
        return "Start State";
    }
}

class StopState implements State {
    doAction(context: Context): void {
        console.log("Player is in stop state");
        context.setState(this);
    }

    toString(): string {
        return "Stop State";
    }
}

class Context {
    private state: State;

    constructor() {
        this.state = null;
    }

    setState(state: State): void {
        this.state = state;
    }

    getState(): State {
        return this.state;
    }
}
```

```typescript
const context = new Context();

const startState = new StartState();
startState.doAction(context);

console.log(context.getState().toString());

const stopState = new StopState();
stopState.doAction(context);

console.log(context.getState().toString());
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class State(ABC):
    @abstractmethod
    def do_action(self, context):
        pass

class StartState(State):
    def do_action(self, context):
        print("Player is in start state")
        context.set_state(self)

    def __str__(self):
        return "Start State"

class StopState(State):
    def do_action(self, context):
        print("Player is in stop state")
        context.set_state(self)

    def __str__(self):
        return "Stop State"

class Context:
    def __init__(self):
        self._state = None

    def set_state(self, state):
        self._state = state

    def get_state(self):
        return self._state
```

```python
if __name__ == "__main__":
    context = Context()

    start_state = StartState()
    start_state.do_action(context)
    print(context.get_state())

    stop_state = StopState()
    stop_state.do_action(context)
    print(context.get_state())
```
:::

# Strategy（ストラテジー）パターン

## メリット

- アルゴリズムの分離

アルゴリズムをコンテキスト（使用するクラス）から分離することで、アルゴリズムの実装を独立して管理できます。

- 拡張性の向上

新しいアルゴリズムを追加する際に、既存のコードに影響を与えることなく、簡単に拡張できます。

- コードの再利用

共通のインターフェースを持つことで、異なるアルゴリズムを同じコードで利用でき、コードの再利用性が向上します。

- オープン/クローズド原則の遵守

新しいストラテジーを追加する際に、既存のコンテキストやアルゴリズムのコードを変更せずに拡張できるため、オープン/クローズド原則に従います。

- メンテナンス性の向上

アルゴリズムが分離されているため、各アルゴリズムを独立してテスト・保守でき、メンテナンスが容易になります。

## デメリット

- 複雑性の増加

ストラテジーごとにクラスを作成するため、クラスの数が増え、設計が複雑になることがあります。

- コンテキストとの連携が必要

コンテキストとストラテジー間の連携を正しく設計する必要があり、実装の手間がかかります。

- アルゴリズムの理解が難しくなる

アルゴリズムが分離されているため、全体のフローを理解するのが難しくなることがあります。

## 採用場面

- 動的にアルゴリズムを変更する必要がある場合

実行時に異なるアルゴリズムを選択する必要がある場合に有効です。

- 同じ問題を解くために複数の方法がある場合

異なる方法で同じ問題を解決する必要がある場合に適しています。

- アルゴリズムが頻繁に変更される場合

アルゴリズムが頻繁に変更される可能性がある場合、ストラテジーパターンを使用することで変更の影響を最小限に抑えることができます。

- コンテキストとアルゴリズムを分離したい場合

アルゴリズムをコンテキストから分離して管理したい場合に有効です。

## 実際の適用例

- ソートアルゴリズム

異なるソートアルゴリズム（例えば、クイックソート、マージソート、バブルソート）を選択できるようにする。

- 支払い方法

異なる支払い方法（クレジットカード、PayPal、銀行振込）を選択して利用できるようにする。

- 検索アルゴリズム

異なる検索アルゴリズム（例えば、線形検索、二分探索）を動的に切り替える。

- データ圧縮

異なる圧縮アルゴリズム（例えば、ZIP圧縮、GZIP圧縮）を選択してデータを圧縮/解凍する。

- グラフィック描画

異なる描画アルゴリズム（例えば、線の描画、塗りつぶしアルゴリズム）を選択して描画処理を行う。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
interface Strategy {
    int doOperation(int num1, int num2);
}

class OperationAdd implements Strategy {
    @Override
    public int doOperation(int num1, int num2) {
        return num1 + num2;
    }
}

class OperationSubtract implements Strategy {
    @Override
    public int doOperation(int num1, int num2) {
        return num1 - num2;
    }
}

class OperationMultiply implements Strategy {
    @Override
    public int doOperation(int num1, int num2) {
        return num1 * num2;
    }
}

class Context {
    private Strategy strategy;

    public Context(Strategy strategy) {
        this.strategy = strategy;
    }

    public void setStrategy(Strategy strategy) {
        this.strategy = strategy;
    }

    public int executeStrategy(int num1, int num2) {
        return strategy.doOperation(num1, num2);
    }
}
```

```java
public class StrategyPatternExample {
    public static void main(String[] args) {
        Context context = new Context(new OperationAdd());
        System.out.println("10 + 5 = " + context.executeStrategy(10, 5));

        context.setStrategy(new OperationSubtract());
        System.out.println("10 - 5 = " + context.executeStrategy(10, 5));

        context.setStrategy(new OperationMultiply());
        System.out.println("10 * 5 = " + context.executeStrategy(10, 5));
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface Strategy {
    doOperation(num1: number, num2: number): number;
}

class OperationAdd implements Strategy {
    doOperation(num1: number, num2: number): number {
        return num1 + num2;
    }
}

class OperationSubtract implements Strategy {
    doOperation(num1: number, num2: number): number {
        return num1 - num2;
    }
}

class OperationMultiply implements Strategy {
    doOperation(num1: number, num2: number): number {
        return num1 * num2;
    }
}

class Context {
    private strategy: Strategy;

    constructor(strategy: Strategy) {
        this.strategy = strategy;
    }

    setStrategy(strategy: Strategy): void {
        this.strategy = strategy;
    }

    executeStrategy(num1: number, num2: number): number {
        return this.strategy.doOperation(num1, num2);
    }
}
```

```typescript
const context = new Context(new OperationAdd());
console.log("10 + 5 = " + context.executeStrategy(10, 5));

context.setStrategy(new OperationSubtract());
console.log("10 - 5 = " + context.executeStrategy(10, 5));

context.setStrategy(new OperationMultiply());
console.log("10 * 5 = " + context.executeStrategy(10, 5));
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class Strategy(ABC):
    @abstractmethod
    def do_operation(self, num1, num2):
        pass

class OperationAdd(Strategy):
    def do_operation(self, num1, num2):
        return num1 + num2

class OperationSubtract(Strategy):
    def do_operation(self, num1, num2):
        return num1 - num2

class OperationMultiply(Strategy):
    def do_operation(self, num1, num2):
        return num1 * num2

class Context:
    def __init__(self, strategy: Strategy):
        self._strategy = strategy

    def set_strategy(self, strategy: Strategy):
        self._strategy = strategy

    def execute_strategy(self, num1, num2):
        return self._strategy.do_operation(num1, num2)
```

```python
if __name__ == "__main__":
    context = Context(OperationAdd())
    print("10 + 5 =", context.execute_strategy(10, 5))

    context.set_strategy(OperationSubtract())
    print("10 - 5 =", context.execute_strategy(10, 5))

    context.set_strategy(OperationMultiply())
    print("10 * 5 =", context.execute_strategy(10, 5))
```
:::

# Template Method（テンプレートメソッド）パターン

## メリット

- コードの再利用

共通の処理をスーパークラスに定義し、サブクラスで具体的な実装を行うことで、コードの再利用性が向上します。

- 共通のアルゴリズムを提供

アルゴリズムの骨組みをスーパークラスに定義し、具体的なステップをサブクラスに任せることで、一貫したアルゴリズムを提供できます。

- 拡張の容易さ

サブクラスで必要な部分だけをオーバーライドすることで、新しい動作を簡単に追加できます。

- DRY原則の遵守

共通の処理を一箇所にまとめることで、重複コードを排除し、DRY（Don't Repeat Yourself）原則を遵守できます。

- コードの可読性向上

アルゴリズムの構造が明確になり、コードの可読性が向上します。

## デメリット

- サブクラスの数が増加

新しい具体的な実装を追加するたびにサブクラスを作成する必要があるため、サブクラスの数が増加することがあります。

- 設計の複雑化

スーパークラスとサブクラスの設計が複雑になることがあります。

- フレキシビリティの制約

スーパークラスに定義されたアルゴリズムの骨組みが固定されているため、大幅な変更が必要な場合には柔軟性が制約されることがあります。

## 採用場面

- アルゴリズムの複数バリエーションが必要な場合

基本的なアルゴリズムは同じで、具体的なステップだけが異なる場合。

- 複数のクラスで共通の処理が必要な場合

共通の処理をスーパークラスにまとめ、各クラスで具体的な処理を実装する場合。

- アルゴリズムの変更が頻繁に行われる場合

アルゴリズムの一部を簡単に変更できるようにするために、テンプレートメソッドパターンを使用。

## 実際の適用例

- データ処理パイプライン

データの前処理、変換、後処理のステップをテンプレートメソッドで定義し、具体的な処理はサブクラスで実装。

- レポート生成

レポートの共通の生成手順（ヘッダー作成、本文作成、フッター作成）をテンプレートメソッドで定義し、各ステップの具体的な内容をサブクラスで実装。

- ゲーム開発

ゲームのメインループ（初期化、ゲームロジック更新、レンダリング）の共通部分をテンプレートメソッドで定義し、具体的なゲームロジックはサブクラスで実装。

- 認証フレームワーク

認証プロセス（ユーザ情報の取得、パスワードの検証、認証結果の処理）をテンプレートメソッドで定義し、各ステップの具体的な実装をサブクラスで行う。

- ファイルの読み取り

ファイルの共通の読み取り手順（ファイルを開く、データを読み取る、ファイルを閉じる）をテンプレートメソッドで定義し、データ読み取り部分の具体的な処理をサブクラスで実装。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
abstract class Game {
    abstract void initialize();
    abstract void startPlay();
    abstract void endPlay();

    // Template method
    public final void play() {
        // Initialize the game
        initialize();
        // Start game
        startPlay();
        // End game
        endPlay();
    }
}

class Cricket extends Game {
    @Override
    void initialize() {
        System.out.println("Cricket Game Initialized! Start playing.");
    }

    @Override
    void startPlay() {
        System.out.println("Cricket Game Started. Enjoy the game!");
    }

    @Override
    void endPlay() {
        System.out.println("Cricket Game Finished!");
    }
}

class Football extends Game {
    @Override
    void initialize() {
        System.out.println("Football Game Initialized! Start playing.");
    }

    @Override
    void startPlay() {
        System.out.println("Football Game Started. Enjoy the game!");
    }

    @Override
    void endPlay() {
        System.out.println("Football Game Finished!");
    }
}
```

```java
public class TemplateMethodPatternExample {
    public static void main(String[] args) {
        Game game = new Cricket();
        game.play();
        System.out.println();
        game = new Football();
        game.play();
    }
}
```
:::

:::details TypeScriptコード
```typescript
abstract class Game {
    // Template method
    public play(): void {
        this.initialize();
        this.startPlay();
        this.endPlay();
    }

    protected abstract initialize(): void;
    protected abstract startPlay(): void;
    protected abstract endPlay(): void;
}

class Cricket extends Game {
    protected initialize(): void {
        console.log("Cricket Game Initialized! Start playing.");
    }

    protected startPlay(): void {
        console.log("Cricket Game Started. Enjoy the game!");
    }

    protected endPlay(): void {
        console.log("Cricket Game Finished!");
    }
}

class Football extends Game {
    protected initialize(): void {
        console.log("Football Game Initialized! Start playing.");
    }

    protected startPlay(): void {
        console.log("Football Game Started. Enjoy the game!");
    }

    protected endPlay(): void {
        console.log("Football Game Finished!");
    }
}
```

```typescript
const cricket = new Cricket();
cricket.play();

console.log();

const football = new Football();
football.play();
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class Game(ABC):
    # Template method
    def play(self):
        self.initialize()
        self.start_play()
        self.end_play()

    @abstractmethod
    def initialize(self):
        pass

    @abstractmethod
    def start_play(self):
        pass

    @abstractmethod
    def end_play(self):
        pass

class Cricket(Game):
    def initialize(self):
        print("Cricket Game Initialized! Start playing.")

    def start_play(self):
        print("Cricket Game Started. Enjoy the game!")

    def end_play(self):
        print("Cricket Game Finished!")

class Football(Game):
    def initialize(self):
        print("Football Game Initialized! Start playing.")

    def start_play(self):
        print("Football Game Started. Enjoy the game!")

    def end_play(self):
        print("Football Game Finished!")
```

```python
if __name__ == "__main__":
    cricket = Cricket()
    cricket.play()
    
    print()
    
    football = Football()
    football.play()
```
:::

# Visitor（ビジター）パターン

## メリット

- 新しい操作の追加が容易

要素のクラスを変更することなく、新しい操作を追加できます。Visitorを新しく作成するだけで新しい機能を追加できます。

- 要素クラスの変更を避ける

既存の要素クラスを変更することなく、Visitorに操作を集中させることができ、要素クラスの安定性を保ちます。

- 単一責任原則の遵守

要素クラスはデータ構造の管理に専念し、操作はVisitorクラスに委ねることで、単一責任原則を守ります。

- 異なる操作のカプセル化

複数の操作をVisitorのサブクラスに分離できるため、各操作が独立して実装され、保守性が向上します。

## デメリット

- 初期設定の複雑さ

Visitorパターンを設定するための初期コストが高くなります。特に、要素の数が多い場合、Visitorのメソッドをすべての要素に追加する必要があります。

- 要素クラスの変更が必要

要素クラスがVisitorを受け入れるためのメソッドを持っている必要があるため、既存の要素クラスを変更する必要があります。

- 依存関係の増加

Visitorと要素クラス間の双方向の依存関係が発生するため、システム全体の依存関係が複雑になることがあります。

- 新しい要素の追加が困難

新しい要素を追加する際には、既存のすべてのVisitorクラスを変更する必要があるため、拡張が難しくなります。

## 採用場面

- 操作が頻繁に追加・変更される場合

要素のクラスを変更せずに、操作の追加や変更が頻繁に行われる場合に適しています。

- 要素クラスが変更されない場合

要素クラスが変更される可能性が低く、安定している場合に有効です。

- 複数の異なる操作が必要な場合

複数の異なる操作を同じデータ構造に対して行う必要がある場合に適しています。

## 実際の適用例

- 構文解析ツール

抽象構文木（AST）に対する異なる処理（例えば、式の評価、コードの生成、最適化）をVisitorパターンで実装。

- ドキュメント処理

ドキュメントの要素（段落、見出し、リストなど）に対して異なる処理（例えば、HTMLへの変換、PDF生成、テキスト抽出）をVisitorパターンで実装。

- グラフィック描画

グラフィックオブジェクト（円、四角形、線など）に対して異なる描画処理や変換処理をVisitorパターンで実装。

- ファイルシステム

ファイルシステムのディレクトリ構造に対して異なる処理（例えば、サイズの計算、ファイルの検索、ファイルリストの生成）をVisitorパターンで実装。

- ゲーム開発

ゲームオブジェクト（キャラクター、アイテム、障害物）に対して異なる処理（例えば、描画、更新、衝突判定）をVisitorパターンで実装。

## サンプルコード(Java、TypeScript、Python)

:::details Javaコード
```java
interface ComputerPartVisitor {
    void visit(Keyboard keyboard);
    void visit(Monitor monitor);
    void visit(Mouse mouse);
    void visit(Computer computer);
}

interface ComputerPart {
    void accept(ComputerPartVisitor computerPartVisitor);
}

class Keyboard implements ComputerPart {
    @Override
    public void accept(ComputerPartVisitor computerPartVisitor) {
        computerPartVisitor.visit(this);
    }
}

class Monitor implements ComputerPart {
    @Override
    public void accept(ComputerPartVisitor computerPartVisitor) {
        computerPartVisitor.visit(this);
    }
}

class Mouse implements ComputerPart {
    @Override
    public void accept(ComputerPartVisitor computerPartVisitor) {
        computerPartVisitor.visit(this);
    }
}

class Computer implements ComputerPart {
    ComputerPart[] parts;

    public Computer() {
        parts = new ComputerPart[] {new Mouse(), new Keyboard(), new Monitor()};
    }

    @Override
    public void accept(ComputerPartVisitor computerPartVisitor) {
        for (int i = 0; i < parts.length; i++) {
            parts[i].accept(computerPartVisitor);
        }
        computerPartVisitor.visit(this);
    }
}

class ComputerPartDisplayVisitor implements ComputerPartVisitor {
    @Override
    public void visit(Keyboard keyboard) {
        System.out.println("Displaying Keyboard.");
    }

    @Override
    public void visit(Monitor monitor) {
        System.out.println("Displaying Monitor.");
    }

    @Override
    public void visit(Mouse mouse) {
        System.out.println("Displaying Mouse.");
    }

    @Override
    public void visit(Computer computer) {
        System.out.println("Displaying Computer.");
    }
}
```

```java
public class VisitorPatternExample {
    public static void main(String[] args) {
        ComputerPart computer = new Computer();
        computer.accept(new ComputerPartDisplayVisitor());
    }
}
```
:::

:::details TypeScriptコード
```typescript
interface ComputerPartVisitor {
    visitKeyboard(keyboard: Keyboard): void;
    visitMonitor(monitor: Monitor): void;
    visitMouse(mouse: Mouse): void;
    visitComputer(computer: Computer): void;
}

interface ComputerPart {
    accept(computerPartVisitor: ComputerPartVisitor): void;
}

class Keyboard implements ComputerPart {
    accept(computerPartVisitor: ComputerPartVisitor): void {
        computerPartVisitor.visitKeyboard(this);
    }
}

class Monitor implements ComputerPart {
    accept(computerPartVisitor: ComputerPartVisitor): void {
        computerPartVisitor.visitMonitor(this);
    }
}

class Mouse implements ComputerPart {
    accept(computerPartVisitor: ComputerPartVisitor): void {
        computerPartVisitor.visitMouse(this);
    }
}

class Computer implements ComputerPart {
    private parts: ComputerPart[];

    constructor() {
        this.parts = [new Mouse(), new Keyboard(), new Monitor()];
    }

    accept(computerPartVisitor: ComputerPartVisitor): void {
        for (const part of this.parts) {
            part.accept(computerPartVisitor);
        }
        computerPartVisitor.visitComputer(this);
    }
}

class ComputerPartDisplayVisitor implements ComputerPartVisitor {
    visitKeyboard(keyboard: Keyboard): void {
        console.log("Displaying Keyboard.");
    }

    visitMonitor(monitor: Monitor): void {
        console.log("Displaying Monitor.");
    }

    visitMouse(mouse: Mouse): void {
        console.log("Displaying Mouse.");
    }

    visitComputer(computer: Computer): void {
        console.log("Displaying Computer.");
    }
}
```

```typescript
const computer = new Computer();
computer.accept(new ComputerPartDisplayVisitor());
```
:::

:::details Pythonコード
```python
from abc import ABC, abstractmethod

class ComputerPartVisitor(ABC):
    @abstractmethod
    def visit_keyboard(self, keyboard):
        pass

    @abstractmethod
    def visit_monitor(self, monitor):
        pass

    @abstractmethod
    def visit_mouse(self, mouse):
        pass

    @abstractmethod
    def visit_computer(self, computer):
        pass

class ComputerPart(ABC):
    @abstractmethod
    def accept(self, computer_part_visitor):
        pass

class Keyboard(ComputerPart):
    def accept(self, computer_part_visitor):
        computer_part_visitor.visit_keyboard(self)

class Monitor(ComputerPart):
    def accept(self, computer_part_visitor):
        computer_part_visitor.visit_monitor(self)

class Mouse(ComputerPart):
    def accept(self, computer_part_visitor):
        computer_part_visitor.visit_mouse(self)

class Computer(ComputerPart):
    def __init__(self):
        self.parts = [Mouse(), Keyboard(), Monitor()]

    def accept(self, computer_part_visitor):
        for part in self.parts:
            part.accept(computer_part_visitor)
        computer_part_visitor.visit_computer(self)

class ComputerPartDisplayVisitor(ComputerPartVisitor):
    def visit_keyboard(self, keyboard):
        print("Displaying Keyboard.")

    def visit_monitor(self, monitor):
        print("Displaying Monitor.")

    def visit_mouse(self, mouse):
        print("Displaying Mouse.")

    def visit_computer(self, computer):
        print("Displaying Computer.")
```

```python
if __name__ == "__main__":
    computer = Computer()
    computer.accept(ComputerPartDisplayVisitor())
```
:::

# おわりに

ChatGPT-4を利用してデザインパターンをまとめました。この記事がデザインパターンの選定を行う際に役立つ資料となれば幸いです。もちろん、誤りがある場合は適宜修正し、より精度の高い記事にしていきたいと考えています。

最後まで読んでいただき、ありがとうございました！
