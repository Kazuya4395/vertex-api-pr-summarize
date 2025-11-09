# Vertex AI PR Summarizer

このGitHub Actionは、GoogleのVertex AIを使用してプルリクエストのサマリとテスト要件を自動生成します。

## 特徴

- 📝 **自動PRサマリ**: 変更内容を200文字以内で簡潔に要約
- 🔄 **変更分析**: 影響度とレビュー重要度を含む詳細な変更点リスト
- ✅ **テスト要件**: チェックボックス形式でテストケースを提案
- 🎯 **柔軟な出力**: PRコメントまたはPR本文への投稿を選択可能
- 🤖 **複数モデル対応**: GeminiとClaudeの両モデルに対応

## 使い方

### 基本セットアップ

1. **ワークフローファイルの作成**: リポジトリにワークフローファイルを作成します（例: `.github/workflows/pr-summarize.yml`）:

```yaml
name: PR Summary Generator

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  summarize:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - name: Generate PR Summary
        uses: your-username/vertex-api-pr-summarize@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          gcp-project-id: ${{ secrets.GCP_PROJECT_ID }}
          gcp-credentials: ${{ secrets.GCP_CREDENTIALS }}
```

2. **シークレットの設定**: リポジトリの設定（`Settings` > `Secrets and variables` > `Actions`）で、以下のシークレットを設定します:
   - `GCP_PROJECT_ID`: Google CloudプロジェクトID
   - `GCP_CREDENTIALS`: GCPサービスアカウントキーのJSONコンテンツ

### 詳細設定

```yaml
- name: Generate PR Summary
  uses: your-username/vertex-api-pr-summarize@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    gcp-project-id: ${{ secrets.GCP_PROJECT_ID }}
    gcp-credentials: ${{ secrets.GCP_CREDENTIALS }}
    gcp-location: 'us-east5'  # オプション: デフォルトは 'us-east5'
    model: 'gemini-2.5-flash'  # オプション: デフォルトは 'gemini-2.5-flash'
    comment-location: 'summary'  # オプション: 'comment' または 'summary' (デフォルトは 'comment')
    system-prompt-path: '.github/prompts/custom-prompt.md'  # オプション
    diff-size-limit: '200000'  # オプション: デフォルトは '100000'
    timeout: '180000'  # オプション: デフォルトは '120000' (2分)
```

## アクションの入力

| 入力 | 説明 | デフォルト値 | 必須 |
|------|------|------------|------|
| `github-token` | GITHUB_TOKENシークレット | N/A | はい |
| `gcp-project-id` | Google CloudプロジェクトID | N/A | はい |
| `gcp-credentials` | GCPサービスアカウントキーのJSONコンテンツ | N/A | はい |
| `gcp-location` | プロジェクトのGoogle Cloudリージョン | `us-east5` | いいえ |
| `model` | 使用するVertex AIモデル（例: `gemini-2.5-flash`, `claude-sonnet-4-5@20250929`） | `gemini-2.5-flash` | いいえ |
| `comment-location` | サマリの投稿先: `comment`（PRコメント）または `summary`（PR本文） | `comment` | いいえ |
| `system-prompt-path` | カスタムシステムプロンプトファイルへのパス | 組み込みプロンプト | いいえ |
| `diff-size-limit` | 処理する差分の最大サイズ（バイト） | `100000` | いいえ |
| `timeout` | Vertex AI API呼び出しのタイムアウト（ミリ秒） | `120000` | いいえ |

## 出力フォーマット

生成されるサマリは以下の構造に従います:

```markdown
## 📝 概要
[変更内容を200文字以内で要約]

## 🔄 変更点

- **[変更内容]**
  - 影響度: 🔴大 / 🟡中 / 🟢小
  - レビュー重要度: 🔴大 / 🟡中 / 🟢小
  - 説明: [詳細説明]

## ✅ テスト要件

- [ ] [テストケース1]
- [ ] [テストケース2]
- [ ] [テストケース3]
```

### 影響度の判定基準
- 🔴 **大**: システム全体に影響、データベーススキーマ変更、API仕様変更など
- 🟡 **中**: 複数のモジュール・機能に影響、既存の振る舞いの変更など
- 🟢 **小**: 単一モジュール内の変更、リファクタリング、ドキュメント修正など

### レビュー重要度の判定基準
- 🔴 **大**: セキュリティ関連、データ整合性、パフォーマンスへの重大な影響
- 🟡 **中**: ロジックの変更、エラーハンドリング、重要な機能追加
- 🟢 **小**: コードスタイル、軽微なリファクタリング、ログ追加など

## カスタムプロンプト

カスタムプロンプトファイルを提供することで、AIの動作をカスタマイズできます。Markdownファイル（例: `.github/prompts/custom-prompt.md`）を作成し、`system-prompt-path`入力で参照してください。

参考として[デフォルトプロンプト](./prompts/summarize-prompt.md)をご覧ください。

## 利用可能なモデル

### Geminiモデル
- `gemini-2.5-flash`（推奨）
- `gemini-2.0-flash-exp`
- `gemini-1.5-pro`
- `gemini-1.5-flash`

### Claudeモデル
注意: Claudeモデルは`us-east5`などの特定のリージョンでのみ利用可能です。
- `claude-sonnet-4-5@20250929`
- `claude-3-5-sonnet@20240620`
- `claude-3-opus@20240229`

最新の利用可能なモデルについては、[Vertex AIドキュメント](https://cloud.google.com/vertex-ai/generative-ai/docs/learn/model-versions)を参照してください。

## 開発

1. リポジトリをクローン
2. 依存関係をインストール: `npm install`
3. テストを実行: `npm test`
4. プロジェクトをビルド: `npm run build`

## ライセンス

MIT

## コントリビューション

コントリビューションを歓迎します！お気軽にPull Requestをお送りください。
