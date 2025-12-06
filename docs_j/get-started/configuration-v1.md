# Gemini CLI 設定 (旧 v1 フォーマット)

**旧式の設定ファイル形式について**

この文書は `settings.json` の旧 v1 フォーマットを説明します。現在非推奨であり、**[2025年9月10日]** の安定版で新フォーマットが有効化され、**[2025年9月17日]** から自動移行されます。

新形式については [Config ドキュメント](./configuration.md) を参照してください。

## 設定の優先順位

1. デフォルト値
2. システムのデフォルトファイル
3. ユーザー設定 (`~/.gemini/settings.json`)
4. プロジェクト設定 (`.gemini/settings.json`)
5. システム上書き (`/etc/gemini-cli/settings.json` など)
6. 環境変数
7. コマンドライン引数

下位の設定が上位をオーバーライドします。

## 設定ファイルの場所

- **システムデフォルト:** Linux `/etc/gemini-cli/system-defaults.json`、Windows `C:\ProgramData\gemini-cli\system-defaults.json`、macOS `/Library/Application Support/GeminiCli/system-defaults.json`。
- **ユーザー:** `~/.gemini/settings.json`
- **プロジェクト:** プロジェクトルート内の `.gemini/settings.json`
- **システム上書き:** Linux `/etc/gemini-cli/settings.json` など。

`GEMINI_CLI_SYSTEM_DEFAULTS_PATH` や `GEMINI_CLI_SYSTEM_SETTINGS_PATH` でパス変更可。

## `settings.json` の主な項目

- `contextFileName`: 使用するコンテキストファイル名（例: `GEMINI.md`, `AGENTS.md`）。
- `bugCommand`: `/bug` に相当する URL テンプレート。
- `fileFiltering`: `@` コマンドで `.gitignore` を尊重するか、再帰検索やファジー検索の切替。
- `coreTools`: 利用可能な組込ツールの名前（`ShellTool(ls)` など）。
- `allowedTools`: 確認ダイアログを省略するツール。
- `excludeTools`: 利用禁止ツール。`run_shell_command(rm -rf)` のような絞込も可。
- `allowMCPServers` / `excludeMCPServers`: 有効または無効な MCP サーバー名。
- `autoAccept`: 安全なツール呼び出しを自動承認するか。
- `theme`: CLI テーマ（例: `GitHub`）。
- `vimMode`: 入力欄の Vim モード切替。
- `sandbox`: サンドボックス利用（`true`, `docker`, `podman` など）。
- `toolDiscoveryCommand` / `toolCallCommand`: 独自ツールの検索・呼び出しコマンド。
- `mcpServers`: MCP サーバー設定（`command`, `args`, `env`, `cwd`, `url`, `httpUrl`, `headers`, `timeout`, `trust`, `includeTools`, `excludeTools` など）。
- `checkpointing`: `/restore` 機能の有効化フラグ。
- `preferredEditor`: 差分表示時に使うエディタ（デフォルト `vscode`）。
- `telemetry`: ログ・メトリクス設定。`enabled`, `target`, `otlpEndpoint`, `logPrompts` など。
- `usageStatisticsEnabled`: 利用統計の収集。
- `hideTips`, `hideBanner`: CLI のヒントとバナー表示制御。
- `maxSessionTurns`: セッションの最大ターン数。
- `summarizeToolOutput`: `run_shell_command` 出力要約トークンなど。
- `excludedProjectEnvVars`: `.env` から除外する環境変数。
- `includeDirectories`: 追加のディレクトリをコンテキストに含める。
- `loadMemoryFromIncludeDirectories`: 追加ディレクトリから GEMINI.md を読み込むか。
- `showLineNumbers`: 出力内のコードブロックに行番号を表示。
- `accessibility`: スクリーンリーダーや読み込みフレーズ制御。

例:

