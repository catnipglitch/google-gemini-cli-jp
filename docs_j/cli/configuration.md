# Gemini CLI の設定

Gemini CLI は、環境変数、コマンドライン引数、設定ファイルなど、動作を設定するいくつかの方法を提供しています。このドキュメントでは、さまざまな設定方法と利用可能な設定について説明します。

## 設定レイヤー

設定は以下の優先順位で適用されます（番号が大きいほど優先度が高く、低い番号の設定を上書きします）：

1.  **デフォルト値:** アプリケーション内にハードコードされたデフォルト値。
2.  **ユーザー設定ファイル:** 現在のユーザーのグローバル設定。
3.  **プロジェクト設定ファイル:** プロジェクト固有の設定。
4.  **システム設定ファイル:** システム全体の設定。
5.  **環境変数:** システム全体またはセッション固有の変数（`.env` ファイルから読み込まれる場合もある）。
6.  **コマンドライン引数:** CLI 起動時に渡される値。

## 設定ファイル

Gemini CLI は、永続的な設定に `settings.json` ファイルを使用します。これらのファイルには3つの場所があります：

- **ユーザー設定ファイル:**
  - **場所:** `~/.gemini/settings.json` （`~` はホームディレクトリ）。
  - **スコープ:** 現在のユーザーのすべての Gemini CLI セッションに適用されます。
- **プロジェクト設定ファイル:**
  - **場所:** プロジェクトのルートディレクトリ内の `.gemini/settings.json`。
  - **スコープ:** その特定のプロジェクトから Gemini CLI を実行する場合にのみ適用されます。プロジェクト設定はユーザー設定を上書きします。
- **システム設定ファイル:**
  - **場所:** `/etc/gemini-cli/settings.json` (Linux)、`C:\ProgramData\gemini-cli\settings.json` (Windows)、または `/Library/Application Support/GeminiCli/settings.json` (macOS)。パスは `GEMINI_CLI_SYSTEM_SETTINGS_PATH` 環境変数を使用してオーバーライドできます。
  - **スコープ:** システム上の全ユーザーのすべての Gemini CLI セッションに適用されます。システム設定はユーザー設定およびプロジェクト設定を上書きします。企業のシステム管理者がユーザーの Gemini CLI 設定を制御する場合に便利です。

**設定内の環境変数に関する注意:** `settings.json` ファイル内の文字列値は、`$VAR_NAME` または `${VAR_NAME}` 構文を使用して環境変数を参照できます。これらの変数は、設定が読み込まれるときに自動的に解決されます。たとえば、環境変数 `MY_API_TOKEN` がある場合、`settings.json` で `"apiKey": "$MY_API_TOKEN"` のように使用できます。

### プロジェクト内の `.gemini` ディレクトリ

プロジェクト設定ファイルに加えて、プロジェクトの `.gemini` ディレクトリには、Gemini CLI の操作に関連する他のプロジェクト固有のファイルを含めることができます。例：

