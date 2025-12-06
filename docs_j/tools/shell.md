# Shell ツール (`run_shell_command`)

`run_shell_command` はシェルコマンドを実行するツールで、スクリプトや対話型コマンド（`vim`, `git rebase -i` など）も実行可能です。`tools.shell.enableInteractiveShell` を `true` にすると `node-pty` を使い、対話対応となります。

- **引数:** `command`（必須）、`description`、`directory`（プロジェクトルート相対）。
- **出力:** コマンド文字列、実行ディレクトリ、`stdout`/`stderr`、エラー、終了コード、シグナル、バックグラウンド PID などを含む。
- **使用例:** `ls -la`、`./scripts/myscript.sh`、`npm run dev &` のように背景実行も可能。

## 設定

- `tools.shell.enableInteractiveShell`: `true` で `node-pty` を使い、対話型セッションを有効。
- `tools.shell.showColor`: インタラクティブシェルでカラー出力を許可。
- `tools.shell.pager`: `less` など独自ページャーを設定（`cat` がデフォルト）。

## セキュリティ・制限

- **環境変数:** `GEMINI_CLI=1` が子プロセスに設定され、スクリプトは CLI 内で実行されていることを検出可。
- **コマンド制限:** `tools.core` で許可プレフィックス（例 `run_shell_command(git)`）、`tools.exclude` でブロックプレフィックス（例 `run_shell_command(rm)`）を定義。`tools.exclude` が優先され、チェーン (`&&`, `||`, `;`) は個別検証される。ワイルドカード `run_shell_command` を `exclude` に入れると全コマンドを拒否。`
- **セキュリティ注意:** `excludeTools` 内のプレフィックスマッチは簡易な文字列照合のため、完全なセキュリティ機構ではなく `coreTools` で明示的に許可すること。

## 対話型利用

対話型コマンド実行時は CLI 上で `ctrl+f` でフォーカスして入力を送信できます。出力は TUI も含めて正しく描画されます。

## エラー監視

`Stderr`/`Error`/`Exit Code` を確認し、必要ならバックグラウンドプロセスの PID を参照。バックグラウンド実行 (`&`) した場合、プロセスは継続し PID を戻します。