```json
{
  "theme": "GitHub",
  "sandbox": "docker",
  "toolDiscoveryCommand": "bin/get_tools",
  "mcpServers": {
    "mainServer": { "command": "bin/mcp_server.py" }
  },
  "telemetry": { "enabled": true, "target": "local", "otlpEndpoint": "http://localhost:4317", "logPrompts": true },
  "usageStatisticsEnabled": true,
  "hideTips": false,
  "hideBanner": false,
  "maxSessionTurns": 10,
  "summarizeToolOutput": { "run_shell_command": { "tokenBudget": 100 } },
  "excludedProjectEnvVars": ["DEBUG", "DEBUG_MODE", "NODE_ENV"],
  "includeDirectories": ["path/to/dir1", "~/path/to/dir2", "../path/to/dir3"],
  "loadMemoryFromIncludeDirectories": true
}
```

## シェル履歴

実行したシェルコマンドは `~/.gemini/tmp/<project_hash>/shell_history` に保存されます。
`<project_hash>` はプロジェクトルートから生成される一意の値です。

## 環境変数と `.env` ファイル

CLI は `.env` ファイルから環境変数を読み込みます。探索順序は次の通りです。

1. カレントディレクトリの `.env`
2. 親ディレクトリ上位へ向けて `.env` を探索（`.git` やホームまで）
3. 見つからなければ `~/.env`

`DEBUG` や `DEBUG_MODE` はデフォルトで読み込まれません。`.gemini/.env` では除外されません。

主要な環境変数:

- `GEMINI_API_KEY`, `GEMINI_MODEL`
- `GOOGLE_API_KEY`, `GOOGLE_CLOUD_PROJECT`, `GOOGLE_APPLICATION_CREDENTIALS`
- `OTLP_GOOGLE_CLOUD_PROJECT`, `GOOGLE_CLOUD_LOCATION`
- `GEMINI_SANDBOX`, `HTTP_PROXY`/`HTTPS_PROXY`
- `SEATBELT_PROFILE` (macOS 用)
- `DEBUG`, `DEBUG_MODE` (デフォルト除外)
- `NO_COLOR`, `CLI_TITLE`, `CODE_ASSIST_ENDPOINT`

## コマンドライン引数

主なフラグ:

- `--model`, `-m`
- `--prompt`, `-p`
- `--prompt-interactive`, `-i`
- `--sandbox`, `--sandbox-image`
- `--debug`, `--help`, `--show-memory-usage`
- `--yolo`, `--approval-mode`
- `--allowed-tools`
- `--telemetry*` 系
- `--extensions`, `--list-extensions`
- `--include-directories`
- `--screen-reader`, `--version`

## コンテキストファイル

`GEMINI.md` などの Markdown ファイルを階層的に読み込み、モデルに指示を与えます。CLI フッターに読み込まれたファイル数が表示され、`/memory show` で内容を確認、`/memory refresh` で再読み込みできます。

- グローバル: `~/.gemini/<contextFileName>`
- プロジェクトルートと親ディレクトリ
- サブディレクトリ（最大 200 まで、`memoryDiscoveryMaxDirs` で制限）

`@path/to/file.md` 構文で別ファイルをインポート可能。

## サンドボックス

デフォルトでは無効。`--sandbox`、`GEMINI_SANDBOX`、`--yolo`/`--approval-mode=yolo` で有効にできます。デフォルトは `gemini-cli-sandbox` Docker イメージ。

プロジェクト固有の Dockerfile は `.gemini/sandbox.Dockerfile` に配置可能。`BUILD_SANDBOX=1 gemini -s` で自動ビルドします。

## 利用統計

匿名化された利用統計を収集し、ツール呼び出し、API リクエスト、セッション設定などに基づいて改善に役立てています。収集対象は PII やプロンプト/レスポンス内容、ファイル内容を含みません。

オプトアウトするには `settings.json` で `usageStatisticsEnabled` を `false` にします。

```json
{
  "usageStatisticsEnabled": false
}
```
