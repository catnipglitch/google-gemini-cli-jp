# Gemini CLI 設定

> **設定フォーマットについて (2025/9/17):** `settings.json` は整理された新フォーマットへ移行しました。
>
> - **2025/9/10** の安定版で新フォーマットがサポート。
> - **2025/9/17** 以降、古い形式から自動移行が始まります。
>
> 旧フォーマットについては [v1 設定ドキュメント](./configuration-v1.md) を参照してください。

Gemini CLI は環境変数、コマンドライン引数、設定ファイルなど多様な方法で構成できます。この文書では各設定手段と `settings.json` の構造を解説します。

## 設定の優先順位

1. デフォルト値
2. システムデフォルトファイル（`/etc/gemini-cli/system-defaults.json` など）
3. ユーザー設定（`~/.gemini/settings.json`）
4. プロジェクト設定（`.gemini/settings.json`）
5. システム上書き（`/etc/gemini-cli/settings.json` など）
6. 環境変数 (`.env` 経由も含む)
7. コマンドライン引数

## 設定ファイルの場所とスキーマ

- システムデフォルト、ユーザー、プロジェクト、グローバルシステムの 4 箇所が存在。
- JSON スキーマは `schemas/settings.schema.json` を参照。外部で作業する場合は
  `https://raw.githubusercontent.com/google-gemini/gemini-cli/main/schemas/settings.schema.json` を利用可能。
- 設定内の文字列は `$VAR` または `${VAR}` で環境変数を参照可能。拡張機能ごとの `.env` も自動ロード。

エンタープライズ向け配備には [Enterprise Configuration](../cli/enterprise.md) も参照してください。

## プロジェクト `.gemini` ディレクトリ

`.gemini/` 内には設定ファイルの他に、カスタムサンドボックス（例: `.gemini/sandbox.Dockerfile` や `.sb` ファイル）などプロジェクト固有の補助ファイルを置けます。

## `settings.json` の構成

設定はカテゴリ別にオブジェクトへまとめます。
自動生成部分（`SETTINGS-AUTOGEN`）では `general`, `output`, `ui`, `ide`, `privacy`, `model`, `modelConfigs`, `context`, `tools`, `advanced` などのカテゴリが追加されています。

### general
- `previewFeatures`, `preferredEditor`, `vimMode`, `disableAutoUpdate`, `disableUpdateNag`, `checkpointing.enabled`, `enablePromptCompletion` など、機能のオン/オフ。
- `retryFetchErrors`, `debugKeystrokeLogging`, `sessionRetention`（`enabled`, `maxAge`, `maxCount`, `minRetention`）でセッション保持やログを調整。

### output
- `format`: `text`, `json`, `stream-json` を選択し、非対話モードの出力を制御。

### ui
- テーマ (`ui.theme`, `ui.customThemes`) や表示制御（ヒント、バナー、サマリ、フッター、行番号、引用、モデル情報など）。
- `ui.useAlternateBuffer` / `ui.incrementalRendering` などは再起動が必要。
- `ui.customWittyPhrases` でロード中のメッセージをカスタム可能。
- アクセシビリティでは `screenReader`, `disableLoadingPhrases` を設定できます。

### ide
- `ide.enabled` で IDE 統合モードを有効にし、`ide.hasSeenNudge` で案内済みフラグを管理。

### privacy
- `privacy.usageStatisticsEnabled` で利用統計収集のオン/オフ。

### model
- `model.name`, `model.maxSessionTurns`, `model.summarizeToolOutput`, `model.compressionThreshold`, `model.skipNextSpeakerCheck` などモデル固有値。

### modelConfigs
- `modelConfigs.aliases`: モデルプリセットの定義と継承。`modelConfigs.customAliases` で独自エイリアス、`modelConfigs.overrides` で条件ベースの上書き。

### context
- `context.fileName`, `context.importFormat`, `context.discoveryMaxDirs`, `context.includeDirectories`, `context.loadMemoryFromIncludeDirectories`。
- `context.fileFiltering` で `.gitignore`, `.geminiignore`, 再帰検索、ファジー検索を制御。

### tools
- `tools.sandbox` でサンドボックス環境を指定（`docker`, `podman`, カスタムコマンド）。
- `tools.shell` 以下で対話型シェル、ページャー、カラー表示、タイムアウトなど。
- `tools.autoAccept`, `tools.core`, `tools.allowed`, `tools.exclude` でツールセットを調整。

