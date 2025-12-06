# Gemini CLI コンパニオンプラグイン仕様

> 最終更新: 2025年9月15日

この仕様は、VS Code 用拡張と同等の IDE モード（ネイティブ差分や文脈認識）を他エディタ向けに実装する際の契約です。

## I. 通信インターフェース

- プラグインは **MCP over HTTP** を実装するローカル HTTP サーバーを起動し、エンドポイント（例: `/mcp`）を公開。ポートは `0`（動的割り当て）でリッスン。
- CLI はプロセスツリーから IDE の PID を特定し、`os.tmpdir()/gemini/ide/` に生成された `gemini-ide-server-${PID}-${PORT}.json` ファイルを探すことで接続先を発見。
- 発見ファイルには `port`, `workspacePath`（コロン/セミコロン区切りの絶対パス）、`authToken`, `ideInfo`（名前・表示名）を含める。authToken は `Authorization: Bearer <token>` で検証する。
- より確実な接続には discovery file に加え、統合ターミナルで `GEMINI_CLI_IDE_SERVER_PORT` 環境変数も設定するとよい（複数ウィンドウ時の識別に役立つ）。

## II. 文脈インターフェース

- `ide/contextUpdate` 通知（50ms デバウンス推奨）でファイルの開閉、フォーカス、カーソル/選択を送信。
- 送信ペイロードは `IdeContext` で、`openFiles`, `cursor`, `selectedText` など必要な情報を含む。仮想ファイルは除外。
- CLI は `timestamp` でソートし、最大 10 ファイル、16KB の選択テキストにトリム。最も新しいファイルのみを「active」とみなし、カーソルと選択はそれだけに付与。

## III. 差分インターフェース

- `openDiff` ツール: MCP サーバーに登録し、diff ビューを開くよう IDE に指示。`OpenDiffRequest` で `filePath`, `newContent` を送信。成功時は `content: []`、失敗時は `isError: true` とエラーメッセージを返す。
- `closeDiff` ツール: 同様に diff ビューを閉じる。成功時は `TextContent` に最終コンテンツを含め、失敗時はエラーメッセージを返す。
- `ide/diffAccepted` `ide/diffRejected` 通知: ユーザー操作の結果を CLI に通知。承認時は最終コンテンツ含む、拒否時はファイルパスのみ。

## IV. ライフサイクル

- IDE 起動/プラグイン有効化時: MCP サーバーを起動し discovery file を生成。
- IDE 終了/プラグイン無効化時: MCP サーバーを停止し discovery file を削除。

この仕様を満たせば、他エディタでも Gemini CLI IDE モードと同等の統合が実現できます。
