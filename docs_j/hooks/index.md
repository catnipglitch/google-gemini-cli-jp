# Gemini CLI フック

フックはエージェントループの特定タイミングで CLI が実行するスクリプトで、ソースコードを変更せずに挙動をカスタマイズできます。
- チュートリアル: [writing-hooks guide](writing-hooks.md)
- ベストプラクティス: [best-practices](best-practices.md)

## フックの用途

- 文脈を補強してプロンプト前に情報追加
- 危険な操作を検証・ブロック
- セキュリティポリシーの適用
- ツール利用や LLM 応答をログ
- モデル選択やパラメータを動的に調整

すべてのフックは同期的に実行され、エージェントは完了を待ってから次へ進みます。

## イベント一覧

| イベント | 発火タイミング | 主な用途 |
| --- | --- | --- |
| `SessionStart` | セッション開始 | リソース初期化、文脈読み込み |
| `SessionEnd` | セッション終了 | クリーンアップ、状態保存 |
| `BeforeAgent` | ユーザー入力後、計画前 | プロンプト検証、文脈挿入 |
| `AfterAgent` | エージェントループ完了 | 出力レビュー、継続制御 |
| `BeforeModel` | LLM 送信前 | プロンプト修正、命令追加 |
| `AfterModel` | モデル応答後 | 応答加工、ログ |
| `BeforeToolSelection` | ツール選択直前 | 利用可能ツール絞込 |
| `BeforeTool` | ツール実行前 | 引数検証、禁止操作禁止 |
| `AfterTool` | ツール後 | 結果処理、テスト実行 |
| `PreCompress` | 文脈圧縮前 | 状態保存や通知 |
| `Notification` | 通知発生時 | 自動承認や記録 |

## コマンドフック

現在サポートされているのは shell コマンドで実行される**command hooks**のみ。設定例:

```json
{
  "type": "command",
  "command": "$GEMINI_PROJECT_DIR/.gemini/hooks/my-hook.sh",
  "timeout": 30000
}
```

matcher で特定のツールやイベントを対象にできます。例: `BeforeTool` で `WriteFile` や `Edit` のみ実行する設定。

- **完全一致:** `"ReadFile"`
- **正規表現:** `"Write.*|Edit"`
- **ワイルドカード:** `"*"` または空文字はすべてのツール
- セッションイベントにも matcher（`startup`, `resume`, `clear`, `exit`, `prompt_input_exit`, `manual`, `auto`, `ToolPermission`）を設定できます。

## 入出力契約

### コマンドフックの通信
- **入力:** STDIN に JSON
- **出力:** 終了コード + stdout/stderr

### 終了コード
- `0`: 成功（stdout はユーザー表示や文脈として利用）
- `2`: ブロック（stderr が表示され操作がキャンセル）
- その他: 警告（ログに残して続行）

### 共通入力フィールド
```json
{
  "session_id": "abc123",
  "cwd": "/path/to/project",
  "hook_event_name": "BeforeTool",
  "timestamp": "2025-12-01T10:30:00Z"
}
```

### 代表的イベントの入出力
- **BeforeTool:** 実行前のツール名・入力。
- **AfterTool:** ツール応答・追加文脈。
- **BeforeAgent / BeforeModel:** プロンプトや LLM リクエストを受け取り、`decision` や追加メッセージで制御。
- **Notification:** 通知情報を受け取り、`systemMessage` でログ可能。

例:
```json
{
  "decision": "allow|deny",
  "hookSpecificOutput": { "hookEventName": "BeforeTool", "additionalContext": "..." }
}
```

単純に `echo "ReadFile,WriteFile"` で `BeforeToolSelection` への除外／許可も可能。

## 設定方法

`settings.json` の `hooks` オブジェクトで定義。
優先順位: システムデフォルト < ユーザー < プロジェクト < システム設定。

### 構成スキーマ
```json
{
  "hooks": {
    "EventName": [
      {
        "matcher": "pattern",
        "hooks": [
          { "name": "hook-id", "type": "command", "command": "./hook.sh", "description": "説明", "timeout": 30000 }
        ]
      }
    ]
  }
}
```

`name`（有効化/無効化コマンド用）、`type`, `command`, `description`, `timeout`, `matcher` を指定可能。

### 環境変数
フックは次の環境を受け取る: `GEMINI_PROJECT_DIR`, `GEMINI_SESSION_ID`, `GEMINI_API_KEY`（設定済みの場合）、および親プロセスの環境。

## フックの運用

- `/hooks panel` で登録済みフックを確認。イベント別、ソース（user/project/system）、タイプ、実行状態を表示。
- `/hooks enable hook-name` / `/hooks disable hook-name` で一時的に制御。
- `hooks.disabled` に追加すると（ユーザー・プロジェクト・システムを UNION で合算）永続的に無効化できます。

## Claude Code からの移行

`gemini hooks migrate --from-claude` で Claude 設定を `.gemini/settings.json` へ変換。イベント名/ツール名/matcher をマッピング（例: `PreToolUse`→`BeforeTool`, `Bash`→`RunShellCommand`）します。

## 補足資料

- [Writing Hooks](writing-hooks.md)
- [Best Practices](best-practices.md)
- [Custom Commands](../cli/custom-commands.md)
- [Configuration](../cli/configuration.md)
- [Hooks Design Document](../hooks-design.md)