### advanced
- セキュリティ制御として `advanced.excludedEnvVars` などを提供。

### `mcpServers`
- `command`, `args`, `env`, `cwd`, `url`, `httpUrl`, `headers`, `timeout`, `trust`, `description`, `includeTools`, `excludeTools` を設定して MCP サーバーを接続。ツール名の衝突は `serverAlias__toolName` で回避。

## 設定例

```json
{
  "ui": { "theme": "GitHub", "hideBanner": true },
  "tools": { "sandbox": "docker", "discoveryCommand": "bin/get_tools", "callCommand": "bin/call_tool", "exclude": ["write_file"] },
  "mcpServers": { ... },
  "telemetry": { "enabled": true, "target": "local", "otlpEndpoint": "http://localhost:4317", "logPrompts": true },
  "privacy": { "usageStatisticsEnabled": true },
  "model": { "name": "gemini-1.5-pro-latest", "maxSessionTurns": 10, "summarizeToolOutput": { "run_shell_command": { "tokenBudget": 100 } } },
  "context": { "fileName": ["CONTEXT.md", "GEMINI.md"], "includeDirectories": ["path/to/dir1", "~/path/to/dir2", "../path/to/dir3"], "loadFromIncludeDirectories": true, "fileFiltering": { "respectGitIgnore": false } },
  "advanced": { "excludedEnvVars": ["DEBUG", "DEBUG_MODE", "NODE_ENV"] }
}
```

## シェル履歴

実行したシェルコマンドは `~/.gemini/tmp/<project_hash>/shell_history` に保存されます。

## 環境変数 / `.env` ファイル

ロード順序:
1. カレントディレクトリの `.env`
2. 親方向へ探索（`.git` またはホームで停止）
3. `~/.env`

`DEBUG`, `DEBUG_MODE` はプロジェクト `.env` では読み込まれず、必要なら `.gemini/.env` を利用。

主な環境変数:
- `GEMINI_API_KEY`, `GEMINI_MODEL`
- `GOOGLE_API_KEY`, `GOOGLE_CLOUD_PROJECT`, `GOOGLE_APPLICATION_CREDENTIALS`
- `OTLP_GOOGLE_CLOUD_PROJECT`, `GEMINI_TELEMETRY_*` 系
- `GEMINI_SANDBOX`, `SEATBELT_PROFILE`, `NO_COLOR`, `CLI_TITLE`, `CODE_ASSIST_ENDPOINT`

## コマンドライン引数

`--model`, `--prompt`, `--prompt-interactive`, `--output-format`, `--sandbox`, `--debug`, `--help`, `--yolo`, `--approval-mode`, `--allowed-tools`, `--extensions`, `--list-extensions`, `--resume`, `--list-sessions`, `--delete-session`, `--include-directories`, `--screen-reader`, `--version`, `--experimental-acp`, `--allowed-mcp-server-names`, `--fake-responses`, `--record-responses` など。必要に応じて CLI のドキュメントを参照してください。

## コンテキストファイル

`context.fileName` で指定された Markdown を階層的に読み込み、AI に文脈を提供します。検索順:

1. グローバル (`~/.gemini/<contextFileName>`)
2. カレントと親ディレクトリ
3. サブディレクトリ（最大 200、`context.discoveryMaxDirs` で調整）

`/memory show` や `/memory refresh` で現在の文脈を確認・再読込。`@path/to/file.md` で他ファイルをインポート。

## サンドボックス

安全でない操作（シェルやファイル書き換え）はサンドボックス内実行がおすすめ。オプション:
- `--sandbox` または `-s`
- `GEMINI_SANDBOX` 環境変数
- `--yolo` / `--approval-mode=yolo`

デフォルトは Docker イメージ `gemini-cli-sandbox`。
プロジェクト固有の Dockerfile を `.gemini/sandbox.Dockerfile` に置き、`BUILD_SANDBOX=1 gemini -s` でビルド可。

## 利用統計

匿名化された利用統計を収集し、ツール呼び出し、API リクエスト、セッション構成を記録。収集対象に以下は含まれません：個人情報、プロンプト/レスポンスの内容、ファイルの内容。

収集を停止するには `privacy.usageStatisticsEnabled` を `false` に設定してください。

```json
{
  "privacy": {
    "usageStatisticsEnabled": false
  }
}
```
