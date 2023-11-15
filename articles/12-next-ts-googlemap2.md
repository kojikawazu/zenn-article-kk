---
title: "Next.js + TypeScript + Google Map JavaScript APIから機能追加してみる"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["typescript", "nextjs", "googlemap"]
published: true
---

# 🎯目的

- 前回はGoogle Mapを表示してみました。今回は機能を追加してみましょう。
- プロトタイプ開発間隔で機能を段階的に追加してみましょう。
  今回追加する機能は以下となります。
  - 緯度経度の表示
  - 周辺店舗のリスト表示
  - 2点間のマーカー表示
  - 2点間の距離表示

# 前回の内容

前回の記事では、Next.jsとTypeScriptを使用してGoogle Mapを表示する基本的な方法を紹介しました。
今回は、その基礎の上に新しい機能を追加していきます。

https://zenn.dev/kou_kawa/articles/11-next-ts-googlemap

# 緯度、経度を取得してみよう

地図上をクリックしたら、緯度経度が取得できる機能を追加します。
以下コードを修正します。

```typescript
// src/components/GoogleMap.tsx

// (import分は変更なし)

const GoogleMap: React.FC = () => {
    const mapRef                  = useRef<HTMLDivElement>(null);
    const [map, setMap]           = useState<google.maps.Map | null>(null);
    const [location, setLocation] = useState<{ lat: number; lng: number } | null>(null); // 緯度経度stateの追加

    // (省略)

    // 以下処理を追加
    useEffect(() => {
        if (!map) return;

        // クリックリスナー追加
        map.addListener('click', (event) => {
            // 緯度経度の取得
            const latitude = event.latLng.lat();
            const longitude = event.latLng.lng();
            setLocation({ lat: latitude, lng: longitude });
        });

    }, [map]);

    // (省略)

    return (
        <div>
            {/** 地図表示 */}
            <div ref={mapRef} style={{ width: INITIALIZE_MAP_WIDTH, height: INITIALIZE_MAP_HEIGHT }} />

            {/** 緯度経度表示(追加) */}
            {location && (
                <div className="mx-5 my-5">
                <h2 className="underline text-lg mb-3">Location</h2>
                <p>Latitude: {location.lat}</p>
                <p>Longitude: {location.lng}</p>
                </div>
            )}
        </div>
    );
};

export default GoogleMap;
```

ここでは、地図上でのクリックイベントを利用して、クリックされた場所の緯度と経度を取得し、画面に表示しています。

スタイルの調整も合わせて行います。
(classNameの修正)

```typescript
// src/pages/index.tsx
const Home: React.FC = () => {
  return (
    <section>
      <h1 className="underline text-xl mt-3 mb-5 ml-3">Google Maps with Next.js and TypeScript</h1>
      <GoogleMap />
    </section>
  );
};
```

