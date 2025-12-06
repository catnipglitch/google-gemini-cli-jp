# Gemini CLI ドキュメントへようこそ

このドキュメントは、コマンドラインインターフェースを通じて Gemini モデルと対話できるツールである Gemini CLI のインストール、使用、開発に関する包括的なガイドを提供します。

## Gemini CLI の概要

Gemini CLI は、Gemini モデルの機能をインタラクティブな Read-Eval-Print Loop (REPL) 環境でターミナルにもたらします。Gemini CLI は、ローカルサーバー（`packages/core`）と通信するクライアント側アプリケーション（`packages/cli`）で構成されており、ローカルサーバーは Gemini API とその AI モデルへのリクエストを管理します。Gemini CLI には、ファイルシステム操作の実行、シェル実行、ウェブフェッチなどのさまざまなツールも含まれており、これらは `packages/core` によって管理されます。

## ドキュメントのナビゲーション

このドキュメントは、以下のセクションにまとめられています。

### 概要

- [**アーキテクチャ概要**](./architecture.md): Gemini CLI のコンポーネントとそれらの相互作用を含む、高レベルな設計を理解します。
- [**貢献ガイド**](../CONTRIBUTING.md): セットアップ、ビルド、テスト、コーディング規約など、コントリビューターと開発者向けの情報。

### はじめに

- [**Gemini CLI クイックスタート**](./get-started/index.md): Gemini CLI をはじめましょう。
- [**Gemini CLI での Gemini 3 Pro**](./get-started/gemini-3.md): Gemini 3 を有効にして使用する方法を学びます。
- [**認証**](./get-started/authentication.md): Gemini CLI への認証を行います。
- [**設定**](./get-started/configuration.md): CLI の設定方法を学びます。
- [**インストール**](./get-started/installation.md): Gemini CLI をインストールして実行します。
- [**例**](./get-started/examples.md): Gemini CLI の使用例。

### CLI

- [**はじめに: Gemini CLI**](./cli/index.md): コマンドラインインターフェースの概要。
- [**コマンド**](./cli/commands.md): 利用可能な CLI コマンドの説明。
- [**チェックポイント**](./cli/checkpointing.md): チェックポイント機能のドキュメント。
- [**カスタムコマンド**](./cli/custom-commands.md): 頻繁に使用するプロンプト用の独自のコマンドとショートカットを作成します。
- [**エンタープライズ**](./cli/enterprise.md): エンタープライズ向けの Gemini CLI。
- [**ヘッドレスモード**](./cli/headless.md): スクリプト作成と自動化のために Gemini CLI をプログラムで利用します。
- [**キーボードショートカット**](./cli/keyboard-shortcuts.md): ワークフローを改善するためのすべてのキーボードショートカットのリファレンス。
- [**モデル選択**](./cli/model.md): `/model` を使用してコマンドを処理するモデルを選択します。
- [**サンドボックス**](./cli/sandbox.md): セキュアなコンテナ化された環境でツール実行を分離します。
- [**設定**](./cli/settings.md): `/settings` を使用して CLI の動作と外観のさまざまな側面を設定します。
- [**テレメトリ**](./cli/telemetry.md): CLI のテレメトリの概要。
- [**テーマ**](./cli/themes.md): Gemini CLI のテーマ。
- [**トークンキャッシュ**](./cli/token-caching.md): トークンキャッシュと最適化。
- [**信頼できるフォルダ**](./cli/trusted-folders.md): 信頼できるフォルダセキュリティ機能の概要。
- [**チュートリアル**](./cli/tutorials.md): Gemini CLI のチュートリアル。
- [**アンインストール**](./cli/uninstall.md): Gemini CLI のアンインストール方法。

### コア

- [**はじめに: Gemini CLI コア**](./core/index.md): Gemini CLI コアに関する情報。
- [**メモポート**](./core/memport.md): メモリインポートプロセッサの使用。
- [**ツールAPI**](./core/tools-api.md): コアがツールを管理し公開する方法に関する情報。
- [**ポリシーエンジン**](./core/policy-engine.md): ツール実行をきめ細かく制御するためにポリシーエンジンを使用します。

### ツール

- [**はじめに: Gemini CLI ツール**](./tools/index.md): Gemini CLI のツールに関する情報。
- [**ファイルシステムツール**](./tools/file-system.md): `read_file` および `write_file` ツールのドキュメント。
- [**シェルツール**](./tools/shell.md): `run_shell_command` ツールのドキュメント。
- [**ウェブフェッチツール**](./tools/web-fetch.md): `web_fetch` ツールのドキュメント。
- [**ウェブ検索ツール**](./tools/web-search.md): `google_web_search` ツールのドキュメント。
- [**メモリツール**](./tools/memory.md): `save_memory` ツールのドキュメント。
- [**Todoツール**](./tools/todos.md): `write_todos` ツールのドキュメント。
- [**MCPサーバー**](./tools/mcp-server.md): Gemini CLI で MCP サーバーを使用します。

### 拡張機能

- [**はじめに: 拡張機能**](./extensions/index.md): 新しい機能で CLI を拡張する方法。
- [**拡張機能の使用開始**](./extensions/getting-started-extensions.md): 独自の拡張機能を構築する方法を学びます。
- [**拡張機能のリリース**](./extensions/extension-releasing.md): Gemini CLI 拡張機能をリリースする方法。

### フック

- [**フック**](./hooks/index.md): 主要なライフサイクルポイントで Gemini CLI の動作をインターセプトおよびカスタマイズします。
- [**フックの記述**](./hooks/writing-hooks.md): 包括的な例で最初のフックを作成する方法を学びます。
- [**ベストプラクティス**](./hooks/best-practices.md): フックのセキュリティ、パフォーマンス、デバッグのガイドライン。

### IDE統合

- [**IDE統合の概要**](./ide-integration/index.md): CLI をエディターに接続します。
- [**IDEコンパニオン拡張機能仕様**](./ide-integration/ide-companion-spec.md): IDEコンパニオン拡張機能を構築するための仕様。

### 開発

- [**NPM**](./npm.md): プロジェクトのパッケージがどのように構成されているかの詳細。
- [**リリース**](./releases.md): プロジェクトのリリースとデプロイサイクルに関する情報。
- [**変更ログ**](./changelogs/index.md): Gemini CLI のハイライトと注目すべき変更。
- [**統合テスト**](./integration-tests.md): このプロジェクトで使用されている統合テストフレームワークに関する情報。
- [**IssueとPRの自動化**](./issue-and-pr-automation.md): Issue とプルリクエストを管理およびトリアージするために使用する自動化されたプロセスについての詳細な概要。

### サポート

- [**FAQ**](./faq.md): よくある質問。
- [**トラブルシューティングガイド**](./troubleshooting.md): 一般的な問題の解決策を見つけます。
- [**割り当てと料金**](./quota-and-pricing.md): 無料枠と有料オプションについて学びます。
- [**利用規約とプライバシー通知**](./tos-privacy.md): Gemini CLI の使用に適用される利用規約とプライバシー通知に関する情報。

このドキュメントが、Gemini CLI を最大限に活用するためにお役に立てば幸いです。