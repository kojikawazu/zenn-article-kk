---
title: "React + TypeScriptでテストコードの基礎を固める"
emoji: "⚡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "typescript", "jest", "vitest"]
published: true
---

# はじめに

テストコードを書く機会が増える中で、より多様なテスト手法やベストプラクティスを身につけたいと感じるようになりました。特に、複雑なロジックやエッジケースを効果的にカバーするためには、より多くのテスト手法の引き出しを持つことが重要と考えます。これまでの知識に不足していた部分を補い、より深い知見を得たいと思います。

そこで、今回はReact + TypeScriptを使ったプロジェクトに焦点を当て、単体テストと結合テストの基本を学び直し、その記録を記事として残すことにしました。JavaやPHPのテストコードやSeleniumのエンドツーエンドテストには今回は触れず、主にVitestを用いたテスト方法について詳しく解説していきます。

# テストの種類

## 単体テスト(Unit Test)

ソフトウェアの最小単位である個々の関数やメソッドが正しく動作することを確認するテストです。これにより、個々のモジュールが期待通りの出力を生成するかを確認します。単体テストは通常l、外部の依存関係をモックして、テスト太陽のモジュールのみに焦点を当てます。

### 特徴

- 迅速なフィードバック
- 問題の原因を特定しやすい
- 高いテストカバレッジを容易に達成
- 依存関係をモックしてテスト

## 結合テスト(Integration Test)

結合テストは、複数のモジュールやコンポーネントが連携して正しく動作することを確認するテストです。これにより、システム内の異なる部分が一緒に動く際に正しく相互作用するかを検証します。結合テストは、ユニットテストでは検出できないインターフェースや相互依存の問題を発見するのに役立ちます。

### 特徴

- 異なるモジュール間のインターフェースと相互作用をテスト
- 単体テストでは発見できない問題を検出
- 依存関係の一部を実際に使用する

## システムテスト(System Test)

システムテストは、アプリケーション全体が期待通りに動作することを確認するテストです。これにより、システム全体が結合され、エンドツーエンドの動作を確認します。システムテストはユーザーの視点に立って、システム全体の機能やパフォーマンスを検証します。

### 特徴

- アプリケーション全体の動作を検証
- 実際の使用環境に近い条件でテスト
- ユーザーの視点に立ったエンドツーエンドテスト
- 最も高いレベルのテストであり、システムの信頼性を確保

### どんなテストがある？

- 機能テスト
  - システム全体の機能が期待通りに動作するかを確認します。
- 非機能テスト
  - システムのパフォーマンス、セキュリティ、ユーザビリティなどを確認します。
- 回帰テスト
  - システムの変更によって既存の機能が壊れていないかを確認します。
- 受け入れテスト
  - システムがユーザーやクライアントの要求を満たしているかを確認します。
- エンドツーエンドテスト
  - ユーザーの視点からシステム全体を通じてのテストを行います。 

### 自動化テストツール

- Cypress
  - モダンなJavaScriptエコシステム向けに設計されたエンドツーエンドのテストフレームワーク
- Selenium
  - ウェブアプリケーションのテスト自動化のためのポピュラーなフレームワーク
- Playwright
  - Microsoftによって開発された最新のエンドツーエンドのテストフレームワーク

:::message
プロジェクト規模や機能単位によってはテストのイメージが異なります。単体テスト、結合テストを纏めて行う場合もあれば、規模に合わせて結合テスト、システムテストを行う場合もあります。そのため、プロジェクトのテスト方法に合わせて臨機応変に対応してください。
:::

# プロジェクトの構築