無事に緯度、経度表示に成功！
![緯度経度表示](https://storage.googleapis.com/zenn-user-upload/c9cbb6eb1fe2-20231115.png)

# 周辺店舗を取得してみよう

```typescript
// src/components/GoogleMap.tsx

// (import分は変更なし)

const GoogleMap: React.FC = () => {
    const mapRef                  = useRef<HTMLDivElement>(null);
    const [map, setMap]           = useState<google.maps.Map | null>(null);
    const [location, setLocation] = useState<{ lat: number; lng: number } | null>(null);
    const [shops, setShops]     = useState<google.maps.places.PlaceResult[]>([]);　// 周辺店舗stateの追加

    // (省略)

    useEffect(() => {
        if (!map) return;

        map.addListener('click', (event) => {

            // 緯度経度の取得
            const latitude = event.latLng.lat();
            const longitude = event.latLng.lng();
            setLocation({ lat: latitude, lng: longitude });

            // 店舗データの取得(追加)
            const service = new google.maps.places.PlacesService(map);
            service.nearbySearch({
                location: { lat: latitude, lng: longitude },
                radius: 1000,  // 検索範囲（メートル）
                type: 'store'  // 店舗を検索
            }, (results, status) => {
                if (status === window.google.maps.places.PlacesServiceStatus.OK) {
                    setShops(results);
                }
            });

        });

    }, [map]);

    // (省略)

    return (
        <div>
            {/** 地図表示 */}
            <div ref={mapRef} style={{ width: INITIALIZE_MAP_WIDTH, height: INITIALIZE_MAP_HEIGHT }} />

            {/** 緯度経度表示 */}
            {location && (
                <div className="mx-5 my-5">
                <h2 className="underline text-lg mb-3">Location</h2>
                <p>Latitude: {location.lat}</p>
                <p>Longitude: {location.lng}</p>
                </div>
            )}

            {/** 店舗リストの表示(追加) */}
            {shops.length > 0 && (
                <div className="mx-5 mb-5">
                    <h2 className="underline text-lg mb-3">Nearby Stores</h2>
                    <ul className="list-disc list-inside">
                        {shops.map((shop, index) => (
                            <li key={index}>{shop.name}</li>
                        ))}
                    </ul>
                </div>
            )}
        </div>
    );
};

export default GoogleMap;
```

周辺店舗の名称の取得に成功！

![周辺店舗リストの取得](https://storage.googleapis.com/zenn-user-upload/691d79519d32-20231115.png)

# ２点間のマーカーで結んでみよう

```typescript
// src/components/GoogleMap.tsx
import { mapPrefectures } from '@/datas/mapPrefectures';　// 都道府県のデータを含むモジュール(追加)

const GoogleMap: React.FC = () => {
    const mapRef                                      = useRef<HTMLDivElement>(null);
    const [map, setMap]                               = useState<google.maps.Map | null>(null);
    const [location, setLocation]                     = useState<{ lat: number; lng: number } | null>(null);
    const [shops, setShops]                           = useState<google.maps.places.PlaceResult[]>([]);
    const [startLocation, setStartLocation]           = useState<string | null>(null); // スタート地点state(追加)
    const [endLocation, setEndLocation]               = useState<string | null>(null); // ゴール地点state追加
    const [directionsRenderer, setDirectionsRenderer] = useState<google.maps.DirectionsRenderer | null>(null); // 道順をマップ上に描画するためのオブジェクト(追加)
 
    // Google Maps APIを使用してルートの方向をレンダリングするための関数(追加)
    const renderDirections = (result: google.maps.DirectionsResult) => {
        if (directionsRenderer) {
            directionsRenderer.setMap(null);
        }

        // Google Maps APIにおいて、道順をマップ上に描画するためのオブジェクトを取得する
        const renderer = new google.maps.DirectionsRenderer();
        // 現在のマップオブジェクトを追加する
        renderer.setMap(map);
        // ルートの結果を設定
        renderer.setDirections(result);
        setDirectionsRenderer(renderer);
    };

    useEffect(() => {
        if (!map) return;

        // スタート地点、ゴール地点の地点間のルートを計算し、その結果を処理する(追加)
        if (startLocation && endLocation) {
            // 指定された開始地点と終了地点間のルートを計算する為のオブジェクトを取得する
            const directionsService = new google.maps.DirectionsService();
    
            // ルート計算のリクエスト
            directionsService.route({
                origin: mapPrefectures[startLocation],      // スタート地点
                destination: mapPrefectures[endLocation],   // ゴール地点
                travelMode: google.maps.TravelMode.DRIVING, // 移動手段(車)
            }, (result, status) => {
                if (status === google.maps.DirectionsStatus.OK) {
                    renderDirections(result);
                } else {
                    console.error('Directions request failed due to ' + status);
                }
            });
        }

        map.addListener('click', (event) => {
            // (省略)
        });

    }, [map, startLocation, endLocation]);

    // (省略)

    return (
        <div>
            <div className="flex">
                {/** スタート地点(追加) */}
                <div className="ml-3 bg-green-200">
                    <label>Start Location:</label>
                    <select value={startLocation || ''} onChange={(e) => setStartLocation(e.target.value)}>
                        <option value="">--Select--</option>
                        {/** ここに都道府県リストを表示する */}
                        {Object.keys(mapPrefectures).map(key => (
                            <option key={key} value={key}>{key}</option>
                        ))}
                    </select>
                </div>

                {/** ゴール地点(追加) */}
                <div className="ml-3 bg-green-200">
                    <label>End Location:</label>
                    <select value={endLocation || ''} onChange={(e) => setEndLocation(e.target.value)}>
                        <option value="">--Select--</option>
                        {/** ここに都道府県リストを表示する */}
                        {Object.keys(mapPrefectures).map(key => (
                            <option key={key} value={key}>{key}</option>
                        ))}
                    </select>
                </div>
            </div>

            {/** 地図表示 */}
            <div ref={mapRef} style={{ width: INITIALIZE_MAP_WIDTH, height: INITIALIZE_MAP_HEIGHT }} />

            {/** 緯度経度表示 */}
            {location && (
                <div className="mx-5 my-5">
                <h2 className="underline text-lg mb-3">Location</h2>
                <p>Latitude: {location.lat}</p>
                <p>Longitude: {location.lng}</p>
                </div>
            )}

            {/** 店舗リストの表示 */}
            {shops.length > 0 && (
                <div className="mx-5 mb-5">
                    <h2 className="underline text-lg mb-3">Nearby Stores</h2>
                    <ul className="list-disc list-inside">
                        {shops.map((shop, index) => (
                            <li key={index}>{shop.name}</li>
                        ))}
                    </ul>
                </div>
            )}
        </div>
    );
};

export default GoogleMap;
```

まずはリロードしてセレクトボックスが表示されてるか確認します。

無事表示成功！
![初期表示](https://storage.googleapis.com/zenn-user-upload/40e069a0997b-20231115.png)

次にスタート地点、ゴール地点を選択します。
今回は「北海道」、「東京」の順で選択します。

無事２点間のマーカー表示成功！
![2点間表示](https://storage.googleapis.com/zenn-user-upload/a44bd96c129d-20231115.png)

# ２点間の距離を表示してみよう

```typescript
// src/components/GoogleMap.tsx

// (import分は変更なし)

const GoogleMap: React.FC = () => {
    const mapRef                                      = useRef<HTMLDivElement>(null);
    const [map, setMap]                               = useState<google.maps.Map | null>(null);
    const [location, setLocation]                     = useState<{ lat: number; lng: number } | null>(null);
    const [shops, setShops]                           = useState<google.maps.places.PlaceResult[]>([]);
    const [startLocation, setStartLocation]           = useState<string | null>(null);
    const [endLocation, setEndLocation]               = useState<string | null>(null);
    const [directionsRenderer, setDirectionsRenderer] = useState<google.maps.DirectionsRenderer | null>(null);
    const [distance, setDistance]                     = useState<string | null>(null);  // 距離state(追加)
   
    // (省略)

    useEffect(() => {
        if (!map) return;

        if (startLocation && endLocation) {
            const directionsService = new google.maps.DirectionsService();
    
            directionsService.route({
                origin: mapPrefectures[startLocation],
                destination: mapPrefectures[endLocation],
                travelMode: google.maps.TravelMode.DRIVING,
            }, (result, status) => {
                if (status === google.maps.DirectionsStatus.OK) {
                    renderDirections(result);

                    // 2点間の距離を取得(追加)
                    const routeDistance = result.routes[0].legs[0].distance.text;
                    setDistance(routeDistance);
                } else {
                    console.error('Directions request failed due to ' + status);
                }
            });
        }

        map.addListener('click', (event) => {
            // (省略)
        });

    }, [map, startLocation, endLocation]);

    // (省略)

    return (
        <div>
            <div className="flex">
                {/** スタート地点 */}
                <div className="ml-3 bg-green-200">
                    <label>Start Location:</label>
                    <select value={startLocation || ''} onChange={(e) => setStartLocation(e.target.value)}>
                        <option value="">--Select--</option>
                        {/** ここに都道府県リストを表示する */}
                        {Object.keys(mapPrefectures).map(key => (
                            <option key={key} value={key}>{key}</option>
                        ))}
                    </select>
                </div>

                {/** ゴール地点 */}
                <div className="ml-3 bg-green-200">
                    <label>End Location:</label>
                    <select value={endLocation || ''} onChange={(e) => setEndLocation(e.target.value)}>
                        <option value="">--Select--</option>
                        {/** ここに都道府県リストを表示する */}
                        {Object.keys(mapPrefectures).map(key => (
                            <option key={key} value={key}>{key}</option>
                        ))}
                    </select>
                </div>

                {/** 2点間の距離(追加) */}
                {distance && (
                    <div className="flex ml-3">
                        <h2 className="mr-3 bg-green-200">Route Distance:</h2>
                        <p>{distance}</p>
                    </div>
                )}
            </div>

            {/** 地図表示 */}
            <div ref={mapRef} style={{ width: INITIALIZE_MAP_WIDTH, height: INITIALIZE_MAP_HEIGHT }} />

            {/** 省略 */}
        </div>
    );
};

export default GoogleMap;
```

2点間の距離を表示。

無事に表示成功！
![2点間の距離算出](https://storage.googleapis.com/zenn-user-upload/918ab98e5cc4-20231115.png)

# 終わりに

 この記事では、Next.jsとTypeScriptを使用してGoogle Mapに様々な機能を追加しました。
 緯度経度の取得、周辺店舗のリスト表示、2点間のマーカー表示、距離表示など、段階的に機能を追加することで、地図アプリケーションの機能を豊かにする方法を探求しました。

最後までお読みいただきありがとうございました！