- [カスタムサンドボックスプロファイル](#sandboxing) (例: `.gemini/sandbox-macos-custom.sb`, `.gemini/sandbox.Dockerfile`)

### `settings.json` で利用可能な設定:

- **`contextFileName`** (文字列 または 文字列の配列):
  - **説明:** コンテキストファイル（例: `GEMINI.md`, `AGENTS.md`）のファイル名を指定します。単一のファイル名、または受け入れるファイル名のリストを指定できます。
  - **デフォルト:** `GEMINI.md`
  - **例:** `"contextFileName": "AGENTS.md"`

- **`bugCommand`** (オブジェクト):
  - **説明:** `/bug` コマンドのデフォルト URL を上書きします。
  - **デフォルト:**
    `"urlTemplate": "https://github.com/google-gemini/gemini-cli/issues/new?template=bug_report.yml&title={title}&info={info}"`
  - **プロパティ:**
    - **`urlTemplate`** (文字列): `{title}` および `{info}` プレースホルダーを含むことができる URL。
  - **例:**
    ```json
    "bugCommand": {
      "urlTemplate": "https://bug.example.com/new?title={title}&info={info}"
    }
    ```

- **`fileFiltering`** (オブジェクト):
  - **説明:** @ コマンドおよびファイル検出ツールの Git 対応ファイルフィルタリング動作を制御します。
  - **デフォルト:** `"respectGitIgnore": true, "enableRecursiveFileSearch": true`
  - **プロパティ:**
    - **`respectGitIgnore`** (ブール値): ファイルを検出する際に .gitignore パターンを尊重するかどうか。`true` に設定すると、git で無視されるファイル（`node_modules/`、`dist/`、`.env` など）は @ コマンドおよびファイルリスト操作から自動的に除外されます。
    - **`enableRecursiveFileSearch`** (ブール値): プロンプトで @ プレフィックスを補完する際、現在のツリー以下のファイル名を再帰的に検索するかどうか。
  - **例:**
    ```json
    "fileFiltering": {
      "respectGitIgnore": true,
      "enableRecursiveFileSearch": false
    }
    ```

- **`coreTools`** (文字列の配列):
  - **説明:** モデルで利用可能にするコアツールの名前のリストを指定できます。これにより、組み込みツールのセットを制限できます。コアツールのリストについては、[組み込みツール](../core/tools-api.md#built-in-tools)を参照してください。`ShellTool` のようにサポートされているツールについては、コマンド固有の制限を指定することもできます。たとえば、`"coreTools": ["ShellTool(ls -l)"]` は、`ls -l` コマンドのみの実行を許可します。
  - **デフォルト:** Gemini モデルで使用可能なすべてのツール。
  - **例:** `"coreTools": ["ReadFileTool", "GlobTool", "ShellTool(ls)"]`

- **`excludeTools`** (文字列の配列):
  - **説明:** モデルから除外するコアツールの名前のリストを指定できます。`excludeTools` と `coreTools` の両方にリストされているツールは除外されます。`ShellTool` のようにサポートされているツールについては、コマンド固有の制限を指定することもできます。たとえば、`"excludeTools": ["ShellTool(rm -rf)"]` は、`rm -rf` コマンドをブロックします。
  - **デフォルト**: 除外されるツールはありません。
  - **例:** `"excludeTools": ["run_shell_command", "findFiles"]`
  - **セキュリティに関する注意:** `run_shell_command` に対する `excludeTools` のコマンド固有の制限は、単純な文字列マッチングに基づいており、簡単に回避できます。この機能は**セキュリティメカニズムではなく**、信頼できないコードを安全に実行するために信頼すべきではありません。実行可能なコマンドを明示的に選択するには、`coreTools` を使用することをお勧めします。

- **`allowMCPServers`** (文字列の配列):
  - **説明:** モデルで利用可能にする MCP サーバー名のリストを指定できます。これにより、接続する MCP サーバーのセットを制限できます。`--allowed-mcp-server-names` が設定されている場合、これは無視されることに注意してください。
  - **デフォルト:** Gemini モデルですべての MCP サーバーが使用可能です。
  - **例:** `"allowMCPServers": ["myPythonServer"]`
  - **セキュリティに関する注意:** これは MCP サーバー名の単純な文字列マッチングを使用しており、変更される可能性があります。ユーザーがこれを回避するのを防ぎたいシステム管理者の場合は、システム設定レベルで `mcpServers` を構成して、ユーザーが独自の MCP サーバーを構成できないようにすることを検討してください。これは完全なセキュリティメカニズムとして使用すべきではありません。

- **`excludeMCPServers`** (文字列の配列):
  - **説明:** モデルから除外する MCP サーバー名のリストを指定できます。`excludeMCPServers` と `allowMCPServers` の両方にリストされているサーバーは除外されます。`--allowed-mcp-server-names` が設定されている場合、これは無視されることに注意してください。
  - **デフォルト**: 除外される MCP サーバーはありません。
  - **例:** `"excludeMCPServers": ["myNodeServer"]`
  - **セキュリティに関する注意:** これは MCP サーバー名の単純な文字列マッチングを使用しており、変更される可能性があります。ユーザーがこれを回避するのを防ぎたいシステム管理者の場合は、システム設定レベルで `mcpServers` を構成して、ユーザーが独自の MCP サーバーを構成できないようにすることを検討してください。これは完全なセキュリティメカニズムとして使用すべきではありません。

- **`autoAccept`** (ブール値):
  - **説明:** 安全と見なされるツール呼び出し（例：読み取り専用操作）を、ユーザーの明示的な確認なしに CLI が自動的に受け入れて実行するかどうかを制御します。`true` に設定すると、CLI は安全と見なされるツールの確認プロンプトをバイパスします。
  - **デフォルト:** `false`
  - **例:** `"autoAccept": true`

- **`theme`** (文字列):
  - **説明:** Gemini CLI の視覚[テーマ](./themes.md)を設定します。
  - **デフォルト:** `"Default"`
  - **例:** `"theme": "GitHub"`

- **`vimMode`** (ブール値):
  - **説明:** 入力編集の Vim モードを有効または無効にします。有効にすると、入力領域は NORMAL モードと INSERT モードを持つ Vim スタイルのナビゲーションおよび編集コマンドをサポートします。Vim モードの状態はフッターに表示され、セッション間で保持されます。
  - **デフォルト:** `false`
  - **例:** `"vimMode": true`

- **`sandbox`** (ブール値 または 文字列):
  - **説明:** ツール実行のためのサンドボックスの使用有無と方法を制御します。`true` に設定すると、Gemini CLI は事前にビルドされた `gemini-cli-sandbox` Docker イメージを使用します。詳細については、[サンドボックス](#sandboxing)を参照してください。
  - **デフォルト:** `false`
  - **例:** `"sandbox": "docker"`

- **`toolDiscoveryCommand`** (文字列):
  - **説明:** プロジェクトからツールを検出するためのカスタムシェルコマンドを定義します。シェルコマンドは、[関数宣言](https://ai.google.dev/gemini-api/docs/function-calling#function-declarations)の JSON 配列を `stdout` に返す必要があります。ツールラッパーはオプションです。
  - **デフォルト:** 空
  - **例:** `"toolDiscoveryCommand": "bin/get_tools"`

- **`toolCallCommand`** (文字列):
  - **説明:** `toolDiscoveryCommand` を使用して検出された特定のツールを呼び出すためのカスタムシェルコマンドを定義します。シェルコマンドは次の基準を満たす必要があります：
    - 最初のコマンドライン引数として関数 `name`（[関数宣言](https://ai.google.dev/gemini-api/docs/function-calling#function-declarations)と完全に同じもの）を取る必要があります。
    - [`functionCall.args`](https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/inference#functioncall) と同様に、`stdin` で関数の引数を JSON として読み取る必要があります。
    - [`functionResponse.response.content`](https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/inference#functionresponse) と同様に、`stdout` で関数の出力を JSON として返す必要があります。
  - **デフォルト:** 空
  - **例:** `"toolCallCommand": "bin/call_tool"`

- **`mcpServers`** (オブジェクト):
  - **説明:** カスタムツールの検出と使用のために、1つ以上の Model-Context Protocol (MCP) サーバーへの接続を設定します。Gemini CLI は、設定された各 MCP サーバーに接続して、利用可能なツールを検出しようとします。複数の MCP サーバーが同じ名前のツールを公開している場合、競合を避けるために、設定で定義したサーバーエイリアス（例: `serverAlias__actualToolName`）がツール名のプレフィックスとして付けられます。互換性のために、システムが MCP ツール定義から特定のスキーマプロパティを削除する場合があることに注意してください。
  - **デフォルト:** 空
  - **プロパティ:**
    - **`<SERVER_NAME>`** (オブジェクト): 名前付きサーバーのサーバーパラメータ。
      - `command` (文字列, 必須): MCP サーバーを起動するために実行するコマンド。
      - `args` (文字列の配列, オプション): コマンドに渡す引数。
      - `env` (オブジェクト, オプション): サーバープロセスに設定する環境変数。
      - `cwd` (文字列, オプション): サーバーを起動する作業ディレクトリ。
      - `timeout` (数値, オプション): この MCP サーバーへのリクエストのタイムアウト（ミリ秒）。
      - `trust` (ブール値, オプション): このサーバーを信頼し、すべてのツール呼び出し確認をバイパスします。
      - `includeTools` (文字列の配列, オプション): この MCP サーバーから含めるツール名のリスト。指定された場合、ここにリストされたツールのみがこのサーバーから利用可能になります（ホワイトリスト動作）。指定されていない場合、サーバーのすべてのツールがデフォルトで有効になります。
      - `excludeTools` (文字列の配列, オプション): この MCP サーバーから除外するツール名のリスト。ここにリストされたツールは、サーバーによって公開されていても、モデルでは利用できません。**注意:** `excludeTools` は `includeTools` よりも優先されます。ツールが両方のリストにある場合、それは除外されます。
  - **例:**
    ```json
    "mcpServers": {
      "myPythonServer": {
        "command": "python",
        "args": ["mcp_server.py", "--port", "8080"],
        "cwd": "./mcp_tools/python",
        "timeout": 5000,
        "includeTools": ["safe_tool", "file_reader"],
      },
      "myNodeServer": {
        "command": "node",
        "args": ["mcp_server.js"],
        "cwd": "./mcp_tools/node",
        "excludeTools": ["dangerous_tool", "file_deleter"]
      },
      "myDockerServer": {
        "command": "docker",
        "args": ["run", "-i", "--rm", "-e", "API_KEY", "ghcr.io/foo/bar"],
        "env": {
          "API_KEY": "$MY_API_TOKEN"
        }
      }
    }
    ```

- **`checkpointing`** (オブジェクト):
  - **説明:** チェックポイント機能を設定します。これにより、会話とファイルの状態を保存および復元できます。詳細は、[チェックポイント機能のドキュメント](./checkpointing.md)を参照してください。
  - **デフォルト:** `{"enabled": false}`
  - **プロパティ:**
    - **`enabled`** (ブール値): `true` の場合、`/restore` コマンドが利用可能になります。

- **`preferredEditor`** (文字列):
  - **説明:** 差分の表示に使用する推奨エディタを指定します。
  - **デフォルト:** `vscode`
  - **例:** `"preferredEditor": "vscode"`

- **`telemetry`** (オブジェクト)
  - **説明:** Gemini CLI のログとメトリックの収集を設定します。詳細については、[テレメトリ](./telemetry.md)を参照してください。
  - **デフォルト:**
    `{"enabled": false, "target": "local", "otlpEndpoint": "http://localhost:4317", "logPrompts": true}`
  - **プロパティ:**
    - **`enabled`** (ブール値): テレメトリが有効かどうか。
    - **`target`** (文字列): 収集されたテレメトリの送信先。サポートされている値は `local` と `gcp` です。
    - **`otlpEndpoint`** (文字列): OTLP エクスポーターのエンドポイント。
    - **`logPrompts`** (ブール値): ユーザープロンプトの内容をログに含めるかどうか。
  - **例:**
    ```json
    "telemetry": {
      "enabled": true,
      "target": "local",
      "otlpEndpoint": "http://localhost:16686",
      "logPrompts": false
    }
    ```
- **`usageStatisticsEnabled`** (ブール値):
  - **説明:** 使用統計の収集を有効または無効にします。詳細については、[使用統計](#usage-statistics)を参照してください。
  - **デフォルト:** `true`
  - **例:**
    ```json
    "usageStatisticsEnabled": false
    ```

- **`hideTips`** (ブール値):
  - **説明:** CLI インターフェイス内の役立つヒントを有効または無効にします。
  - **デフォルト:** `false`
  - **例:**

    ```json
    "hideTips": true
    ```

- **`hideBanner`** (ブール値):
  - **説明:** CLI インターフェイス内の起動バナー（アスキーアートロゴ）を有効または無効にします。
  - **デフォルト:** `false`
  - **例:**

    ```json
    "hideBanner": true
    ```

- **`maxSessionTurns`** (数値):
  - **説明:** セッションの最大ターン数を設定します。セッションがこの制限を超えると、CLI は処理を停止し、新しいチャットを開始します。
  - **デフォルト:** `-1` (無制限)
  - **例:**
    ```json
    "maxSessionTurns": 10
    ```

- **`summarizeToolOutput`** (オブジェクト):
  - **説明:** ツール出力の要約を有効または無効にします。`tokenBudget` 設定を使用して、要約のトークン予算を指定できます。
  - 注: 現在、`run_shell_command` ツールのみがサポートされています。
  - **デフォルト:** `{}` (デフォルトで無効)
  - **例:**
    ```json
    "summarizeToolOutput": {
      "run_shell_command": {
        "tokenBudget": 2000
      }
    }
    ```

- **`excludedProjectEnvVars`** (文字列の配列):
  - **説明:** プロジェクトの `.env` ファイルからの読み込みから除外する環境変数を指定します。これにより、プロジェクト固有の環境変数（`DEBUG=true` など）が gemini-cli の動作に干渉するのを防ぎます。`.gemini/.env` ファイルからの変数は決して除外されません。
  - **デフォルト:** `["DEBUG", "DEBUG_MODE"]`
  - **例:**
    ```json
    "excludedProjectEnvVars": ["DEBUG", "DEBUG_MODE", "NODE_ENV"]
    ```

- **`includeDirectories`** (文字列の配列):
  - **説明:** ワークスペースコンテキストに含める追加の絶対パスまたは相対パスの配列を指定します。これにより、複数のディレクトリにまたがるファイルをあたかも1つのディレクトリにあるかのように扱うことができます。パスにはユーザーのホームディレクトリを参照するために `~` を使用できます。この設定は、`--include-directories` コマンドラインフラグと組み合わせることができます。
  - **デフォルト:** `[]`
  - **例:**
    ```json
    "includeDirectories": [
      "/path/to/another/project",
      "../shared-library",
      "~/common-utils"
    ]
    ```

- **`loadMemoryFromIncludeDirectories`** (ブール値):
  - **説明:** `/memory refresh` コマンドの動作を制御します。`true` に設定した場合、追加されたすべてのディレクトリから `GEMINI.md` ファイルを読み込みます。`false` に設定した場合、`GEMINI.md` は現在のディレクトリからのみ読み込まれます。
  - **デフォルト:** `false`
  - **例:**
    ```json
    "loadMemoryFromIncludeDirectories": true
    ```

### `settings.json` の例:

```json
{
  "theme": "GitHub",
  "sandbox": "docker",
  "toolDiscoveryCommand": "bin/get_tools",
  "toolCallCommand": "bin/call_tool",
  "mcpServers": {
    "mainServer": {
      "command": "bin/mcp_server.py"
    },
    "anotherServer": {
      "command": "node",
      "args": ["mcp_server.js", "--verbose"]
    }
  },
  "telemetry": {
    "enabled": true,
    "target": "local",
    "otlpEndpoint": "http://localhost:4317",
    "logPrompts": true
  },
  "usageStatisticsEnabled": true,
  "hideTips": false,
  "hideBanner": false,
  "maxSessionTurns": 10,
  "summarizeToolOutput": {
    "run_shell_command": {
      "tokenBudget": 100
    }
  },
  "excludedProjectEnvVars": ["DEBUG", "DEBUG_MODE", "NODE_ENV"],
  "includeDirectories": ["path/to/dir1", "~/path/to/dir2", "../path/to/dir3"],
  "loadMemoryFromIncludeDirectories": true
}
```

## シェル履歴

CLI は、実行したシェルコマンドの履歴を保持します。異なるプロジェクト間での競合を避けるために、この履歴はユーザーのホームフォルダー内のプロジェクト固有のディレクトリに保存されます。

- **場所:** `~/.gemini/tmp/<project_hash>/shell_history`
  - `<project_hash>` は、プロジェクトのルートパスから生成される一意の識別子です。
  - 履歴は `shell_history` という名前のファイルに保存されます。

## 環境変数と `.env` ファイル

環境変数は、特に API キーのような機密情報や、環境間で変更される可能性のある設定において、アプリケーションを設定する一般的な方法です。

CLI は自動的に `.env` ファイルから環境変数を読み込みます。読み込み順序は次のとおりです：

1.  現在の作業ディレクトリにある `.env` ファイル。
2.  見つからない場合、`.env` ファイルが見つかるか、プロジェクトルート（`.git` フォルダーで識別）またはホームディレクトリに到達するまで、親ディレクトリを上方に検索します。
3.  それでも見つからない場合は、`~/.env`（ユーザーのホームディレクトリ）を探します。

**環境変数の除外:** 一部の環境変数（`DEBUG` や `DEBUG_MODE` など）は、gemini-cli の動作への干渉を防ぐために、プロジェクトの `.env` ファイルからの読み込みから自動的に除外されます。`.gemini/.env` ファイルからの変数は決して除外されません。この動作は、`settings.json` ファイルの `excludedProjectEnvVars` 設定を使用してカスタマイズできます。

- **`GEMINI_API_KEY`** (必須):
  - Gemini API の API キー。
  - **動作に不可欠です。** CLI はこれがないと機能しません。
  - シェルプロファイル（例: `~/.bashrc`, `~/.zshrc`）または `.env` ファイルで設定してください。
- **`GEMINI_MODEL`**:
  - 使用するデフォルトの Gemini モデルを指定します。
  - ハードコードされたデフォルトを上書きします。
  - 例: `export GEMINI_MODEL="gemini-2.5-flash"`
- **`GEMINI_CLI_CUSTOM_HEADERS`**:
  - Gemini API および Code Assist リクエストに追加の HTTP ヘッダーを追加します。
  - カンマ区切りの `Name: value` ペアを受け入れます。
  - 例:
    `export GEMINI_CLI_CUSTOM_HEADERS="X-My-Header: foo, X-Trace-ID: abc123"`
- **`GEMINI_API_KEY_AUTH_MECHANISM`**:
  - `AuthType.USE_GEMINI` または `AuthType.USE_VERTEX_AI` を使用するときに、認証のために API キーをどのように送信するかを指定します。
  - 有効な値は `x-goog-api-key` (デフォルト) または `bearer` です。
  - `bearer` に設定すると、API キーは `Authorization: Bearer <key>` ヘッダーで送信されます。
  - 例: `export GEMINI_API_KEY_AUTH_MECHANISM="bearer"`
- **`GOOGLE_API_KEY`**:
  - Google Cloud API キー。
  - エクスプレスモードで Vertex AI を使用するために必要です。
  - 必要な権限があることを確認してください。
  - 例: `export GOOGLE_API_KEY="YOUR_GOOGLE_API_KEY"`
- **`GOOGLE_CLOUD_PROJECT`**:
  - Google Cloud プロジェクト ID。
  - Code Assist または Vertex AI を使用するために必要です。
  - Vertex AI を使用する場合、このプロジェクトに必要な権限があることを確認してください。
  - **Cloud Shell に関する注意:** Cloud Shell 環境で実行する場合、この変数はデフォルトで Cloud Shell ユーザーに割り当てられた特別なプロジェクトになります。Cloud Shell のグローバル環境で `GOOGLE_CLOUD_PROJECT` を設定している場合、このデフォルトによって上書きされます。Cloud Shell で別のプロジェクトを使用するには、`.env` ファイルで `GOOGLE_CLOUD_PROJECT` を定義する必要があります。
  - 例: `export GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"`
- **`GOOGLE_APPLICATION_CREDENTIALS`** (文字列):
  - **説明:** Google Application Credentials JSON ファイルへのパス。
  - **例:**
    `export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/credentials.json"`
- **`OTLP_GOOGLE_CLOUD_PROJECT`**:
  - Google Cloud でのテレメトリ用の Google Cloud プロジェクト ID。
  - 例: `export OTLP_GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"`
- **`GOOGLE_CLOUD_LOCATION`**:
  - Google Cloud プロジェクトのロケーション（例: us-central1）。
  - 非エクスプレスモードで Vertex AI を使用するために必要です。
  - 例: `export GOOGLE_CLOUD_LOCATION="YOUR_PROJECT_LOCATION"`
- **`GEMINI_SANDBOX`**:
  - `settings.json` の `sandbox` 設定の代替。
  - `true`、`false`、`docker`、`podman`、またはカスタムコマンド文字列を受け入れます。
- **`HTTP_PROXY` / `HTTPS_PROXY`**:
  - 発信 HTTP/HTTPS リクエストに使用するプロキシサーバーを指定します。
  - 例: `export HTTPS_PROXY="http://proxy.example.com:8080"`
- **`SEATBELT_PROFILE`** (macOS 固有):
  - macOS 上の Seatbelt (`sandbox-exec`) プロファイルを切り替えます。
  - `permissive-open`: (デフォルト) プロジェクトフォルダー（およびいくつかの他のフォルダー、`packages/cli/src/utils/sandbox-macos-permissive-open.sb` を参照）への書き込みを制限しますが、他の操作は許可します。
  - `strict`: デフォルトで操作を拒否する厳格なプロファイルを使用します。
  - `<profile_name>`: カスタムプロファイルを使用します。カスタムプロファイルを定義するには、プロジェクトの `.gemini/` ディレクトリに `sandbox-macos-<profile_name>.sb` という名前のファイルを作成します（例: `my-project/.gemini/sandbox-macos-custom.sb`）。
- **`DEBUG` または `DEBUG_MODE`** (基になるライブラリや CLI 自体で頻繁に使用されます):
  - `true` または `1` に設定すると、詳細なデバッグログが有効になり、トラブルシューティングに役立ちます。
  - **注意:** これらの変数は、gemini-cli の動作への干渉を防ぐため、デフォルトでプロジェクトの `.env` ファイルから自動的に除外されます。gemini-cli 専用にこれらを設定する必要がある場合は、`.gemini/.env` ファイルを使用してください。
- **`NO_COLOR`**:
  - 任意の値を設定すると、CLI のすべてのカラー出力を無効にします。
- **`CLI_TITLE`**:
  - 文字列を設定して、CLI のタイトルをカスタマイズします。
- **`CODE_ASSIST_ENDPOINT`**:
  - Code Assist サーバーのエンドポイントを指定します。
  - 開発やテストに役立ちます。
- **`GEMINI_SYSTEM_MD`**:
  - ベースのシステムプロンプトを Markdown ファイルの内容で上書きします。
  - `1` または `true` に設定すると、`.gemini/system.md` のファイルを使用します。
  - ファイルパスに設定すると、そのファイルを使用します。パスは絶対パスまたは相対パスを使用できます。ホームディレクトリの `~` もサポートされています。
  - 指定されたファイルが存在する必要があります。
- **`GEMINI_WRITE_SYSTEM_MD`**:
  - デフォルトのシステムプロンプトをファイルに書き込みます。カスタマイズするためのテンプレートを取得するのに役立ちます。
  - `1` または `true` に設定すると、`.gemini/system.md` に書き込みます。
  - ファイルパスに設定すると、そのパスに書き込みます。パスは絶対パスまたは相対パスを使用できます。ホームディレクトリの `~` もサポートされています。**注意: ファイルがすでに存在する場合は上書きされます。**

## コマンドライン引数

CLI の実行時に直接渡される引数は、その特定のセッションの他の設定を上書きできます。

- **`--model <model_name>`** (**`-m <model_name>`**):
  - このセッションで使用する Gemini モデルを指定します。
  - 例: `npm start -- --model gemini-1.5-pro-latest`
- **`--prompt <your_prompt>`** (**`-p <your_prompt>`**):
  - コマンドに直接プロンプトを渡すために使用します。これにより、Gemini CLI が非対話モードで呼び出されます。
- **`--prompt-interactive <your_prompt>`** (**`-i <your_prompt>`**):
  - 提供されたプロンプトを初期入力として対話型セッションを開始します。
  - プロンプトは、対話型セッションの前ではなく、セッション内で処理されます。
  - stdin からの入力をパイプする場合は使用できません。
  - 例: `gemini -i "explain this code"`
- **`--sandbox`** (**`-s`**):
  - このセッションのサンドボックスモードを有効にします。
- **`--sandbox-image`**:
  - サンドボックスイメージの URI を設定します。
- **`--debug`** (**`-d`**):
  - このセッションのデバッグモードを有効にし、より詳細な出力を提供します。
- **`--all-files`** (**`-a`**):
  - 設定されている場合、現在のディレクトリ内のすべてのファイルをプロンプトのコンテキストとして再帰的に含めます。
- **`--help`** (または **`-h`**):
  - コマンドライン引数に関するヘルプ情報を表示します。
- **`--show-memory-usage`**:
  - 現在のメモリ使用量を表示します。
- **`--yolo`**:
  - YOLO モードを有効にします。これにより、すべてのツール呼び出しが自動的に承認されます。
- **`--telemetry`**:
  - [テレメトリ](./telemetry.md)を有効にします。
- **`--telemetry-target`**:
  - テレメトリターゲットを設定します。詳細については、[テレメトリ](./telemetry.md)を参照してください。
- **`--telemetry-otlp-endpoint`**:
  - テレメトリ用の OTLP エンドポイントを設定します。詳細については、[テレメトリ](./telemetry.md)を参照してください。
- **`--telemetry-log-prompts`**:
  - テレメトリ用のプロンプトのログ記録を有効にします。詳細については、[テレメトリ](./telemetry.md)を参照してください。
- **`--extensions <extension_name ...>`** (**`-e <extension_name ...>`**):
  - セッションに使用する拡張機能のリストを指定します。指定しない場合、利用可能なすべての拡張機能が使用されます。
  - 特別な用語 `gemini -e none` を使用して、すべての拡張機能を無効にします。
  - 例: `gemini -e my-extension -e my-other-extension`
- **`--list-extensions`** (**`-l`**):
  - 利用可能なすべての拡張機能を一覧表示して終了します。
- **`--include-directories <dir1,dir2,...>`**:
  - マルチディレクトリサポートのために、ワークスペースに追加のディレクトリを含めます。
  - 複数回指定するか、カンマ区切り値として指定できます。
  - 最大5つのディレクトリを追加できます。
  - 例: `--include-directories /path/to/project1,/path/to/project2` または
    `--include-directories /path/to/project1 --include-directories /path/to/project2`
- **`--version`**:
  - CLI のバージョンを表示します。

## コンテキストファイル（階層的な指示コンテキスト）

CLI の「動作」に対する厳密な設定ではありませんが、コンテキストファイル（デフォルトは `GEMINI.md` ですが、`contextFileName` 設定で設定可能）は、Gemini モデルに提供される「指示コンテキスト」（「メモリ」とも呼ばれます）を設定するために重要です。この強力な機能により、プロジェクト固有の指示、コーディングスタイルガイド、または関連する背景情報を AI に提供し、応答をニーズに合わせてより正確に調整できます。CLI には、読み込まれたコンテキストファイルの数を表示するフッターのインジケーターなど、アクティブなコンテキストに関する情報を提供する UI 要素が含まれています。

- **目的:** これらの Markdown ファイルには、対話中に Gemini モデルに認識させたい指示、ガイドライン、またはコンテキストが含まれています。システムは、この指示コンテキストを階層的に管理するように設計されています。

### コンテキストファイルのコンテンツ例（例：`GEMINI.md`）

TypeScript プロジェクトのルートにあるコンテキストファイルの内容の概念的な例を次に示します：

```markdown
# Project: My Awesome TypeScript Library

## General Instructions:

- When generating new TypeScript code, please follow the existing coding style.
- Ensure all new functions and classes have JSDoc comments.
- Prefer functional programming paradigms where appropriate.
- All code should be compatible with TypeScript 5.0 and Node.js 20+.

## Coding Style:

- Use 2 spaces for indentation.
- Interface names should be prefixed with `I` (e.g., `IUserService`).
- Private class members should be prefixed with an underscore (`_`).
- Always use strict equality (`===` and `!==`).

## Specific Component: `src/api/client.ts`

- This file handles all outbound API requests.
- When adding new API call functions, ensure they include robust error handling
  and logging.
- Use the existing `fetchWithRetry` utility for all GET requests.

## Regarding Dependencies:

- Avoid introducing new external dependencies unless absolutely necessary.
- If a new dependency is required, please state the reason.
```

この例は、一般的なプロジェクトコンテキスト、特定のコーディング規約、さらには特定のファイルやコンポーネントに関するメモを提供する方法を示しています。コンテキストファイルが適切で正確であるほど、AI はより適切に支援できます。規約とコンテキストを確立するために、プロジェクト固有のコンテキストファイルを作成することを強くお勧めします。

- **階層的な読み込みと優先順位:** CLI は、いくつかの場所からコンテキストファイル（例：`GEMINI.md`）を読み込むことにより、高度な階層メモリシステムを実装しています。このリストの下位（より具体的）にあるファイルの内容は、通常、上位（より一般的）にあるファイルの内容を上書きまたは補足します。正確な連結順序と最終的なコンテキストは、`/memory show` コマンドを使用して確認できます。一般的な読み込み順序は次のとおりです：
  1.  **グローバルコンテキストファイル:**
      - 場所: `~/.gemini/<contextFileName>` （例: ユーザーのホームディレクトリにある `~/.gemini/GEMINI.md`）。
      - スコープ: すべてのプロジェクトにデフォルトの指示を提供します。
  2.  **プロジェクトルートおよび祖先のコンテキストファイル:**
      - 場所: CLI は、設定されたコンテキストファイルを現在の作業ディレクトリで検索し、次に各親ディレクトリをプロジェクトルート（`.git` フォルダーで識別）またはホームディレクトリまで検索します。
      - スコープ: プロジェクト全体またはその大部分に関連するコンテキストを提供します。
  3.  **サブディレクトリコンテキストファイル（コンテキスト/ローカル）:**
      - 場所: CLI は、現在の作業ディレクトリより*下*のサブディレクトリにある設定されたコンテキストファイルもスキャンします（`node_modules`、`.git` などの一般的な無視パターンを尊重します）。この検索の幅はデフォルトで200ディレクトリに制限されていますが、`settings.json` ファイルの `memoryDiscoveryMaxDirs` フィールドで設定できます。
      - スコープ: プロジェクトの特定のコンポーネント、モジュール、またはサブセクションに関連する非常に具体的な指示を可能にします。
- **連結と UI 表示:** 見つかったすべてのコンテキストファイルの内容は連結され（その起源とパスを示す区切り文字付き）、システムプロンプトの一部として Gemini モデルに提供されます。CLI フッターには読み込まれたコンテキストファイルの数が表示され、アクティブな指示コンテキストに関する視覚的な手がかりがすぐに得られます。
- **コンテンツのインポート:** `@path/to/file.md` 構文を使用して他の Markdown ファイルをインポートすることで、コンテキストファイルをモジュール化できます。詳細については、[メモリインポートプロセッサのドキュメント](../core/memport.md)を参照してください。
- **メモリ管理用コマンド:**
  - `/memory refresh` を使用して、設定されたすべての場所からすべてのコンテキストファイルを強制的に再スキャンおよび再読み込みします。これにより、AI の指示コンテキストが更新されます。
  - `/memory show` を使用して、現在読み込まれている結合された指示コンテキストを表示し、AI が使用している階層とコンテンツを確認できます。
  - `/memory` コマンドとそのサブコマンド（`show` および `refresh`）の詳細については、[コマンドドキュメント](./commands.md#memory)を参照してください。

これらの設定レイヤーとコンテキストファイルの階層的な性質を理解して活用することで、AI のメモリを効果的に管理し、Gemini CLI の応答を特定のニーズやプロジェクトに合わせて調整できます。

## サンドボックス

Gemini CLI は、システムを保護するために、サンドボックス環境内で潜在的に安全でない操作（シェルコマンドやファイルの変更など）を実行できます。

サンドボックスはデフォルトで無効になっていますが、いくつかの方法で有効にできます：

- `--sandbox` または `-s` フラグを使用する。
- `GEMINI_SANDBOX` 環境変数を設定する。
- `--yolo` モードでは、サンドボックスはデフォルトで有効になっています。

デフォルトでは、事前にビルドされた `gemini-cli-sandbox` Docker イメージを使用します。

プロジェクト固有のサンドボックスのニーズがある場合は、プロジェクトのルートディレクトリに `.gemini/sandbox.Dockerfile` でカスタム Dockerfile を作成できます。この Dockerfile は、ベースのサンドボックスイメージに基づくことができます：

```dockerfile
FROM gemini-cli-sandbox

# Add your custom dependencies or configurations here
# For example:
# RUN apt-get update && apt-get install -y some-package
# COPY ./my-config /app/my-config
```

`.gemini/sandbox.Dockerfile` が存在する場合、Gemini CLI の実行時に `BUILD_SANDBOX` 環境変数を使用して、カスタムサンドボックスイメージを自動的にビルドできます：

```bash
BUILD_SANDBOX=1 gemini -s
```

## 使用統計

Gemini CLI を改善するために、匿名化された使用統計を収集しています。このデータは、CLI がどのように使用されているかを理解し、一般的な問題を特定し、新機能の優先順位を付けるのに役立ちます。

**収集するもの:**

- **ツール呼び出し:** 呼び出されたツールの名前、成功したか失敗したか、実行にかかった時間をログに記録します。ツールに渡された引数やツールから返されたデータは収集しません。
- **API リクエスト:** 各リクエストに使用された Gemini モデル、リクエストの期間、成功したかどうかをログに記録します。プロンプトやレスポンスの内容は収集しません。
- **セッション情報:** 有効なツールや承認モードなど、CLI の構成に関する情報を収集します。

**収集しないもの:**

- **個人を特定できる情報 (PII):** 名前、メールアドレス、API キーなどの個人情報は収集しません。
- **プロンプトとレスポンスの内容:** プロンプトの内容や Gemini モデルからのレスポンスはログに記録しません。
- **ファイルの内容:** CLI によって読み書きされるファイルの内容はログに記録しません。

**オプトアウトする方法:**

`settings.json` ファイルで `usageStatisticsEnabled` プロパティを `false` に設定することで、いつでも使用統計の収集をオプトアウトできます：

```json
{
  "usageStatisticsEnabled": false
}
```