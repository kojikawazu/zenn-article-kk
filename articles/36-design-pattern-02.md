---
title: "デザインパターン備忘録(後半)"
emoji: "🖌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["java", "typescript", "python"]
published: false
---

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
