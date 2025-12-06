# Gemini CLI のツール

Gemini CLI には Gemini モデルがローカル環境へアクセスしたり操作を実行したりするための組み込みツールが揃っています。コア (`packages/core`) はこれらのスキーマをモデルへ提示し、実行、応答を返します。

## ツール利用の流れ

1. プロンプトを出力
2. CLI → core → Gemini API へ送信（ツール一覧含む）
3. モデルが必要なツールを `FunctionCall` で要求
4. core が検証・ユーザー確認後に実行（`run_shell_command` 等の危険な操作は確認付き）
5. ツール出力を core が受け取り Gemini モデルへ返却
6. モデルが最終応答を作成し CLI へ表示

CLI 内ではツール呼び出しや結果のステータスが逐次表示されます。

## セキュリティ

- 危険な操作（`write_file`, `edit`, `run_shell_command` など）は常に確認を要求
- サンドボックスが有効な場合、全ツールや MCP サーバーはサンドボックス内で利用可能である必要がある
- MCP サーバーや `npx` 実行もサンドボックス環境に存在することを確認

## 主なツールカテゴリ

- **ファイル操作:** [file system tools](./file-system.md)
- **シェル:** [`run_shell_command`](./shell.md)
- **ウェブフェッチ:** [`web_fetch`](./web-fetch.md)
- **ウェブ検索:** [`google_web_search`](./web-search.md)
- **メモリ:** [`save_memory`](./memory.md)
- **Todo:** [`write_todos`](./todos.md)

追加機能:
- **MCP サーバー:** [MCP server](./mcp-server.md) によってローカルサービスや API を仲介
- **サンドボックス:** [CLI サンドボックス](../cli/sandbox.md) で安全にツールを実行
