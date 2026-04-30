# 視聴率ダッシュボード（在名7局）

在名7局（東海テレビ・中京テレビ・CBC・名古屋テレビ・テレビ愛知・NHK総合・NHKEテレ）の視聴率・占拠率を可視化・分析するWebアプリです。

## 機能

- **Dashboard** — 折れ線グラフ・セグメントタイムライン・番組表ビュー
- **Search** — キーワード検索 + AI意味検索（Anthropic Claude API使用）
- **Analysis** — 全体統括・ハイライト分析・トピック分析（AI評価付き）

## ローカル起動

```bash
npm install
npm run dev
```

ブラウザで http://localhost:5173 を開く。

## ビルド

```bash
npm run build
```

`dist/` フォルダに静的ファイルが生成される。

## AWS Amplifyへのデプロイ

1. このリポジトリをGitHub等にpush
2. AWS Amplify Consoleで「ホスティング」→「リポジトリ接続」を選び、リポジトリを連携
3. ビルド設定は `amplify.yml` が自動検出されるのでそのまま「保存」
4. 数分後にデプロイ完了。発行されるURLでアクセス可能

## AI機能の設定

`src/App.jsx` 内の `API_CONFIG` 設定:

```javascript
const API_CONFIG = {
  useMock: true,   // true: Anthropic API直叩き(デモ動作)
                   // false: 自社バックエンドAPI経由(本番)
  baseUrl: "",     // 本番例: "https://api.your-domain.com"
};
```

### useMock=true（デモ動作）の制限

- フロントエンドから `api.anthropic.com` を直接呼びます
- **CORS制限により通常のWebブラウザでは動作しません**
- Claude.ai内のArtifact環境のみで動作する特権機能
- 本番デプロイでは必ず `useMock: false` に変更し、バックエンド経由でAPIを呼ぶ必要があります

### 本番運用 (useMock=false)

以下のエンドポイントをバックエンドに実装する必要があります:

- `POST /api/search/semantic` — 意味検索（Bedrock Titan Embeddings + OpenSearch k-NN推奨）
- `POST /api/analysis/overview` — 全体統括分析
- `POST /api/analysis/highlight` — ハイライト分析
- `POST /api/analysis/topic` — トピック分析

各エンドポイントの入出力仕様は `src/App.jsx` の `apiClient` オブジェクトを参照。

## デモデータ

- **実データ**: 2026/04/01・2026/04/07 の朝帯（6:00-8:00）・夕方帯（16:40-19:00）
  名古屋地区・世帯視聴率・1分刻み
- **生成データ**: それ以外の日付は実データの平均プロファイルに日替わり乱数を加えて生成

`REAL_RATINGS` オブジェクトに `"YYYY-MM-DD|morning"` または `"YYYY-MM-DD|evening"` キーで実データを追加すると差し替え可能。

## ファイル構成

```
viewership-dashboard-app/
├── package.json
├── vite.config.js
├── amplify.yml          # Amplifyビルド設定
├── index.html
└── src/
    ├── main.jsx         # Reactエントリーポイント
    └── App.jsx          # アプリ本体（単一ファイル）
```