今回はViteというビルドツールを使用し、Reactプロジェクトを構築します。詳細は[公式サイト](https://ja.vitejs.dev/guide/)を参照してください。テストには[vitest](https://vitest.dev/guide/)を使用します。

今回は以下バージョンを使用します。

- React:18.2.0
- TypeScript:5.2.2
- vite:5.2.0
- vitest:1.6.0

```bash
# viteでプロジェクトを構築
npm create vite@latest [プロジェクト名]
```

```bash
# 他モジュールのインストール
npm i -D vite-tsconfig-paths
npm i -D vitest happy-dom @vitest/coverage-v8 @testing-library/react @testing-library/user-event @testing-library/jest-dom @types/jest
```

# プロジェクトの設定

以下ファイルの設定を行います。

:::details vitest-setup.ts
```typescript:vitest-setup.ts
import "@testing-library/jest-dom/vitest";
```
:::

:::details vite.config.ts
```typescript:vite.config.ts
/// <reference types="vitest" />
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react-swc';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: "happy-dom",
    setupFiles: ["./vitest-setup.ts"],
  },
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    }
  }
})
```
:::

:::details tsconfig.json
```json
{
  "compilerOptions": {
        /** 省略 */

        /** ここから追加 */
        /** path */
        "baseUrl": "./",
        "paths": {
        "@/*": ["src/*"],
        },
        "types": [
        "vitest/globals", 
        "@testing-library/jest-dom",
        "cypress"
        ]
    },
}
```
:::

# テストコード

テストコードを書きやすそうなお問い合わせページを実装しながら、テストコードを書いていきます。

## カスタムhooksに対する単体テスト①

フォーム画面のラベル部分をカスタムhooksにしました。それに対し、単体テストを行います。
初期設定とpropsに渡された値の設定の為、ここはテストしやすいです。

:::details useFormLabel.types.ts
```typescript:src/hooks/useFormLabel.types.ts
/**
 * フォームラベルのPropsの型定義
 */
export type UseFormLabelProps = {
    initialFormTitle?:           string;
    initialFormName?:            string;
    initialFormEmail?:           string;
    initialFormPassword?:        string;
    initialFormButton?:          string;
    initialPlaceholderName?:     string;
    initialPlaceholderEmail?:    string;
    initialPlaceholderPassword?: string;
};
```
:::

:::details useFormLabel.ts
```typescript:src/hooks/useFormLabel.ts
import { UseFormLabelProps } from "@/hooks/useFormLabel.types";

/**
 * カスタムhooks フォームラベル
 * @param param0 初期値を含むオブジェクト
 * @returns フォームラベルとプレースホルダーの初期値を含むオブジェクト
 */
export const useFormLabel = ({
    initialFormTitle           = "",
    initialFormName            = "",
    initialFormEmail           = "",
    initialFormPassword        = "",
    initialFormButton          = "",
    initialPlaceholderName     = "",
    initialPlaceholderEmail    = "",
    initialPlaceholderPassword = "",
}: UseFormLabelProps = {}) => {
    return {
        formTitle: initialFormTitle,
        formName: initialFormName,
        formEmail: initialFormEmail,
        formPassword: initialFormPassword,
        formButton: initialFormButton,
        formPlaceholderName: initialPlaceholderName,
        formPlaceholderEmail: initialPlaceholderEmail,
        formPlaceholderPassword: initialPlaceholderPassword,
    };
}
```
:::

:::details useFormLabel.test.ts
```typescript:src/hooks/useFormLabel.test.ts
import { renderHook } from '@testing-library/react';　// カスタムhooksをテストする場合に使用します。
import { useFormLabel } from '@/hooks/useFormLabel';

/**
 * useFormLabel のテストコード
 */
describe("useFormLabel test", () => {
    
    describe("useFormLabel test title", () => {
        /** タイトルに対して、初期設定、propsが渡された場合の設定をテストします。 */
        test("should render the default title", () => {
            const { result } = renderHook(useFormLabel);
            expect(result.current.formTitle).toBe("");
        });

        test("should render the initial title", () => {
            const { result } = renderHook(useFormLabel, {
                initialProps: { initialFormTitle: "テストタイトル" },
            });
            expect(result.current.formTitle).toBe("テストタイトル");
        });
    });

    describe("useFormLabel test label name", () => {
        /** お名前(ラベル)に対して、初期設定、propsが渡された場合の設定をテストします。 */
        test("should render the default label name", () => {
            const { result } = renderHook(useFormLabel);
            expect(result.current.formName).toBe("");
        });

        test("should render the initial label name", () => {
            const { result } = renderHook(useFormLabel, {
                initialProps: { initialFormName: "テストネーム" },
            });
            expect(result.current.formName).toBe("テストネーム");
        });
    });
        
    describe("useFormLabel test placeholder name", () => {
        /** お名前(Placeholder)に対して、初期設定、propsが渡された場合の設定をテストします。 */
        test("should render the default placeholder name", () => {
            const { result } = renderHook(useFormLabel);
            expect(result.current.formPlaceholderName).toBe("");
        });

        test("should render the initial placeholder name", () => {
            const { result } = renderHook(useFormLabel, {
                initialProps: { initialPlaceholderName: "デフォルト" },
            });
            expect(result.current.formPlaceholderName).toBe("デフォルト");
        });
    });

    describe("useFormLabel test label email", () => {
        /** Eメールアドレス(ラベル)に対して、初期設定、propsが渡された場合の設定をテストします。 */
        test("should render the default label email", () => {
            const { result } = renderHook(useFormLabel);
            expect(result.current.formEmail).toBe("");
        });

        test("should render the initial label name", () => {
            const { result } = renderHook(useFormLabel, {
                initialProps: { initialFormEmail: "テストEメール" },
            });
            expect(result.current.formEmail).toBe("テストEメール");
        });        
    });

    describe("useFormLabel test placeholder email", () => {
        /** Eメールアドレス(Placeholder)に対して、初期設定、propsが渡された場合の設定をテストします。 */
        test("should render the default placeholder email", () => {
            const { result } = renderHook(useFormLabel);
            expect(result.current.formPlaceholderEmail).toBe("");
        });

        test("should render the initial placeholder name", () => {
            const { result } = renderHook(useFormLabel, {
                initialProps: { initialPlaceholderEmail: "テストEメール" },
            });
            expect(result.current.formPlaceholderEmail).toBe("テストEメール");
        });
    });

    describe("useFormLabel test label password", () => {
        /** パスワード(ラベル)に対して、初期設定、propsが渡された場合の設定をテストします。 */
        test("should render the default label password", () => {
            const { result } = renderHook(useFormLabel);
            expect(result.current.formPassword).toBe("");
        });

        test("should render the initial label password", () => {
            const { result } = renderHook(useFormLabel, {
                initialProps: { initialFormPassword: "テストパスワード" },
            });
            expect(result.current.formPassword).toBe("テストパスワード");
        });
    });

    describe("useFormLabel test placeholder password", () => {
        /** パスワード(Placeholder)に対して、初期設定、propsが渡された場合の設定をテストします。 */
        test("should render the default placeholder password", () => {
            const { result } = renderHook(useFormLabel);
            expect(result.current.formPlaceholderPassword).toBe("");
        });

        test("should render the initial placeholder password", () => {
            const { result } = renderHook(useFormLabel, {
                initialProps: { initialPlaceholderPassword: "テストパスワード" },
            });
            expect(result.current.formPlaceholderPassword).toBe("テストパスワード");
        });
    });

    describe("useFormLabel test button", () => {
        /** ボタン名に対して、初期設定、propsが渡された場合の設定をテストします。 */
        test("should render the default button", () => {
            const { result } = renderHook(useFormLabel);
            expect(result.current.formButton).toBe("");
        });

        test("should render the initial button", () => {
            const { result } = renderHook(useFormLabel, {
                initialProps: { initialFormButton: "テスト" },
            });
            expect(result.current.formButton).toBe("テスト");
        });
    });
});
```
:::

## カスタムhooksに対する単体テスト②

フォームで扱うお名前、Eメールアドレス、パスワード、その他関数をカスタムhooksに纏めました。そのhooksに対してテストを行います。

:::details useForm.ts
```typescript:src/hooks/useForm.ts
import { useState } from "react";

/**
 * カスタムhooks フォーム
 * @returns カスタムhooks
 */
export const useForm = () => {
    const [name, setName]         = useState<string>("");
    const [email, setEmail]       = useState<string>("");
    const [password, setPassword] = useState<string>("");
    const [errors, setErrors] = useState<{ name?: string; email?: string; password?: string }>({});

    const resetForm = () => {
        setName("");
        setEmail("");
        setPassword("");
        setErrors({});
    }

    const validateForm = () => {
        const newErrors: { name?: string; email?: string; password?: string } = {};
        if (!name)     newErrors.name     s= "名前は必須です";
        if (!email)    newErrors.email    = "Eメールは必須です";
        if (!password) newErrors.password = "パスワードは必須です";

        setErrors(newErrors);
        return Object.keys(newErrors).length === 0;
    }

    const handleSubmit = () => {
        if (validateForm()) {
            console.log("Form submitted successfully.");
            resetForm();
        } else {
            console.log("Form validation failed");
        }
    }

    return {
        name,
        setName,
        email,
        setEmail,
        password,
        setPassword,
        errors,
        setErrors,
        resetForm,
        handleSubmit,
    };
}
```
:::

:::details useForm.test.ts
```typescript
import { renderHook, act } from '@testing-library/react';
import { useForm } from '@/hooks/useForm';

/**
 * useForm のテストコード
 */
describe("useForm test", () => {

    describe("useForm test name", () => {
        /** お名前に対し、初期設定、変更後の値をテストします。 */
        test("should render the default name", () => {
            const { result } = renderHook(useForm);
            expect(result.current.name).toBe("");
        });

        test("should render the input name", () => {
            const { result } = renderHook(useForm);
            act(() => result.current.setName("テストネーム"));
            expect(result.current.name).toBe("テストネーム");
        });
    });

    describe("useForm test email", () => {c
        /** Eメールアドレスに対し、初期設定、変更後の値をテストします。 */
        test("should render the default email", () => {
            const { result } = renderHook(useForm);
            expect(result.current.email).toBe("");
        });

        test("should render the input email", () => {
            const { result } = renderHook(useForm);
            act(() => result.current.setEmail("テストEメール"));
            expect(result.current.email).toBe("テストEメール");
        });
    });

    describe("useForm test password", () => {
        /** パスワードに対し、初期設定、変更後の値をテストします。 */
        test("should render the default password", () => {
            const { result } = renderHook(useForm);
            expect(result.current.password).toBe("");
        });

        test("should render the input password", () => {
            const { result } = renderHook(useForm);
            act(() => result.current.setPassword("テストパスワード"));
            expect(result.current.password).toBe("テストパスワード");
        });
    });

    describe("useForm execute resetForm test", () => {
        /** resetForm関数を実行した場合のテストします。 */
        test("execute resetForm", () => {
            const { result } = renderHook(useForm);

            act(() => {
                result.current.setName("テストネーム");
                result.current.setEmail("テストEメール");
                result.current.setPassword("テストパスワード");
                result.current.resetForm();
            });

            expect(result.current.name).toBe("");
            expect(result.current.email).toBe("");
            expect(result.current.password).toBe("");
            expect(result.current.errors).toEqual({});
        });
    });

    describe("useForm handleSubmit test", () => {
        /** handleSubmit関数を実行した場合のテストします。 */
        test("should reset form on successful submit", () => {
            const { result } = renderHook(useForm);

            act(() => {
                result.current.setName("テストネーム");
                result.current.setEmail("テストEメール");
                result.current.setPassword("テストパスワード");
            });

            expect(result.current.name).toBe("テストネーム");
            expect(result.current.email).toBe("テストEメール");
            expect(result.current.password).toBe("テストパスワード");

            act(() => result.current.handleSubmit());

            expect(result.current.name).toBe("");
            expect(result.current.email).toBe("");
            expect(result.current.password).toBe("");
            expect(result.current.errors).toEqual({});
        });
    });

    test("should set errors on failed submit", () => {
        /** 入力なしのまま、handleSubmit関数を実行した場合のテストします。 */
        const { result } = renderHook(useForm);

        act(() => {
            result.current.setName("");
            result.current.setEmail("");
            result.current.setPassword("");
        });

        act(() => result.current.handleSubmit());

        expect(result.current.errors).toEqual({
            name: "名前は必須です",
            email: "Eメールは必須です",
            password: "パスワードは必須です"
        });
    });

    test("should set specific errors on failed submit", () => {
        /** お名前だけ値を設定し、handleSubmit関数を実行した場合のテストします。 */
        const { result } = renderHook(useForm);

        act(() => {
            result.current.setName("テストネーム");
            result.current.setEmail("");
            result.current.setPassword("");
        });

        act(() => result.current.handleSubmit());

        expect(result.current.errors).toEqual({
            email: "Eメールは必須です",
            password: "パスワードは必須です"
        });
    });
});
```
:::

## コンポーネントに対する単体テスト・結合テスト

カスタムhooksを使ってお問い合わせフォーム用のコンポーネントを実装し、そのコンポーネントのテストを行います。カスタムHooksの関数に対して、一部Mockを使った単体テスト、Mockなしの結合テストを行います。

:::details Contact.tsx
```typescript:src/components/contact/Contact.tsx
import { useEffect } from 'react';
import { useForm } from '@/hooks/useForm';
import { useFormLabel } from '@/hooks/useFormLabel';

/**
 * お問い合わせフォームコンポーネント
 * @returns JSX
 */
const Contact = () => {
  const {
    name,
    setName,
    email,
    setEmail,
    password,
    setPassword,
    errors,
    setErrors,
    handleSubmit,
  } = useForm();

  const {
    formTitle,
    formName,
    formEmail,
    formPassword,
    formButton,
    formPlaceholderName,
    formPlaceholderEmail,
    formPlaceholderPassword,
  } = useFormLabel({
    initialFormTitle:    "お問い合わせページ",
    initialFormName:     "お名前",
    initialFormEmail:    "Eメールアドレス",
    initialFormPassword: "パスワード",
    initialFormButton:   "送信",
    initialPlaceholderName:     "フルネーム",
    initialPlaceholderEmail:    "xxxx@xxxx.com",
    initialPlaceholderPassword: "xxxxxxxx"
  });

  const onSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    handleSubmit();
  };

  useEffect(() => {
    if (name || email || password) {
      setErrors({});
    }
  }, [name, email, password, setErrors]);

  return (
    <>
      <div>
        <div>
          <h1>{formTitle}</h1>
        </div>
        
        <form onSubmit={onSubmit}>
          <div>
            <label htmlFor="name">{formName}</label>
            <input 
              type="text" 
              id="name"
              placeholder={formPlaceholderName}
              value={name}
              onChange={(e) => setName(e.target.value)} />
            {errors.name && <p>{errors.name}</p>}
          </div>

          <div>
            <label htmlFor="email">{formEmail}</label>
            <input 
              type="email"
              id="email"
              placeholder={formPlaceholderEmail}
              value={email}
              onChange={(e) => setEmail(e.target.value)} />
            {errors.email && <p>{errors.email}</p>}
          </div>

          <div>
            <label htmlFor="password">{formPassword}</label>
            <input 
              type="password"
              id="password"
              placeholder={formPlaceholderPassword}
              value={password}
              onChange={(e) => setPassword(e.target.value)} />
            {errors.password && <p>{errors.password}</p>}
          </div>

          <div>
            <button type="submit">{formButton}</button>
          </div>
        </form>
      </div>
    </>
  );
}

export default Contact;
```
:::

:::details Contact.test.tsx (Mockあり)
```typescript:src/components/contact/Contact.test.tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { vi, Mock } from "vitest";
import { useForm } from '@/hooks/useForm';
import Contact from "@/components/contact/Contact";

// useFormをモック化する
vi.mock('@/hooks/useForm');

/**
 * お問い合わせコンポーネントのテストコード(Mockあり)
 */
describe("<Contact /> mock test", () => { 

    describe("<Contact /> Mock send action", () => {
        let resetFormMock: Mock;
        let validateFormMock: Mock;
        let setErrorsMock: Mock;

        // 各テストの前にモックの設定を行う
        beforeEach(() => {
            // 新しいモック関数を作成する
            resetFormMock = vi.fn();
            validateFormMock = vi.fn().mockReturnValue(true);
            setErrorsMock = vi.fn();

             // useForm フックの戻り値をモック化
            (useForm as Mock).mockReturnValue({
                name: "",
                setName: vi.fn(),
                email: "",
                setEmail: vi.fn(),
                password: "",
                setPassword: vi.fn(),
                handleSubmit: resetFormMock, // handleSubmit をモック関数に置き換え
                validateForm: validateFormMock,
                errors: {},
                setErrors: setErrorsMock,
            });
        });

        // 各テストの後にモックをリセット
        afterEach(() => {
            vi.resetAllMocks();
        });

        /** 送信ボタン押下のテストコード */
        test("send button click test", async () => {
            // userEvent のセットアップ
            const user = userEvent.setup();
            // Contact コンポーネントをレンダリング
            render(<Contact />);
            
             // 各入力フィールドとボタンを取得
            const nameTextElement = screen.getByRole("textbox", {
                name: "お名前",
            });
            const emailTextElement = screen.getByRole("textbox", {
                name: "Eメールアドレス",
            });
            const passwordTextElement = screen.getByLabelText("パスワード");
            const sendButtonElement = screen.getByRole("button", {
                name: "送信",
            });

            // 各入力フィールドに値を入力
            await user.type(nameTextElement,     "テストネーム");
            await user.type(emailTextElement,    "テストEメール");
            await user.type(passwordTextElement, "テストパスワード");
            // 送信ボタンをクリック
            await user.click(sendButtonElement);
    
            // resetFormMock が 1 回呼び出されたことを確認
            expect(resetFormMock).toHaveBeenCalledTimes(1);
            expect(validateFormMock).toHaveBeenCalledTimes(1);
        });

        test("should show validation errors on empty submit", async () => {
            const user = userEvent.setup();
            // フォームバリデーションを失敗させるために false を返す
            validateFormMock.mockReturnValueOnce(false);
            (useForm as Mock).mockReturnValue({
                name: "",
                setName: vi.fn(),
                email: "",
                setEmail: vi.fn(),
                password: "",
                setPassword: vi.fn(),
                handleSubmit: resetFormMock,
                validateForm: validateFormMock,
                errors: {
                    name: "名前は必須です",
                    email: "Eメールは必須です",
                    password: "パスワードは必須です"
                },
                setErrors: setErrorsMock,
            });
            render(<Contact />);
    
            const sendButtonElement = screen.getByRole("button", {
                name: "送信",
            });
    
            await user.click(sendButtonElement);
    
            expect(screen.getByText("名前は必須です")).toBeInTheDocument();
            expect(screen.getByText("Eメールは必須です")).toBeInTheDocument();
            expect(screen.getByText("パスワードは必須です")).toBeInTheDocument();
            expect(resetFormMock).not.toHaveBeenCalled();
        });
    });
});
```
:::

:::details ContactIT.test.tsx (Mockなし)
```typescript:src/components/contact/ContactIT.test.tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import Contact from "@/components/contact/Contact";

/**
 * お問い合わせのテストコード
 */
describe("<Contact /> test", () => {
    describe("<Contact /> test H1", () => {
        /** お問い合わせページのタイトルのテスト */
        test("renders correctly", () => {
            render(<Contact />);

            const h1Element = screen.getByRole("heading", {
                name: "お問い合わせページ",
            });
            expect(h1Element).toBeInTheDocument();
        });
    });

    describe("<Contact /> test name", () => {
        /** お名前のラベルテスト */
        test("renders correctly form name label", () => {
            render(<Contact />);
    
            const nameLabelElement = screen.getByText("お名前");
            expect(nameLabelElement).toBeInTheDocument();
        });
    
        /** お名前のテキストボックステスト */
        test("renders correctly form name textbox", () => {
            render(<Contact />);
    
            const nameTextElement = screen.getByRole("textbox", {
                name: "お名前",
            });
            expect(nameTextElement).toBeInTheDocument();
        });
    
        /** お名前のplaceholderテスト */
        test("renders correctly form name textbox placeholder", () => {
            render(<Contact />);
    
            const nameTextPlaceElement = screen.getByPlaceholderText("フルネーム");
            expect(nameTextPlaceElement).toBeInTheDocument();
        });
    
        /** お名前の初期値テスト */
        test("renders correctly form name textbox value", () => {
            render(<Contact />);
    
            const nameTextElement = screen.getByRole("textbox", {
                name: "お名前",
            });
            expect(nameTextElement).toHaveValue("");
        });
    });
    
    describe("<Contact /> test email", () => {
        /** Eメールアドレスのラベルテスト */
        test("renders correctly form email label", () => {
            render(<Contact />);
    
            const emailLabelElement = screen.getByText("Eメールアドレス");
            expect(emailLabelElement).toBeInTheDocument();
        });
    
        /** Eメールアドレスのテキストボックステスト */
        test("renders correctly form email textbox", () => {
            render(<Contact />);
    
            const emailTextElement = screen.getByRole("textbox", {
                name: "Eメールアドレス",
            });
            expect(emailTextElement).toBeInTheDocument();
        });
    
        /** Eメールアドレスのplaceholderテスト */
        test("renders correctly form name email placeholder", () => {
            render(<Contact />);
    
            const emailTextPlaceElement = screen.getByPlaceholderText("xxxx@xxxx.com");
            expect(emailTextPlaceElement).toBeInTheDocument();
        });
    
        /** Eメールアドレスの初期値テスト */
        test("renders correctly form name email value", () => {
            render(<Contact />);
    
            const emailTextElement = screen.getByRole("textbox", {
                name: "Eメールアドレス",
            });
            expect(emailTextElement).toHaveValue("");
        });
    });

    describe("<Contact /> test password", () => {
        /** パスワードのラベルテスト */
        test("renders correctly form password label", () => {
            render(<Contact />);
    
            const passwordLabelElement = screen.getByText("パスワード");
            expect(passwordLabelElement).toBeInTheDocument();
        });
    
        /** パスワードのテキストボックステスト */
        test("renders correctly form password textbox", () => {
            render(<Contact />);
    
            const passwordTextElement = screen.getByLabelText("パスワード");
            expect(passwordTextElement).toBeInTheDocument();
        });
    
        /** パスワードのplaceholderテスト */
        test("renders correctly form name password placeholder", () => {
            render(<Contact />);
    
            const passwordTextPlaceElement = screen.getByPlaceholderText("xxxxxxxx");
            expect(passwordTextPlaceElement).toBeInTheDocument();
        });
    
         /** パスワードの初期値テスト */
        test("renders correctly form name password value", () => {
            render(<Contact />);
    
            const passwordTextElement = screen.getByLabelText("パスワード");
            expect(passwordTextElement).toHaveValue("");
        });
    });

    describe("<Contact /> send button", () => {
        /** 送信ボタンのラベルテスト */
        test("renders correctly form send button", () => {
            render(<Contact />);
    
            const sendButtonElement = screen.getByRole("button", {
                name: "送信",
            });
            expect(sendButtonElement).toBeInTheDocument();
        });

        /** 送信ボタンのSubmitテスト */
        test("handles submit action correctly", async () => {
            const user = userEvent.setup();
            render(<Contact />);

            const nameTextElement = screen.getByRole("textbox", {
                name: "お名前",
            });
            const emailTextElement = screen.getByRole("textbox", {
                name: "Eメールアドレス",
            });
            const passwordTextElement = screen.getByLabelText("パスワード");
            const sendButtonElement = screen.getByRole("button", {
                name: "送信",
            });

            await user.type(nameTextElement, "テストネーム");
            await user.type(emailTextElement, "テストEメール");
            await user.type(passwordTextElement, "テストパスワード");

            await user.click(sendButtonElement);

            expect(nameTextElement).toHaveValue("");
            expect(emailTextElement).toHaveValue("");
            expect(passwordTextElement).toHaveValue("");

            expect(screen.queryByText("名前は必須です")).not.toBeInTheDocument();
            expect(screen.queryByText("Eメールは必須です")).not.toBeInTheDocument();
            expect(screen.queryByText("パスワードは必須です")).not.toBeInTheDocument();
        });

        /** 送信ボタンのSubmitテスト(入力値が空の場合) */
        test("shows validation errors on empty submit", async () => {
            const user = userEvent.setup();
            render(<Contact />);
            
            const sendButtonElement = screen.getByRole("button", {
                name: "送信",
            });

            await user.click(sendButtonElement);

            expect(screen.getByText("名前は必須です")).toBeInTheDocument();
            expect(screen.getByText("Eメールは必須です")).toBeInTheDocument();
            expect(screen.getByText("パスワードは必須です")).toBeInTheDocument();
        });

        /** 送信ボタンのSubmitテスト(入力値が一部空の場合) */
        test("shows specific validation errors on partial submit", async () => {
            const user = userEvent.setup();
            render(<Contact />);

            const nameTextElement = screen.getByRole("textbox", {
                name: "お名前",
            });
            const sendButtonElement = screen.getByRole("button", {
                name: "送信",
            });

            await user.type(nameTextElement, "テストネーム");
            await user.click(sendButtonElement);

            expect(screen.queryByText("名前は必須です")).not.toBeInTheDocument();
            expect(screen.getByText("Eメールは必須です")).toBeInTheDocument();
            expect(screen.getByText("パスワードは必須です")).toBeInTheDocument();
        });
    });
});
```
:::

## お問い合わせページの結合テスト

コンポーネントまで実装し、テスト完了しました。次はお問い合わせページを行い、テストを行います。今回は結合テストを行います。

:::details ContactPage.tsx
```typescript:src/pages/ContactPage.tsx
import Contact from '@/components/contact/Contact';

/**
 * お問い合わせページコンポーネント
 * @returns JSX
 */
const ContactPage = () => {
  return (
    <div>
        <Contact />
    </div>
  );
}

export default ContactPage;
```
:::


:::details ContactPage.test.tsx
```typescript:src/pages/ContactPage.test.tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import ContactPage from "@/pages/ContactPage";

/**
 * ContactPage の結合テストコード
 */
describe("<ContactPage /> integration test", () => {

    /** 入力フォームに値を入力し、送信ボタンを押下した場合のテスト */
    test("handles form submission correctly", async () => {
        const user = userEvent.setup();
        render(<ContactPage />);

        const nameTextElement = screen.getByRole("textbox", {
            name: "お名前",
        });
        const emailTextElement = screen.getByRole("textbox", {
            name: "Eメールアドレス",
        });
        const passwordTextElement = screen.getByLabelText("パスワード");
        const sendButtonElement = screen.getByRole("button", {
            name: "送信",
        });

        await user.type(nameTextElement, "テストネーム");
        await user.type(emailTextElement, "a@example.com");
        await user.type(passwordTextElement, "abcde");
        await user.click(sendButtonElement);

        expect(nameTextElement).toHaveValue("");
        expect(emailTextElement).toHaveValue("");
        expect(passwordTextElement).toHaveValue("");
    });

    /** 入力値空のまま、送信ボタンを押下した場合のテスト */
    test("shows validation errors on empty submit", async () => {
        const user = userEvent.setup();
        render(<ContactPage />);
        
        const sendButtonElement = screen.getByRole("button", {
            name: "送信",
        });

        await user.click(sendButtonElement);

        expect(screen.getByText("名前は必須です")).toBeInTheDocument();
        expect(screen.getByText("Eメールは必須です")).toBeInTheDocument();
        expect(screen.getByText("パスワードは必須です")).toBeInTheDocument();
    });
});
```
:::

:::details ContactPageST.test.tsx
```typescript:src/pages/ContactPageST.test.tsx
import { ReactNode } from 'react';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { BrowserRouter } from 'react-router-dom';
import ContactPage from '@/pages/ContactPage';

/**
 * ContactPage のシステムテストコード
 */

/** BrowserRouterでラップしてコンポーネントをレンダリングする */
const renderWithRouter = (ui: ReactNode, { route = '/' } = {}) => {
    window.history.pushState({}, 'Test page', route);
    return render(ui, { wrapper: BrowserRouter });
};

describe('Contact Page', () => {

    /** 初期画面表示のテスト */
    test('renders Contact page correctly', () => {
        renderWithRouter(<ContactPage />);
        
        expect(screen.getByRole('heading', { name: /お問い合わせページ/i })).toBeInTheDocument();
        expect(screen.getByLabelText(/お名前/i)).toBeInTheDocument();
        expect(screen.getByLabelText(/Eメールアドレス/i)).toBeInTheDocument();
        expect(screen.getByLabelText(/パスワード/i)).toBeInTheDocument();
        expect(screen.getByRole('button', { name: /送信/i })).toBeInTheDocument();
    });

    /** フォームを入力し、送信ボタンを押下するテスト */
    test('handles form submission correctly', async () => {
        const user = userEvent.setup();
        renderWithRouter(<ContactPage />);

        await user.type(screen.getByLabelText(/お名前/i), 'テストネーム');
        await user.type(screen.getByLabelText(/Eメールアドレス/i), 'test@example.com');
        await user.type(screen.getByLabelText(/パスワード/i), 'password123');
        await user.click(screen.getByRole('button', { name: /送信/i }));

        // フォームが送信された後の動作を確認する
        expect(screen.getByLabelText(/お名前/i)).toHaveValue('');
        expect(screen.getByLabelText(/Eメールアドレス/i)).toHaveValue('');
        expect(screen.getByLabelText(/パスワード/i)).toHaveValue('');
    });

    /** 入力値空のまま、送信ボタンを押下するテスト */
    test('shows validation errors on empty submit', async () => {
        const user = userEvent.setup();
        renderWithRouter(<ContactPage />);
        
        await user.click(screen.getByRole('button', { name: /送信/i }));
        
        expect(screen.getByText(/名前は必須です/i)).toBeInTheDocument();
        expect(screen.getByText(/Eメールは必須です/i)).toBeInTheDocument();
        expect(screen.getByText(/パスワードは必須です/i)).toBeInTheDocument();
    });
});
```
:::

# 終わりに

今回は、vitestを使った単体テストやシステムテストについて学びました。これまで曖昧だった部分が明確になり、テストコードを書く際の自信がついたと感じています。TypeScriptだけでなく、Java、Python、PHPでもテストコードを書く機会がありますので、それぞれの言語に適したテスト手法も習得していきたいと考えています。

また、エンドツーエンドテストについても、さらに深堀りして理解を深めていく必要があると感じています。引き続き、テスト分野の知見を広げ、より品質の高いシステムを開発できるよう努めていきたいと思います。

最後までお読みいただき、ありがとうございました！

# URL

- vite

https://ja.vitejs.dev/guide/

- vitest

https://vitest.dev/
