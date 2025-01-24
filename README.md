# Google Cloud Natural Language API Sample

Google Cloud Natural Language APIを利用したテキスト感情分析アプリケーション。入力されたテキストを文節ごとに分析し、感情の度合いを視覚的に表示します。

## 機能

- テキスト全体の感情分析
- 文節ごとの感情分析（色分け表示）
- 各文節のホバーでツールチップ表示
  - 感情スコア (-1.0 ~ 1.0)
  - 感情の大きさ
  - 判定（ポジティブ/ネガティブ/ニュートラル）

## セットアップ

1. プロジェクトのクローン
```bash
git clone [repository-url]
```

2. 依存関係のインストール
```bash
npm install
```

3. 環境変数の設定
```bash
cp .env.example .env
```
`.env`ファイルにGoogle Cloud Platform APIキーを設定:
```
VITE_GOOGLE_API_KEY=your_api_key_here
```

4. 開発サーバーの起動
```bash
npm run dev
```

## APIキーの取得方法

1. Google Cloud Platformでプロジェクトを作成
2. Cloud Natural Language APIを有効化
3. 認証情報からAPIキーを生成

## 注意事項

- APIキーは`.env`ファイルで管理し、公開リポジトリにはコミットしないでください
- ビルド済みファイルは公開しないでください
