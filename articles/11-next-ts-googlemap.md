---
title: "Next.js + TypeScript + Google Map JavaScript APIを試してみる"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["typescript", "nextjs", "googlemap"]
published: true
---

# 🎯目的

- 久しぶりにGoogle Mapsを触ってみたいと思いました。
- Next.jsとTypeScriptを使ってGoogle Mapsをどのように扱うか試してみたいと思います。

段階を踏んで記事を投稿する予定です。
今回はGoogle Mapの表示のみに焦点を当てます。

# Google Maps JavaScript API キーを取得

Google Mapsを使用するには、Google Maps JavaScript API キーが必要です。
以下の手順に従ってください。手順が最新のものと異なる場合は、Googleの指示に従ってください。

1. Google Cloud Console にアクセス:
    - Google Cloud Platform の Console にアクセスします。

2. プロジェクトの作成:
    - ダッシュボードの上部にあるプロジェクトドロップダウンから「プロジェクトを作成」を選択します。
    - プロジェクト名を入力し、「作成」をクリックします。

3. APIライブラリを有効にする:
    - 新しいプロジェクトのダッシュボードに移動し、「APIとサービス」>「ライブラリ」を選択します。
    - 「Maps JavaScript API」を検索し、選択して有効にします。

4. 認証情報を作成:
    - APIを有効にした後、画面上部の「認証情報」をクリックします。
    - 「認証情報を作成」ボタンをクリックし、「APIキー」を選択します。

5. APIキーの設定:
    - 新しいAPIキーが生成されます。このキーは後ほど.envファイルに保存します。

※ 料金に注意
   Google Maps JavaScript APIは無料枠がありますが、使用量に応じて料金が発生する可能性があります。料金体系を理解し、必要に応じて予算を設定してください。

# Next.js + TypeScriptのプロジェクト構築

以下のコマンドを実行して、Next.js + TypeScriptのプロジェクトを構築します。
今回は以下の設定で進めます。

```bash
# プロジェクトの構築
npx create-next-app next-ts-googlemap-sample
√ Would you like to use TypeScript? ... Yes
√ Would you like to use ESLint? ... Yes
√ Would you like to use Tailwind CSS? ... Yes
√ Would you like to use `src/` directory? ... No
√ Would you like to use App Router? (recommended) ... No
√ Would you like to customize the default import alias (@/*)? ... No
```

プロジェクト構築完了後、プロジェクト直下で以下のコマンドを実行します。
Google Maps JavaScript APIを使用する際に必要な型定義を提供するパッケージをインストールします。

```bash
# 必要なパッケージをインストールする
npm install @types/googlemaps
```

# package.jsonでバージョン調整

今回のプロジェクトで使用するバージョンは以下の通りです。
基本的にはプロジェクト生成時点のバージョンで試します。

```json
// package.json(一部抜粋)
{
  "dependencies": {
    "next": "^13",
    "react": "^18",
    "react-dom": "^18"
  },
  "devDependencies": {
    "@types/googlemaps": "^3.43.3",
    "@types/node": "^20",
    "@types/react": "^18",
    "@types/react-dom": "^18",
    "autoprefixer": "^10.0.1",
    "eslint": "^8",
    "eslint-config-next": "^14",
    "postcss": "^8",
    "tailwindcss": "^3.3.0",
    "typescript": "^5"
  }
}
```

Next.jsの最新バージョンは現時点で14ですが、この記事では13を使用します。
そのため、Next.jsのバージョンを13に手動で変更し、package.jsonを更新します。

```bash
# package.jsonを反映
npm install
```

# tsconfig.json

TypeScriptプロジェクトでGoogle Mapsの型定義を使用する場合は、tsconfig.jsonに以下の設定を追加します。

```json
// tsconfig.json(追加部分だけ抜粋)
{
  "compilerOptions": {
    "types": ["googlemaps"],
  },
}
```

# globals.css

今回はTailwind CSSも利用していますが、必要最低限のスタイルのみを設定します。

```typescript
// src/styles/globals.css

// Tailwind のベーススタイル
@tailwind base;
// カスタムコンポーネントのスタイル
@tailwind components;
// Tailwind のユーティリティクラス
@tailwind utilities;
```

# GoogleMap用のコンポーネント

Google Maps JavaScript APIを利用して、ウェブページに地図を表示するためのコンポーネントを実装します。

```typescript
// src/components/GoogleMap.tsx
import React, { useEffect, useRef, useState } from 'react';

// 初期化用の定数
const INITIALIZE_LAT  = 35.68238;  // 緯度
const INITIALIZE_LNG  = 139.76556; // 経度
const INITIALIZE_ZOOM = 15;        // ズームレベル

const INITIALIZE_MAP_WIDTH  = '100%';  // 地図の幅
const INITIALIZE_MAP_HEIGHT = '400px'; // 地図の高さ

const GoogleMap: React.FC = () => {
    const mapRef        = useRef<HTMLDivElement>(null);
    const [map, setMap] = useState<google.maps.Map | null>(null);

    useEffect(() => {
        if (!mapRef.current) return;

        const initializedMap  = new google.maps.Map(mapRef.current, {
          center: { lat: INITIALIZE_LAT, lng: INITIALIZE_LNG },
          zoom: INITIALIZE_ZOOM,
        });

        setMap(initializedMap);
    }, []);

    return (
        <div>
            <div ref={mapRef} style={{ width: INITIALIZE_MAP_WIDTH, height: INITIALIZE_MAP_HEIGHT }} />
        </div>
    )
}

export default GoogleMap;
```
# Documentの実装

Next.jsプロジェクトでカスタムドキュメントを使用すると、アプリケーションの<html>と<body>タグをカスタマイズできます。以下はその実装例です。

```typescript
// src/pages/_document.tsx
import Document, { Html, Head, Main, NextScript } from 'next/document';

class MyDocument extends Document {
    render() {
        const googleMapsApiKey = process.env.NEXT_PUBLIC_GOOGLE_MAP_API_KEY;
        
        return (
        <Html>
            <Head>
              {googleMapsApiKey && (
                  <script
                      async
                      src={`https://maps.googleapis.com/maps/api/js?key=${googleMapsApiKey}&libraries=places`}
                  />
              )}
            </Head>
            <body>
            <Main />
            <NextScript />
            </body>
        </Html>
        );
    }
}
  
export default MyDocument;
```

.envファイルを作成し、事前に取得したGoogle Maps JavaScript APIキーを設定します。

```bash
# .env
NEXT_PUBLIC_GOOGLE_MAP_API_KEY={Google Maps JavaScript APIキー}
```

# indexページの実装

最後に、アプリケーションのメインページであるindexページを実装します。

```typescript
// src/pages/index.tsx
import React from 'react';
import GoogleMap from '@/components/GoogleMap';

const Home: React.FC = () => {
  return (
    <section>
      <h1 className="mb-5">Google Maps with Next.js and TypeScript</h1>
      <GoogleMap />
    </section>
  );
};

export default Home;
```

# 🎉実行結果

地図の表示に成功しました！

例：「東京の中心部の地図表示結果」
![地図表示結果](https://storage.googleapis.com/zenn-user-upload/944e1fbbced1-20231109.png)

# ✨終わりに

Next.js、TypeScript、Google Maps JavaScript APIを使って地図の表示に成功しました。これで基本的な土台ができました。
次はどんな機能を追加できるか試してみたいと思います。

最後までお読みいただきありがとうございました！
