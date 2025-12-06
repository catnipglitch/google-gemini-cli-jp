# MCP サーバー

Model Context Protocol (MCP) サーバーは Gemini モデルが外部システムや API と対話するための架け橋です。CLI は `mcpServers` 設定で複数サーバーを登録し、各ツールを `DiscoveredMCPTool` として扱います。

## 発見と実行の仕組み

1. `settings.json` の `mcpServers` を列挙し、Stdio/SSE/Streamable HTTP を使って接続。
2. ツール定義を取得・スキーマ検証・名前のサニタイズ（許可文字以外は `_` 置換、63 文字以上は切り詰め）。
3. 名前衝突があるサーバーは `serverName__toolName` でプレフィックス。
4. 各ツールは確認（`trust` の有無や allow list）を経て MCP サーバーへ呼び出し、`llmContent`/`returnDisplay` を整形して返す。
5. rich content（`text`, `image`, `audio`, `resource`）を `CallToolResult` の `content` 配列で返して多様な出力を 1 回の呼び出しで提供可能。

## 設定の書き方

`settings.json` の `mcpServers` 各エントリーで次を指定:
- `command`（Stdio）、`url`（SSE）、`httpUrl`（HTTP）のいずれか
- `args`, `env`, `cwd`, `timeout`, `trust`, `includeTools`, `excludeTools`, `headers` など
- `includeTools` は指定したツールのみ、有効時の allow list。
- `excludeTools` は優先的に除外（`includeTools` より優先）。

さらに `mcp` オブジェクトで `allowed`/`excluded` リストやグローバル `serverCommand` を設定可能。

## OAuth 対応

SSE/HTTP サーバーは OAuth 2.0 に対応。CLI は 401 応答時に `Authorization` を使った認証を動的に検出し、ブラウザを開いて CSA/トークンを処理します。
- **`authProviderType`:** `dynamic_discovery`, `google_credentials`, `service_account_impersonation`
- **トークン管理:** `~/.gemini/mcp-oauth-tokens.json` に安全に保存・更新。
- `/mcp auth` コマンドで認証、一覧、再認証を実行。

## CLI コマンド

- `/mcp`: サーバー一覧、接続状態、ツール一覧を表示。
- `gemini mcp add`: Stdio/HTTP/SSE サーバーを追加（`-s` で scope 選択、`--timeout`, `--trust`, `--include-tools`, `--exclude-tools` などを指定）。
- `gemini mcp list`: 接続状態を一覧。
- `gemini mcp remove`: サーバー設定を削除。

## トラブルシューティング

- 接続できない: `command`, `args`, `cwd` を確認、手動で実行できるかテスト。
- ツールが登録されない: MCP サーバーが `listTool` を返しているか、スキーマが正しいか確認。
- 実行失敗: パラメータ検証、タイムアウト、例外処理をチェック。
- サンドボックスで失敗: 必要な実行環境やネットワークアクセスをサンドボックス内に整備。

## セキュリティとパフォーマンス

- `trust` を `true` にすると確認をスキップするため信頼済サーバーだけに限定。
- 環境変数には API キーなど機密情報を含めることがあり、アクセス制御と監査を徹底。
- タイムアウトを適切に設定し、接続が切断されると `DISCONNECTED` へ移行。
- 不要なツール定義は除去され、使わないサーバーは自動クリーンアップ。

MCP サーバーの導入により Gemini CLI の機能を柔軟かつ安全に拡張できます。
