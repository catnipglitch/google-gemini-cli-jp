# Gemini CLI

[![Gemini CLI CI](https://github.com/google-gemini/gemini-cli/actions/workflows/ci.yml/badge.svg)](https://github.com/google-gemini/gemini-cli/actions/workflows/ci.yml)
[![Gemini CLI E2E](https://github.com/google-gemini/gemini-cli/actions/workflows/e2e.yml/badge.svg)](https://github.com/google-gemini/gemini-cli/actions/workflows/e2e.yml)
[![Version](https://img.shields.io/npm/v/@google/gemini-cli)](https://www.npmjs.com/package/@google/gemini-cli)
[![License](https://img.shields.io/github/license/google-gemini/gemini-cli)](https://github.com/google-gemini/gemini-cli/blob/main/LICENSE)
[![View Code Wiki](https://www.gstatic.com/_/boq-sdlc-agents-ui/_/r/YUi5dj2UWvE.svg)](https://codewiki.google/github.com/google-gemini/gemini-cli)

![Gemini CLI Screenshot](./docs_j/assets/gemini-screenshot.png)

Gemini CLIは、Geminiのパワーを直接ターミナルにもたらすオープンソースのAIエージェントです。Geminiへの軽量なアクセスを提供し、プロンプトからモデルへの最も直接的なパスを提供します。

Gemini CLIの全容については、[ドキュメント](https://geminicli.com/docs/)をご覧ください。

## 🚀 Gemini CLIを選ぶ理由

- **🎯 無料枠**: 個人のGoogleアカウントで、60リクエスト/分、1,000リクエスト/日。
- **🧠 強力な Gemini 2.5 Pro**: 1Mトークンのコンテキストウィンドウにアクセス可能。
- **🔧 組み込みツール**: Google検索グラウンディング、ファイル操作、シェルコマンド、ウェブフェッチ。
- **🔌 拡張可能**: カスタム統合のためのMCP (Model Context Protocol) サポート。
- **💻 ターミナルファースト**: コマンドラインで作業する開発者向けに設計。
- **🛡️ オープンソース**: Apache 2.0ライセンス。

## 📦 インストール

### インストール前の前提条件

- Node.js バージョン 20 以降
- macOS、Linux、または Windows

### クイックインストール

#### npxで即時実行

```bash
# npxを使用 (インストール不要)
npx https://github.com/google-gemini/gemini-cli
```

#### npmでグローバルインストール

```bash
npm install -g @google/gemini-cli
```

#### Homebrew (macOS/Linux) でグローバルインストール

```bash
brew install gemini-cli
```

## リリースサイクルとタグ

詳細については、[リリース](docs_j/releases.md)をご覧ください。

### プレビュー

新しいプレビューリリースは、毎週火曜日UTC 2359に公開されます。これらのリリースは完全に検証されておらず、回帰やその他の未解決の問題が含まれている可能性があります。テストにご協力ください。`preview` タグでインストールします。

```bash
npm install -g @google/gemini-cli@preview
```

### 安定版

- 新しい安定版リリースは、毎週火曜日UTC 2000に公開されます。これは、先週の `preview` リリースに加えて、バグ修正と検証が完了したものです。`latest` タグを使用します。

```bash
npm install -g @google/gemini-cli@latest
```

### ナイトリー

- 新しいリリースは毎日UTC 0000に公開されます。これは、リリース時点のmainブランチからの全ての変更を含みます。保留中の検証や問題がある可能性があることを前提としてください。`nightly` タグを使用します。

```bash
npm install -g @google/gemini-cli@nightly
```

## 📋 主要機能

### コード理解と生成

- 大規模なコードベースのクエリと編集
- マルチモーダル機能を使用して、PDF、画像、またはスケッチから新しいアプリを生成
- 自然言語による問題のデバッグとトラブルシューティング

### 自動化と統合

- プルリクエストのクエリや複雑なリベース処理などの運用タスクを自動化
- MCPサーバーを使用して、[Imagen, Veo, Lyriaによるメディア生成](https://github.com/GoogleCloudPlatform/vertex-ai-creative-studio/tree/main/experiments/mcp-genmedia)を含む新しい機能を接続
- ワークフロー自動化のためのスクリプトで非対話的に実行

### 高度な機能

- リアルタイム情報のために、組み込みの[Google検索](https://ai.google.dev/gemini-api/docs/grounding)でクエリをグラウンディング
- 会話チェックポイント機能により、複雑なセッションを保存および再開
- カスタムコンテキストファイル (GEMINI.md) を使用して、プロジェクトの動作をカスタマイズ

### GitHub統合

Gemini CLIを[**Gemini CLI GitHub Action**](https://github.com/google-github-actions/run-gemini-cli)でGitHubワークフローに直接統合します。

- **プルリクエストレビュー**: 文脈に応じたフィードバックと提案を伴う自動コードレビュー
- **課題トリアージ**: コンテンツ分析に基づいたGitHub課題の自動ラベリングと優先順位付け
- **オンデマンド支援**: デバッグ、説明、タスク委任のヘルプが必要な場合は、課題やプルリクエストで `@gemini-cli` をメンション
- **カスタムワークフロー**: チームのニーズに合わせた自動化されたスケジュール、オンデマンドワークフローを構築

## 🔐 認証オプション

ニーズに最適な認証方法を選択してください。

### オプション1: Googleでログイン (Googleアカウントを使用したOAuthログイン)

**✨ 最適なユーザー**: 個人開発者、およびGemini Code Assistライセンスを持つすべての人。（詳細は[割り当て制限と利用規約](https://cloud.google.com/gemini/docs/quotas)を参照）

**メリット**:

- **無料枠**: 60リクエスト/分、1,000リクエスト/日
- 1Mトークンのコンテキストウィンドウを持つ **Gemini 2.5 Pro**
- **APIキー管理不要** - Googleアカウントでサインインするだけ
- 最新モデルへの**自動更新**

#### Gemini CLIを起動し、「Login with Google」を選択し、プロンプトが表示されたらブラウザ認証フローに従ってください。

```bash
gemini
```

#### 組織から有料のCode Assistライセンスを使用している場合は、Google Cloudプロジェクトを設定することを忘れないでください。

```bash
# Google Cloudプロジェクトを設定
export GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"
gemini
```

### オプション2: Gemini APIキー

**✨ 最適なユーザー**: 特定のモデル制御または有料ティアアクセスが必要な開発者

**メリット**:

- **無料枠**: Gemini 2.5 Proで100リクエスト/日
- **モデル選択**: 特定のGeminiモデルを選択
- **使用量ベースの課金**: 必要に応じて上位の制限にアップグレード可能

```bash
# https://aistudio.google.com/apikey からキーを取得
export GEMINI_API_KEY="YOUR_API_KEY"
gemini
```

### オプション3: Vertex AI

**✨ 最適なユーザー**: エンタープライズチームおよび本番ワークロード

**メリット**:

- **エンタープライズ機能**: 高度なセキュリティとコンプライアンス
- **スケーラブル**: 請求アカウントで高いレート制限
- **統合**: 既存のGoogle Cloudインフラストラクチャと連携

```bash
# Google Cloud Console からキーを取得
export GOOGLE_API_KEY="YOUR_API_KEY"
export GOOGLE_GENAI_USE_VERTEXAI=true
gemini
```

Google Workspaceアカウントおよびその他の認証方法については、[認証ガイド](./docs_j/get-started/authentication.md)をご覧ください。

## 🚀 はじめに

### 基本的な使い方

#### 現在のディレクトリで開始

```bash
gemini
```

#### 複数のディレクトリを含める

```bash
gemini --include-directories ../lib,../docs
```

#### 特定のモデルを使用

```bash
gemini -m gemini-2.5-flash
```

#### スクリプト用の非対話モード

シンプルなテキスト応答を取得します。

```bash
gemini -p "Explain the architecture of this codebase"
```

JSONのパースやエラー処理を含むより高度なスクリプトについては、`--output-format json` フラグを使用して構造化された出力を取得します。

```bash
gemini -p "Explain the architecture of this codebase" --output-format json
```

リアルタイムイベントストリーミング（長時間実行される操作の監視に役立ちます）には、`--output-format stream-json` を使用して、改行区切りのJSONイベントを取得します。

```bash
gemini -p "Run tests and deploy" --output-format stream-json
```

### クイック例

#### 新しいプロジェクトを開始

```bash
cd new-project/
gemini
> Write me a Discord bot that answers questions using a FAQ.md file I will provide
```

#### 既存のコードを分析

```bash
git clone https://github.com/google-gemini/gemini-cli
cd gemini-cli
gemini
> Give me a summary of all of the changes that went in yesterday
```

## 📚 ドキュメント

### はじめに

- [**クイックスタートガイド**](./docs_j/get-started/index.md) - すぐに起動して実行できます。
- [**認証設定**](./docs_j/get-started/authentication.md) - 詳細な認証設定。
- [**設定ガイド**](./docs_j/get-started/configuration.md) - 設定とカスタマイズ。
- [**キーボードショートカット**](./docs_j/cli/keyboard-shortcuts.md) - 生産性向上ヒント。

### コア機能

- [**コマンドリファレンス**](./docs_j/cli/commands.md) - すべてのスラッシュコマンド（`/help`、`/chat` など）。
- [**カスタムコマンド**](./docs_j/cli/custom-commands.md) - 独自の再利用可能なコマンドを作成。
- [**コンテキストファイル (GEMINI.md)**](./docs_j/cli/gemini-md.md) - Gemini CLIに永続的なコンテキストを提供。
- [**チェックポイント**](./docs_j/cli/checkpointing.md) - 会話を保存して再開。
- [**トークンキャッシュ**](./docs_j/cli/token-caching.md) - トークン使用量を最適化。

### ツールと拡張機能

- [**組み込みツール概要**](./docs_j/tools/index.md)
  - [ファイルシステム操作](./docs_j/tools/file-system.md)
  - [シェルコマンド](./docs_j/tools/shell.md)
  - [ウェブフェッチと検索](./docs_j/tools/web-fetch.md)
- [**MCPサーバー統合**](./docs_j/tools/mcp-server.md) - カスタムツールで拡張。
- [**カスタム拡張機能**](./docs_j/extensions/index.md) - 独自のコマンドを構築して共有。

### 高度なトピック

- [**ヘッドレスモード (スクリプト)**](./docs_j/cli/headless.md) - 自動化されたワークフローでGemini CLIを使用。
- [**アーキテクチャ概要**](./docs_j/architecture.md) - Gemini CLIの仕組み。
- [**IDE統合**](./docs_j/ide-integration/index.md) - VS Codeコンパニオン。
- [**サンドボックスとセキュリティ**](./docs_j/cli/sandbox.md) - 安全な実行環境。
- [**信頼できるフォルダ**](./docs_j/cli/trusted-folders.md) - フォルダごとの実行ポリシーを制御。
- [**エンタープライズガイド**](./docs_j/cli/enterprise.md) - 企業環境でのデプロイと管理。
- [**テレメトリとモニタリング**](./docs_j/cli/telemetry.md) - 使用状況トラッキング。
- [**ツールAPI開発**](./docs_j/core/tools-api.md) - カスタムツールを作成。
- [**ローカル開発**](./docs_j/local-development.md) - ローカル開発ツール。

### トラブルシューティングとサポート

- [**トラブルシューティングガイド**](./docs_j/troubleshooting.md) - 一般的な問題と解決策。
- [**FAQ**](./docs_j/faq.md) - よくある質問。
- CLIから直接問題を報告するには `/bug` コマンドを使用します。

### MCPサーバーの使用

`~/.gemini/settings.json` でMCPサーバーを設定し、Gemini CLIをカスタムツールで拡張します。

```text
> @github List my open pull requests
> @slack Send a summary of today's commits to #dev channel
> @database Run a query to find inactive users
```

セットアップ手順については、[MCPサーバー統合ガイド](./docs_j/tools/mcp-server.md)をご覧ください。

## 🤝 貢献

貢献を歓迎します！Gemini CLIは完全にオープンソース（Apache 2.0）であり、コミュニティに以下を推奨しています。

- バグの報告と機能の提案。
- ドキュメントの改善。
- コードの改善の提出。
- 独自のMCPサーバーと拡張機能を共有。

開発セットアップ、コーディング標準、プルリクエストの提出方法については、[貢献ガイド](./CONTRIBUTING.md)をご覧ください。

計画されている機能と優先順位については、[公式ロードマップ](https://github.com/orgs/google-gemini/projects/11)を確認してください。

## 📖 リソース

- **[公式ロードマップ](./ROADMAP.md)** - 次の予定を確認。
- **[変更履歴](./docs_j/changelogs/index.md)** - 最近の注目すべき更新を確認。
- **[NPMパッケージ](https://www.npmjs.com/package/@google/gemini-cli)** - パッケージレジストリ。
- **[GitHub課題](https://github.com/google-gemini/gemini-cli/issues)** - バグを報告したり、機能を要求したりします。
- **[セキュリティアドバイザリ](https://github.com/google-gemini/gemini-cli/security/advisories)** - セキュリティ更新。

### アンインストール

削除手順については、[アンインストールガイド](docs_j/cli/uninstall.md)をご覧ください。

## 📄 法的

- **ライセンス**: [Apache License 2.0](LICENSE)
- **利用規約**: [利用規約とプライバシー](./docs_j/tos-privacy.md)
- **セキュリティ**: [セキュリティポリシー](SECURITY.md)

---

<p align="center">
  Built with ❤️ by Google and the open source community
</p>