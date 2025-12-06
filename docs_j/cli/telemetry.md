# OpenTelemetry による可観測性 (Observability)

Gemini CLI で OpenTelemetry を有効にして設定する方法を学びます。

- [OpenTelemetry による可観測性](#opentelemetry-による可観測性-observability)
  - [主な利点](#主な利点)
  - [OpenTelemetry の統合](#opentelemetry-の統合)
  - [設定](#設定)
  - [Google Cloud テレメトリ](#google-cloud-テレメトリ)
    - [前提条件](#前提条件)
    - [直接エクスポート（推奨）](#直接エクスポート推奨)
    - [コレクターベースのエクスポート（上級者向け）](#コレクターベースのエクスポート上級者向け)
  - [ローカルテレメトリ](#ローカルテレメトリ)
    - [ファイルベースの出力（推奨）](#ファイルベースの出力推奨)
    - [コレクターベースのエクスポート（上級者向け）](#コレクターベースのエクスポート上級者向け-1)
  - [ログとメトリクス](#ログとメトリクス)
    - [ログ](#ログ)
      - [セッション](#セッション)
      - [ツール](#ツール)
      - [ファイル](#ファイル)
      - [API](#api)
      - [モデルルーティング](#モデルルーティング)
      - [チャットとストリーミング](#チャットとストリーミング)
      - [回復力 (Resilience)](#回復力-resilience)
      - [拡張機能](#拡張機能)
      - [エージェント実行](#エージェント実行)
      - [IDE](#ide)
      - [UI](#ui)
    - [メトリクス](#メトリクス)
      - [カスタム](#カスタム)
        - [セッション](#セッション-1)
        - [ツール](#ツール-1)
        - [API](#api-1)
        - [トークン使用量](#トークン使用量)
        - [ファイル](#ファイル-1)
        - [チャットとストリーミング](#チャットとストリーミング-1)
        - [モデルルーティング](#モデルルーティング-1)
        - [エージェント実行](#エージェント実行-1)
        - [UI](#ui-1)
        - [パフォーマンス](#パフォーマンス)
      - [GenAI セマンティック規約](#genai-セマンティック規約)

## 主な利点

- **🔍 利用分析**: チーム全体での対話パターンや機能の採用状況を把握
- **⚡ パフォーマンス監視**: 応答時間、トークン消費、リソース使用率を追跡
- **🐛 リアルタイムデバッグ**: 発生したボトルネック、障害、エラーパターンを特定
- **📊 ワークフローの最適化**: 構成やプロセスを改善するための情報に基づいた意思決定を行う
- **🏢 企業ガバナンス**: チーム間の利用状況の監視、コストの追跡、コンプライアンスの確保、既存の監視インフラとの統合

## OpenTelemetry の統合

ベンダー中立の業界標準の可観測性フレームワークである **[OpenTelemetry]** 上に構築された Gemini CLI の可観測性システムは、以下を提供します：

- **普遍的な互換性**: 任意の OpenTelemetry バックエンド（Google Cloud, Jaeger, Prometheus, Datadog など）にエクスポート可能
- **標準化されたデータ**: ツールチェーン全体で一貫した形式と収集方法を使用
- **将来を見据えた統合**: 既存および将来の可観測性インフラストラクチャと接続
- **ベンダーロックインなし**: 計装を変更することなくバックエンドを切り替え可能

[OpenTelemetry]: https://opentelemetry.io/

## 設定

すべてのテレメトリ動作は `.gemini/settings.json` ファイルで制御されます。環境変数を使用してファイル内の設定を上書きできます。

| 設定項目       | 環境変数                         | 説明                                                | 値                | デフォルト               |
| -------------- | -------------------------------- | --------------------------------------------------- | ----------------- | ----------------------- |
| `enabled`      | `GEMINI_TELEMETRY_ENABLED`       | テレメトリの有効/無効                               | `true`/`false`    | `false`                 |
| `target`       | `GEMINI_TELEMETRY_TARGET`        | テレメトリデータの送信先                            | `"gcp"`/`"local"` | `"local"`               |
| `otlpEndpoint` | `GEMINI_TELEMETRY_OTLP_ENDPOINT` | OTLP コレクターエンドポイント                       | URL 文字列        | `http://localhost:4317` |
| `otlpProtocol` | `GEMINI_TELEMETRY_OTLP_PROTOCOL` | OTLP トランスポートプロトコル                       | `"grpc"`/`"http"` | `"grpc"`                |
| `outfile`      | `GEMINI_TELEMETRY_OUTFILE`       | テレメトリをファイルに保存（`otlpEndpoint` を上書き） | ファイルパス      | -                       |
| `logPrompts`   | `GEMINI_TELEMETRY_LOG_PROMPTS`   | テレメトリログにプロンプトを含める                  | `true`/`false`    | `true`                  |
| `useCollector` | `GEMINI_TELEMETRY_USE_COLLECTOR` | 外部 OTLP コレクターを使用（上級者向け）            | `true`/`false`    | `false`                 |
| `useCliAuth`   | `GEMINI_TELEMETRY_USE_CLI_AUTH`  | テレメトリに CLI 認証情報を使用（GCP ターゲットのみ）| `true`/`false`    | `false`                 |

**ブール値の環境変数に関する注意:** ブール値の設定（`enabled`, `logPrompts`, `useCollector`）については、対応する環境変数を `true` または `1` に設定すると機能が有効になります。それ以外の値に設定すると無効になります。

すべての設定オプションの詳細については、[設定ガイド](../get-started/configuration.md)を参照してください。

## Google Cloud テレメトリ

### 前提条件

以下のいずれかの方法を使用する前に、次の手順を完了してください：

1. Google Cloud プロジェクト ID を設定する：
   - 推論とは別のプロジェクトでテレメトリを行う場合：
     ```bash
     export OTLP_GOOGLE_CLOUD_PROJECT="your-telemetry-project-id"
     ```
   - 推論と同じプロジェクトでテレメトリを行う場合：
     ```bash
     export GOOGLE_CLOUD_PROJECT="your-project-id"
     ```

2. Google Cloud で認証する：
   - ユーザーアカウントを使用する場合：
     ```bash
     gcloud auth application-default login
     ```
   - サービスアカウントを使用する場合：
     ```bash
     export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/service-account.json"
     ```
3. アカウントまたはサービスアカウントに以下の IAM ロールがあることを確認する：
   - Cloud Trace Agent
   - Monitoring Metric Writer
   - Logs Writer

4. 必要な Google Cloud API を有効にする（まだ有効になっていない場合）：
   ```bash
   gcloud services enable \
     cloudtrace.googleapis.com \
     monitoring.googleapis.com \
     logging.googleapis.com \
     --project="$OTLP_GOOGLE_CLOUD_PROJECT"
   ```

### CLI 認証情報による認証

デフォルトでは、Google Cloud 用のテレメトリコレクターは Application Default Credentials (ADC) を使用します。ただし、Gemini CLI へのログインに使用するのと同じ OAuth 認証情報を使用するように設定することもできます。これは、ADC がセットアップされていない環境で役立ちます。

これを有効にするには、`telemetry` 設定の `useCliAuth` プロパティを `true` に設定します：

```json
{
  "telemetry": {
    "enabled": true,
    "target": "gcp",
    "useCliAuth": true
  }
}
```

**重要:**

- この設定には **直接エクスポート**（プロセス内エクスポーター）の使用が必要です。
- `useCollector: true` とは**併用できません**。両方を有効にすると、テレメトリは無効になり、エラーがログに記録されます。
- CLI は自動的にあなたの認証情報を使用して、Google Cloud Trace、Metrics、Logging API で認証します。

### 直接エクスポート（推奨）

テレメトリを Google Cloud サービスに直接送信します。コレクターは不要です。

1. `.gemini/settings.json` でテレメトリを有効にする：
   ```json
   {
     "telemetry": {
       "enabled": true,
       "target": "gcp"
     }
   }
   ```
2. Gemini CLI を実行し、プロンプトを送信する。
3. ログとメトリクスを表示する：
   - プロンプト送信後、ブラウザで Google Cloud Console を開きます：
     - ログ: https://console.cloud.google.com/logs/
     - メトリクス: https://console.cloud.google.com/monitoring/metrics-explorer
     - トレース: https://console.cloud.google.com/traces/list

### コレクターベースのエクスポート（上級者向け）

カスタム処理、フィルタリング、ルーティングを行う場合は、OpenTelemetry コレクターを使用してデータを Google Cloud に転送します。

1. `.gemini/settings.json` を設定する：
   ```json
   {
     "telemetry": {
       "enabled": true,
       "target": "gcp",
       "useCollector": true
     }
   }
   ```
2. 自動化スクリプトを実行する：
   ```bash
   npm run telemetry -- --target=gcp
   ```
   これにより以下が実行されます：
   - Google Cloud に転送するローカル OTEL コレクターを開始
   - ワークスペースを設定
   - Google Cloud Console でトレース、メトリクス、ログを表示するためのリンクを提供
   - コレクターログを `~/.gemini/tmp/<projectHash>/otel/collector-gcp.log` に保存
   - 終了時にコレクターを停止（例: `Ctrl+C`）
3. Gemini CLI を実行し、プロンプトを送信する。
4. ログとメトリクスを表示する：
   - プロンプト送信後、ブラウザで Google Cloud Console を開きます：
     - ログ: https://console.cloud.google.com/logs/
     - メトリクス: https://console.cloud.google.com/monitoring/metrics-explorer
     - トレース: https://console.cloud.google.com/traces/list
   - `~/.gemini/tmp/<projectHash>/otel/collector-gcp.log` を開いてローカルコレクターのログを表示します。

## ローカルテレメトリ

ローカル開発やデバッグのために、テレメトリデータをローカルでキャプチャできます：

### ファイルベースの出力（推奨）

1. `.gemini/settings.json` でテレメトリを有効にする：
   ```json
   {
     "telemetry": {
       "enabled": true,
       "target": "local",
       "otlpEndpoint": "",
       "outfile": ".gemini/telemetry.log"
     }
   }
   ```
2. Gemini CLI を実行し、プロンプトを送信する。
3. 指定されたファイル（例: `.gemini/telemetry.log`）でログとメトリクスを表示する。

### コレクターベースのエクスポート（上級者向け）

1. 自動化スクリプトを実行する：
   ```bash
   npm run telemetry -- --target=local
   ```
   これにより以下が実行されます：
   - Jaeger と OTEL コレクターをダウンロードして開始
   - ローカルテレメトリ用にワークスペースを設定
   - http://localhost:16686 で Jaeger UI を提供
   - ログ/メトリクスを `~/.gemini/tmp/<projectHash>/otel/collector.log` に保存
   - 終了時にコレクターを停止（例: `Ctrl+C`）
2. Gemini CLI を実行し、プロンプトを送信する。
3. http://localhost:16686 でトレースを表示し、コレクターログファイルでログ/メトリクスを表示する。

## ログとメトリクス

以下のセクションでは、Gemini CLI で生成されるログとメトリクスの構造について説明します。

`session.id`, `installation.id`, `user.email`（Google アカウントで認証されている場合のみ利用可能）は、すべてのログとメトリクスに共通の属性として含まれます。

### ログ

ログは特定のイベントのタイムスタンプ付き記録です。Gemini CLI では以下のイベントがカテゴリ別にログに記録されます。

#### セッション

起動時の設定とユーザープロンプトの送信をキャプチャします。

- `gemini_cli.config`: 起動時に一度、CLI 設定とともに出力されます。
  - **属性**:
    - `model` (文字列)
    - `embedding_model` (文字列)
    - `sandbox_enabled` (ブール値)
    - `core_tools_enabled` (文字列)
    - `approval_mode` (文字列)
    - `api_key_enabled` (ブール値)
    - `vertex_ai_enabled` (ブール値)
    - `log_user_prompts_enabled` (ブール値)
    - `file_filtering_respect_git_ignore` (ブール値)
    - `debug_mode` (ブール値)
    - `mcp_servers` (文字列)
    - `mcp_servers_count` (整数)
    - `extensions` (文字列)
    - `extension_ids` (文字列)
    - `extension_count` (整数)
    - `mcp_tools` (文字列, 該当する場合)
    - `mcp_tools_count` (整数, 該当する場合)
    - `output_format` ("text", "json", "stream-json")

- `gemini_cli.user_prompt`: ユーザーがプロンプトを送信したときに出力されます。
  - **属性**:
    - `prompt_length` (整数)
    - `prompt_id` (文字列)
    - `prompt` (文字列; `telemetry.logPrompts` が `false` の場合は除外)
    - `auth_type` (文字列)

#### ツール

ツールの実行、出力の切り捨て、スマート編集の動作をキャプチャします。

- `gemini_cli.tool_call`: 各ツール（関数）呼び出しごとに出力されます。
  - **属性**:
    - `function_name`
    - `function_args`
    - `duration_ms`
    - `success` (ブール値)
    - `decision` ("accept", "reject", "auto_accept", "modify", 該当する場合)
    - `error` (該当する場合)
    - `error_type` (該当する場合)
    - `prompt_id` (文字列)
    - `tool_type` ("native" または "mcp")
    - `mcp_server_name` (文字列, 該当する場合)
    - `extension_name` (文字列, 該当する場合)
    - `extension_id` (文字列, 該当する場合)
    - `content_length` (整数, 該当する場合)
    - `metadata` (該当する場合)

- `gemini_cli.tool_output_truncated`: ツール呼び出しの出力が切り捨てられました。
  - **属性**:
    - `tool_name` (文字列)
    - `original_content_length` (整数)
    - `truncated_content_length` (整数)
    - `threshold` (整数)
    - `lines` (整数)
    - `prompt_id` (文字列)

- `gemini_cli.smart_edit_strategy`: スマート編集戦略が選択されました。
  - **属性**:
    - `strategy` (文字列)

- `gemini_cli.smart_edit_correction`: スマート編集の修正結果。
  - **属性**:
    - `correction` ("success" | "failure")

- `gen_ai.client.inference.operation.details`: このイベントは、[イベントに関する OpenTelemetry GenAI セマンティック規約]に準拠した、GenAI 操作に関する詳細情報を提供します。
  - **属性**:
    - `gen_ai.request.model` (文字列)
    - `gen_ai.provider.name` (文字列)
    - `gen_ai.operation.name` (文字列)
    - `gen_ai.input.messages` (json 文字列)
    - `gen_ai.output.messages` (json 文字列)
    - `gen_ai.response.finish_reasons` (文字列の配列)
    - `gen_ai.usage.input_tokens` (整数)
    - `gen_ai.usage.output_tokens` (整数)
    - `gen_ai.request.temperature` (浮動小数点数)
    - `gen_ai.request.top_p` (浮動小数点数)
    - `gen_ai.request.top_k` (整数)
    - `gen_ai.request.max_tokens` (整数)
    - `gen_ai.system_instructions` (json 文字列)
    - `server.address` (文字列)
    - `server.port` (整数)

#### ファイル

ツールによって実行されたファイル操作を追跡します。

- `gemini_cli.file_operation`: 各ファイル操作ごとに出力されます。
  - **属性**:
    - `tool_name` (文字列)
    - `operation` ("create" | "read" | "update")
    - `lines` (整数, オプション)
    - `mimetype` (文字列, オプション)
    - `extension` (文字列, オプション)
    - `programming_language` (文字列, オプション)

#### API

Gemini API のリクエスト、レスポンス、エラーをキャプチャします。

- `gemini_cli.api_request`: Gemini API に送信されたリクエスト。
  - **属性**:
    - `model` (文字列)
    - `prompt_id` (文字列)
    - `request_text` (文字列, オプション)

- `gemini_cli.api_response`: Gemini API から受信したレスポンス。
  - **属性**:
    - `model` (文字列)
    - `status_code` (整数|文字列)
    - `duration_ms` (整数)
    - `input_token_count` (整数)
    - `output_token_count` (整数)
    - `cached_content_token_count` (整数)
    - `thoughts_token_count` (整数)
    - `tool_token_count` (整数)
    - `total_token_count` (整数)
    - `response_text` (文字列, オプション)
    - `prompt_id` (文字列)
    - `auth_type` (文字列)
    - `finish_reasons` (文字列の配列)

- `gemini_cli.api_error`: API リクエストが失敗しました。
  - **属性**:
    - `model` (文字列)
    - `error` (文字列)
    - `error_type` (文字列)
    - `status_code` (整数|文字列)
    - `duration_ms` (整数)
    - `prompt_id` (文字列)
    - `auth_type` (文字列)

- `gemini_cli.malformed_json_response`: `generateJson` レスポンスを解析できませんでした。
  - **属性**:
    - `model` (文字列)

#### モデルルーティング

- `gemini_cli.slash_command`: スラッシュコマンドが実行されました。
  - **属性**:
    - `command` (文字列)
    - `subcommand` (文字列, オプション)
    - `status` ("success" | "error")

- `gemini_cli.slash_command.model`: スラッシュコマンドによってモデルが選択されました。
  - **属性**:
    - `model_name` (文字列)

- `gemini_cli.model_routing`: モデルルーターが決定を行いました。
  - **属性**:
    - `decision_model` (文字列)
    - `decision_source` (文字列)
    - `routing_latency_ms` (整数)
    - `reasoning` (文字列, オプション)
    - `failed` (ブール値)
    - `error_message` (文字列, オプション)

#### チャットとストリーミング

- `gemini_cli.chat_compression`: チャットコンテキストが圧縮されました。
  - **属性**:
    - `tokens_before` (整数)
    - `tokens_after` (整数)

- `gemini_cli.chat.invalid_chunk`: ストリームから無効なチャンクを受信しました。
  - **属性**:
    - `error.message` (文字列, オプション)

- `gemini_cli.chat.content_retry`: コンテンツエラーにより再試行がトリガーされました。
  - **属性**:
    - `attempt_number` (整数)
    - `error_type` (文字列)
    - `retry_delay_ms` (整数)
    - `model` (文字列)

- `gemini_cli.chat.content_retry_failure`: すべてのコンテンツ再試行が失敗しました。
  - **属性**:
    - `total_attempts` (整数)
    - `final_error_type` (文字列)
    - `total_duration_ms` (整数, オプション)
    - `model` (文字列)

- `gemini_cli.conversation_finished`: 会話セッションが終了しました。
  - **属性**:
    - `approvalMode` (文字列)
    - `turnCount` (整数)

- `gemini_cli.next_speaker_check`: 次の話者の決定。
  - **属性**:
    - `prompt_id` (文字列)
    - `finish_reason` (文字列)
    - `result` (文字列)

#### 回復力 (Resilience)

モデルおよびネットワーク操作のフォールバックメカニズムを記録します。

- `gemini_cli.flash_fallback`: フォールバックとして Flash モデルに切り替えました。
  - **属性**:
    - `auth_type` (文字列)

- `gemini_cli.ripgrep_fallback`: ファイル検索のフォールバックとして grep に切り替えました。
  - **属性**:
    - `error` (文字列, オプション)

- `gemini_cli.web_fetch_fallback_attempt`: Web フェッチのフォールバックを試みました。
  - **属性**:
    - `reason` ("private_ip" | "primary_failed")

#### 拡張機能

拡張機能のライフサイクルと設定の変更を追跡します。

- `gemini_cli.extension_install`: 拡張機能がインストールされました。
  - **属性**:
    - `extension_name` (文字列)
    - `extension_version` (文字列)
    - `extension_source` (文字列)
    - `status` (文字列)

- `gemini_cli.extension_uninstall`: 拡張機能がアンインストールされました。
  - **属性**:
    - `extension_name` (文字列)
    - `status` (文字列)

- `gemini_cli.extension_enable`: 拡張機能が有効になりました。
  - **属性**:
    - `extension_name` (文字列)
    - `setting_scope` (文字列)

- `gemini_cli.extension_disable`: 拡張機能が無効になりました。
  - **属性**:
    - `extension_name` (文字列)
    - `setting_scope` (文字列)

- `gemini_cli.extension_update`: 拡張機能が更新されました。
  - **属性**:
    - `extension_name` (文字列)
    - `extension_version` (文字列)
    - `extension_previous_version` (文字列)
    - `extension_source` (文字列)
    - `status` (文字列)

#### エージェント実行

- `gemini_cli.agent.start`: エージェントの実行が開始されました。
  - **属性**:
    - `agent_id` (文字列)
    - `agent_name` (文字列)

- `gemini_cli.agent.finish`: エージェントの実行が終了しました。
  - **属性**:
    - `agent_id` (文字列)
    - `agent_name` (文字列)
    - `duration_ms` (整数)
    - `turn_count` (整数)
    - `terminate_reason` (文字列)

#### IDE

IDE コンパニオン接続と会話ライフサイクルイベントをキャプチャします。

- `gemini_cli.ide_connection`: IDE コンパニオン接続。
  - **属性**:
    - `connection_type` (文字列)

#### UI

ターミナルレンダリングの問題と関連シグナルを追跡します。

- `kitty_sequence_overflow`: ターミナルの kitty 制御シーケンスオーバーフロー。
  - **属性**:
    - `sequence_length` (整数)
    - `truncated_sequence` (文字列)

### メトリクス

メトリクスは、時間の経過に伴う動作の数値的な測定値です。

#### カスタム

##### セッション

起動時の CLI セッションをカウントします。

- `gemini_cli.session.count` (カウンター, 整数): CLI 起動ごとに1回インクリメントされます。

##### ツール

ツールの使用とレイテンシを測定します。

- `gemini_cli.tool.call.count` (カウンター, 整数): ツール呼び出しをカウントします。
  - **属性**:
    - `function_name`
    - `success` (ブール値)
    - `decision` (文字列: "accept", "reject", "modify", "auto_accept", 該当する場合)
    - `tool_type` (文字列: "mcp" または "native", 該当する場合)

- `gemini_cli.tool.call.latency` (ヒストグラム, ミリ秒): ツール呼び出しのレイテンシを測定します。
  - **属性**:
    - `function_name`

##### API

API リクエスト量とレイテンシを追跡します。

- `gemini_cli.api.request.count` (カウンター, 整数): すべての API リクエストをカウントします。
  - **属性**:
    - `model`
    - `status_code`
    - `error_type` (該当する場合)

- `gemini_cli.api.request.latency` (ヒストグラム, ミリ秒): API リクエストのレイテンシを測定します。
  - **属性**:
    - `model`
  - 注: `gen_ai.client.operation.duration` (GenAI 規約) と重複します。

##### トークン使用量

モデルおよびタイプ別の使用トークンを追跡します。

- `gemini_cli.token.usage` (カウンター, 整数): 使用されたトークンをカウントします。
  - **属性**:
    - `model`
    - `type` ("input", "output", "thought", "cache", "tool")
  - 注: `input`/`output` については `gen_ai.client.token.usage` と重複します。

##### ファイル

基本的なコンテキストを含むファイル操作をカウントします。

- `gemini_cli.file.operation.count` (カウンター, 整数): ファイル操作をカウントします。
  - **属性**:
    - `operation` ("create", "read", "update")
    - `lines` (整数, オプション)
    - `mimetype` (文字列, オプション)
    - `extension` (文字列, オプション)
    - `programming_language` (文字列, オプション)

- `gemini_cli.lines.changed` (カウンター, 整数): 変更された行数（ファイルの差分から）。
  - **属性**:
    - `function_name`
    - `type` ("added" または "removed")

##### チャットとストリーミング

圧縮、無効なチャンク、再試行のための回復力カウンター。

- `gemini_cli.chat_compression` (カウンター, 整数): チャット圧縮操作をカウントします。
  - **属性**:
    - `tokens_before` (整数)
    - `tokens_after` (整数)

- `gemini_cli.chat.invalid_chunk.count` (カウンター, 整数): ストリームからの無効なチャンクをカウントします。

- `gemini_cli.chat.content_retry.count` (カウンター, 整数): コンテンツエラーによる再試行をカウントします。

- `gemini_cli.chat.content_retry_failure.count` (カウンター, 整数): すべてのコンテンツ再試行が失敗したリクエストをカウントします。

##### モデルルーティング

ルーティングのレイテンシ/失敗とスラッシュコマンドによる選択。

- `gemini_cli.slash_command.model.call_count` (カウンター, 整数): スラッシュコマンドによるモデル選択をカウントします。
  - **属性**:
    - `slash_command.model.model_name` (文字列)

- `gemini_cli.model_routing.latency` (ヒストグラム, ミリ秒): モデルルーティングの決定レイテンシ。
  - **属性**:
    - `routing.decision_model` (文字列)
    - `routing.decision_source` (文字列)

- `gemini_cli.model_routing.failure.count` (カウンター, 整数): モデルルーティングの失敗をカウントします。
  - **属性**:
    - `routing.decision_source` (文字列)
    - `routing.error_message` (文字列)

##### エージェント実行

エージェントのライフサイクルメトリクス：実行回数、期間、ターン数。

- `gemini_cli.agent.run.count` (カウンター, 整数): エージェントの実行をカウントします。
  - **属性**:
    - `agent_name` (文字列)
    - `terminate_reason` (文字列)

- `gemini_cli.agent.duration` (ヒストグラム, ミリ秒): エージェントの実行期間。
  - **属性**:
    - `agent_name` (文字列)

- `gemini_cli.agent.turns` (ヒストグラム, ターン): エージェント実行ごとのターン数。
  - **属性**:
    - `agent_name` (文字列)

##### UI

フリッカー数などの UI 安定性シグナル。

- `gemini_cli.ui.flicker.count` (カウンター, 整数): フリッカー（ターミナルよりも高くレンダリングされた）した UI フレームをカウントします。

##### パフォーマンス

起動、CPU/メモリ、フェーズタイミングのためのオプションのパフォーマンス監視。

- `gemini_cli.startup.duration` (ヒストグラム, ミリ秒): フェーズ別の CLI 起動時間。
  - **属性**:
    - `phase` (文字列)
    - `details` (マップ, オプション)

- `gemini_cli.memory.usage` (ヒストグラム, バイト): メモリ使用量。
  - **属性**:
    - `memory_type` ("heap_used", "heap_total", "external", "rss")
    - `component` (文字列, オプション)

- `gemini_cli.cpu.usage` (ヒストグラム, パーセント): CPU 使用率（パーセント）。
  - **属性**:
    - `component` (文字列, オプション)

- `gemini_cli.tool.queue.depth` (ヒストグラム, カウント): 実行キュー内のツール数。

- `gemini_cli.tool.execution.breakdown` (ヒストグラム, ミリ秒): フェーズ別のツール時間。
  - **属性**:
    - `function_name` (文字列)
    - `phase` ("validation", "preparation", "execution", "result_processing")

- `gemini_cli.api.request.breakdown` (ヒストグラム, ミリ秒): フェーズ別の API リクエスト時間。
  - **属性**:
    - `model` (文字列)
    - `phase` ("request_preparation", "network_latency", "response_processing",
      "token_processing")

- `gemini_cli.token.efficiency` (ヒストグラム, 比率): トークン効率メトリクス。
  - **属性**:
    - `model` (文字列)
    - `metric` (文字列)
    - `context` (文字列, オプション)

- `gemini_cli.performance.score` (ヒストグラム, スコア): 複合パフォーマンススコア。
  - **属性**:
    - `category` (文字列)
    - `baseline` (数値, オプション)

- `gemini_cli.performance.regression` (カウンター, 整数): 回帰検出イベント。
  - **属性**:
    - `metric` (文字列)
    - `severity` ("low", "medium", "high")
    - `current_value` (数値)
    - `baseline_value` (数値)

- `gemini_cli.performance.regression.percentage_change` (ヒストグラム, パーセント): 回帰が検出されたときのベースラインからの変化率。
  - **属性**:
    - `metric` (文字列)
    - `severity` ("low", "medium", "high")
    - `current_value` (数値)
    - `baseline_value` (数値)

- `gemini_cli.performance.baseline.comparison` (ヒストグラム, パーセント): ベースラインとの比較。
  - **属性**:
    - `metric` (文字列)
    - `category` (文字列)
    - `current_value` (数値)
    - `baseline_value` (数値)

#### GenAI セマンティック規約

以下のメトリクスは、GenAI アプリケーション全体での標準化された可観測性のための [OpenTelemetry GenAI セマンティック規約] に準拠しています：

- `gen_ai.client.token.usage` (ヒストグラム, トークン): 操作ごとに使用された入力および出力トークンの数。
  - **属性**:
    - `gen_ai.operation.name` (文字列): 操作タイプ（例: "generate_content", "chat"）
    - `gen_ai.provider.name` (文字列): GenAI プロバイダー（"gcp.gen_ai" または "gcp.vertex_ai"）
    - `gen_ai.token.type` (文字列): トークンタイプ（"input" または "output"）
    - `gen_ai.request.model` (文字列, オプション): リクエストに使用されたモデル名
    - `gen_ai.response.model` (文字列, オプション): レスポンスを生成したモデル名
    - `server.address` (文字列, オプション): GenAI サーバーアドレス
    - `server.port` (整数, オプション): GenAI サーバーポート

- `gen_ai.client.operation.duration` (ヒストグラム, 秒): GenAI 操作期間（秒）。
  - **属性**:
    - `gen_ai.operation.name` (文字列): 操作タイプ（例: "generate_content", "chat"）
    - `gen_ai.provider.name` (文字列): GenAI プロバイダー（"gcp.gen_ai" または "gcp.vertex_ai"）
    - `gen_ai.request.model` (文字列, オプション): リクエストに使用されたモデル名
    - `gen_ai.response.model` (文字列, オプション): レスポンスを生成したモデル名
    - `server.address` (文字列, オプション): GenAI サーバーアドレス
    - `server.port` (整数, オプション): GenAI サーバーポート
    - `error.type` (文字列, オプション): 操作が失敗した場合のエラータイプ

[イベントに関する OpenTelemetry GenAI セマンティック規約]:
  https://github.com/open-telemetry/semantic-conventions/blob/8b4f210f43136e57c1f6f47292eb6d38e3bf30bb/docs/gen-ai/gen-ai-events.md
[OpenTelemetry GenAI セマンティック規約]:
  https://github.com/open-telemetry/semantic-conventions/blob/main/docs/gen-ai/gen-ai-metrics.md