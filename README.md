# Smart Mirror

天気、時刻、ニュース、タスク、買い物リストを鏡面ディスプレイ向けに表示するスマートミラー用の静的Webアプリです。

中央に余白を残し、顔が映る領域を邪魔しない外周配置のUIになっています。

## 主な機能

- 現在時刻、日付、挨拶の表示
- 日の出・日の入りと一日の進捗リング
- Open-Meteoを利用した現在の天気、時間別予報、週間予報
- 大気質、洗濯指数、月齢、おでかけ目安の表示
- 天気急変アラートの表示
- RSSニュースのローテーション表示
- Supabaseと連携したタスク、買い物リストの表示
- 夜間の自動減光
- 長時間表示向けの毎日自動リロード

## ファイル構成

```text
.
├── index.html              # スマートミラー本体
├── app.js                  # データ取得、描画、通知などのロジック
├── style.css               # レイアウトとデザイン
├── sounds/
│   └── notification_sound.mp3
└── todo/
    └── index.html          # 簡易タスク追加ページ
```

## 使い方

このアプリはビルド不要の静的ファイルです。

1. リポジトリを任意の場所に配置します。
2. `index.html` をブラウザで開きます。
3. スマートミラー端末ではブラウザを全画面表示またはキオスクモードで起動します。

ローカルサーバーで表示したい場合は、プロジェクト直下で以下のように起動できます。

```bash
python3 -m http.server 8000
```

その後、ブラウザで `http://localhost:8000` を開いてください。

## 設定

主な設定は [app.js](./app.js) の `CONFIG` にまとまっています。

### 表示地点

デフォルトでは水戸市の緯度経度を使います。

```js
location: { name: "水戸", lat: 36.3418, lon: 140.4468 },
useGeolocation: false,
```

現在地取得を使う場合は `useGeolocation` を `true` にしてください。取得できない場合は `location` の値にフォールバックします。

### 天気

天気データはAPIキー不要のOpen-Meteoを利用しています。

```js
weather: {
  refreshIntervalMs: 10 * 60 * 1000,
  suddenChange: {
    lookAheadHours: 6,
    precipProbJump: 40,
    windSpeedThreshold: 15,
    tempDropThreshold: 6,
    renotifyCooldownMs: 2 * 60 * 60 * 1000,
  },
},
```

急な雨、強風、気温低下などの警告条件もここで調整できます。

### ニュース

ニュースはRSSをrss2json経由で取得します。

```js
news: {
  rssUrl: "https://news.web.nhk/n-data/conf/na/rss/cat0.xml",
  maxItems: 8,
  rotateIntervalMs: 12000,
  refetchIntervalMs: 15 * 60 * 1000,
},
```

別のRSSを表示したい場合は `rssUrl` を変更してください。

### Supabase

タスクと買い物リストはSupabaseのテーブルを参照します。

```js
supabase: {
  url: "...",
  anonKey: "...",
  table: "todos2",
  shoppingTable: "todos",
},
```

自分のSupabaseプロジェクトで使う場合は、`url`、`anonKey`、テーブル名を変更してください。

## 表示サイズの調整

ミラー端末の表示倍率や解像度に合わせて、[style.css](./style.css) の以下のCSS変数を調整します。

```css
--zoom-percent: 150%;
--zoom-percent-vh: 150vh;
--zoom-scale: 0.6666667;
```

1920x1080の画面をブラウザ拡大率込みで使う場合など、実機で表示しながら調整してください。

## 補足

- 外部通信を行うため、天気、ニュース、Supabase連携にはインターネット接続が必要です。
- ブラウザの音声再生制限により、通知音は一度ユーザー操作が入るまで鳴らない場合があります。
- `todo/index.html` は簡易的なタスク追加用ページです。本体のミラー表示とは別ページとして使います。
