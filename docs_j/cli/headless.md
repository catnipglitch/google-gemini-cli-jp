# ヘッドレスモード

ヘッドレスモードでは、Gemini CLI をコマンドラインスクリプトや自動化ツールからインタラクティブな UI なしでプログラムで実行できます。これは、スクリプト作成、自動化、CI/CD パイプライン、AI 搭載ツールの構築に最適です。

- [ヘッドレスモード](#headless-mode)
  - [概要](#概要)
  - [基本的な使い方](#基本的な使い方)
    - [直接プロンプト](#直接プロンプト)
    - [標準入力](#標準入力)
    - [ファイル入力との組み合わせ](#ファイル入力との組み合わせ)
  - [出力形式](#出力形式)
    - [テキスト出力 (デフォルト)](#テキスト出力-デフォルト)
    - [JSON 出力](#json-出力)
      - [応答スキーマ](#応答スキーマ)
      - [使用例](#使用例)
    - [ストリーミング JSON 出力](#ストリーミング-json-出力)
      - [ストリーミング JSON を使用するタイミング](#ストリーミング-json-を使用するタイミング)
      - [イベントタイプ](#イベントタイプ)
      - [基本的な使い方](#基本的な使い方-1)
      - [出力例](#出力例)
      - [ストリームイベントの処理](#ストリームイベントの処理)
      - [実世界の例](#実世界の例)
    - [ファイルリダイレクト](#ファイルリダイレクト)
  - [設定オプション](#設定オプション)
  - [例](#例)
    - [コードレビュー](#コードレビュー)
    - [コミットメッセージの生成](#コミットメッセージの生成)
    - [API ドキュメント](#api-ドキュメント)
    - [バッチコード分析](#バッチコード分析)
    - [コードレビュー](#コードレビュー-1)
    - [ログ分析](#ログ分析)
    - [リリースノートの生成](#リリースノートの生成)
    - [モデルとツールの使用状況追跡](#モデルとツールの使用状況追跡)
  - [リソース](#リソース)

## 概要

ヘッドレスモードは、Gemini CLI にヘッドレスインターフェースを提供します。

- コマンドライン引数または標準入力経由でプロンプトを受け入れます。
- 構造化された出力（テキストまたは JSON）を返します。
- ファイルリダイレクトとパイピングをサポートします。
- 自動化とスクリプト作成ワークフローを有効にします。
- エラー処理のために一貫した終了コードを提供します。

## 基本的な使い方

### 直接プロンプト

`--prompt`（または `-p`）フラグを使用してヘッドレスモードで実行します。

```bash
gemini --prompt "What is machine learning?"
```

### 標準入力

ターミナルから Gemini CLI に入力をパイプします。

```bash
echo "Explain this code" | gemini
```

### ファイル入力との組み合わせ

ファイルから読み込み、Gemini で処理します。

```bash
cat README.md | gemini --prompt "Summarize this documentation"
```

## 出力形式

### テキスト出力 (デフォルト)

標準の人間が読める出力:

```bash
gemini -p "What is the capital of France?"
```

応答形式:

```
The capital of France is Paris.
```

### JSON 出力

応答、統計、メタデータを含む構造化データを返します。この形式は、プログラムによる処理と自動化スクリプトに最適です。

#### 応答スキーマ

JSON 出力は、次の高レベル構造に従います。

```json
{
  "response": "string", // プロンプトに答えるメインの AI 生成コンテンツ
  "stats": {
    // 使用状況メトリックとパフォーマンスデータ
    "models": {
      // モデルごとの API とトークン使用統計
      "[model-name]": {
        "api": {
          /* リクエスト数、エラー、レイテンシ */
        },
        "tokens": {
          /* プロンプト、応答、キャッシュ、合計カウント */
        }
      }
    },
    "tools": {
      // ツール実行統計
      "totalCalls": "number",
      "totalSuccess": "number",
      "totalFail": "number",
      "totalDurationMs": "number",
      "totalDecisions": {
        /* accept, reject, modify, auto_accept カウント */
      },
      "byName": {
        /* ツールごとの詳細統計 */
      }
    },
    "files": {
      // ファイル変更統計
      "totalLinesAdded": "number",
      "totalLinesRemoved": "number"
    }
  },
  "error": {
    // エラーが発生した場合にのみ存在
    "type": "string", // エラータイプ（例: "ApiError", "AuthError"）
    "message": "string", // 人間が読めるエラーの説明
    "code": "number" // オプションのエラーコード
  }
}
```

#### 使用例

```bash
gemini -p "What is the capital of France?" --output-format json
```

応答:

```json
{
  "response": "The capital of France is Paris.",
  "stats": {
    "models": {
      "gemini-2.5-pro": {
        "api": {
          "totalRequests": 2,
          "totalErrors": 0,
          "totalLatencyMs": 5053
        },
        "tokens": {
          "prompt": 24939,
          "candidates": 20,
          "total": 25113,
          "cached": 21263,
          "thoughts": 154,
          "tool": 0
        }
      },
      "gemini-2.5-flash": {
        "api": {
          "totalRequests": 1,
          "totalErrors": 0,
          "totalLatencyMs": 1879
        },
        "tokens": {
          "prompt": 8965,
          "candidates": 10,
          "total": 9033,
          "cached": 0,
          "thoughts": 30,
          "tool": 28
        }
      }
    },
    "tools": {
      "totalCalls": 1,
      "totalSuccess": 1,
      "totalFail": 0,
      "totalDurationMs": 1881,
      "totalDecisions": {
        "accept": 0,
        "reject": 0,
        "modify": 0,
        "auto_accept": 1
      },
      "byName": {
        "google_web_search": {
          "count": 1,
          "success": 1,
          "fail": 0,
          "durationMs": 1881,
          "decisions": {
            "accept": 0,
            "reject": 0,
            "modify": 0,
            "auto_accept": 1
          }
        }
      }
    },
    "files": {
      "totalLinesAdded": 0,
      "totalLinesRemoved": 0
    }
  }
}
```

### ストリーミング JSON 出力

リアルタイムイベントを改行区切りの JSON（JSONL）として返します。初期化、メッセージ、ツール呼び出し、結果など、すべての重要なアクションは発生するとすぐに発行されます。この形式は、長時間実行される操作の監視、ライブ進行状況を表示する UI の構築、イベントに反応する自動化パイプラインの作成に最適です。

#### ストリーミング JSON を使用するタイミング

次の場合に `--output-format stream-json` を使用します。

- **リアルタイムの進行状況監視** - ツール呼び出しと応答が発生するとすぐに確認
- **イベント駆動型自動化** - 特定のイベント（例: ツール障害）に反応
- **ライブ UI 更新** - AI エージェントの活動をリアルタイムで表示するインターフェースを構築
- **詳細な実行ログ** - タイムスタンプ付きの完全な対話履歴をキャプチャ
- **パイプライン統合** - ログ/監視システムにイベントをストリーム

#### イベントタイプ

ストリーミング形式は6つのイベントタイプを発行します。

1.  **`init`** - セッション開始（session_id、モデルを含む）
2.  **`message`** - ユーザープロンプトとアシスタント応答
3.  **`tool_use`** - パラメータを含むツール呼び出しリクエスト
4.  **`tool_result`** - ツール実行結果（成功/エラー）
5.  **`error`** - 致命的ではないエラーと警告
6.  **`result`** - 集計された統計情報を含む最終セッション結果

#### 基本的な使い方

```bash
# コンソールへのイベントストリーム
gemini --output-format stream-json --prompt "What is 2+2?"

# イベントストリームをファイルに保存
gemini --output-format stream-json --prompt "Analyze this code" > events.jsonl

# jq でパース
gemini --output-format stream-json --prompt "List files" | jq -r '.type'
```

#### 出力例

各行は完全な JSON イベントです。

```jsonl
{"type":"init","timestamp":"2025-10-10T12:00:00.000Z","session_id":"abc123","model":"gemini-2.0-flash-exp"}
{"type":"message","role":"user","content":"List files in current directory","timestamp":"2025-10-10T12:00:01.000Z"}
{"type":"tool_use","tool_name":"Bash","tool_id":"bash-123","parameters":{"command":"ls -la"},"timestamp":"2025-10-10T12:00:02.000Z"}
{"type":"tool_result","tool_id":"bash-123","status":"success","output":"file1.txt\nfile2.txt","timestamp":"2025-10-10T12:00:03.000Z"}
{"type":"message","role":"assistant","content":"Here are the files...","delta":true,"timestamp":"2025-10-10T12:00:04.000Z"}
{"type":"result","status":"success","stats":{"total_tokens":250,"input_tokens":50,"output_tokens":200,"duration_ms":3000,"tool_calls":1},"timestamp":"2025-10-10T12:00:05.000Z"}
```

### ファイルリダイレクト

出力をファイルに保存したり、他のコマンドにパイプしたりします。

```bash
# ファイルに保存
gemini -p "Explain Docker" > docker-explanation.txt
gemini -p "Explain Docker" --output-format json > docker-explanation.json

# ファイルに追加
gemini -p "Add more details" >> docker-explanation.txt

# 他のツールにパイプ
gemini -p "What is Kubernetes?" --output-format json | jq '.response'
gemini -p "Explain microservices" | wc -w
gemini -p "List programming languages" | grep -i "python"
```

## 設定オプション

ヘッドレス使用の主要なコマンドラインオプション:

| オプション                  | 説明                        | 例                                            |
| ----------------------- | ---------------------------------- | -------------------------------------------------- |
| `--prompt`, `-p`        | ヘッドレスモードで実行             | `gemini -p "query"`                                |
| `--output-format`       | 出力形式を指定（text、json） | `gemini -p "query" --output-format json`           |
| `--model`, `-m`         | Gemini モデルを指定               | `gemini -p "query" -m gemini-2.5-flash`            |
| `--debug`, `-d`         | デバッグモードを有効にする           | `gemini -p "query" --debug`                        |
| `--include-directories` | 追加のディレクトリを含める       | `gemini -p "query" --include-directories src,docs` |
| `--yolo`, `-y`          | すべてのアクションを自動承認       | `gemini -p "query" --yolo`                         |
| `--approval-mode`       | 承認モードを設定                 | `gemini -p "query" --approval-mode auto_edit`      |

利用可能なすべての構成オプション、設定ファイル、環境変数の詳細については、[構成ガイド](../get-started/configuration.md)を参照してください。

## 例

#### コードレビュー

```bash
cat src/auth.py | gemini -p "Review this authentication code for security issues" > security-review.txt
```

#### コミットメッセージの生成

```bash
result=$(git diff --cached | gemini -p "Write a concise commit message for these changes" --output-format json)
echo "$result" | jq -r '.response'
```

#### API ドキュメント

```bash
result=$(cat api/routes.js | gemini -p "Generate OpenAPI spec for these routes" --output-format json)
echo "$result" | jq -r '.response' > openapi.json
```

#### バッチコード分析

```bash
for file in src/*.py; do
    echo "Analyzing $file..."
    result=$(cat "$file" | gemini -p "Find potential bugs and suggest improvements" --output-format json)
    echo "$result" | jq -r '.response' > "reports/$(basename "$file").analysis"
    echo "Completed analysis for $(basename "$file")" >> reports/progress.log
done
```

#### コードレビュー

```bash
result=$(git diff origin/main...HEAD | gemini -p "Review these changes for bugs, security issues, and code quality" --output-format json)
echo "$result" | jq -r '.response' > pr-review.json
```

#### ログ分析

```bash
grep "ERROR" /var/log/app.log | tail -20 | gemini -p "Analyze these errors and suggest root cause and fixes" > error-analysis.txt
```

#### リリースノートの生成

```bash
result=$(git log --oneline v1.0.0..HEAD | gemini -p "Generate release notes from these commits" --output-format json)
response=$(echo "$result" | jq -r '.response')
echo "$response"
echo "$response" >> CHANGELOG.md
```

#### モデルとツールの使用状況追跡

```bash
result=$(gemini -p "Explain this database schema" --include-directories db --output-format json)
total_tokens=$(echo "$result" | jq -r '.stats.models // {} | to_entries | map(.value.tokens.total) | add // 0')
models_used=$(echo "$result" | jq -r '.stats.models // {} | keys | join(", ") | if . == "" then "none" else . end')
tool_calls=$(echo "$result" | jq -r '.stats.tools.totalCalls // 0')
tools_used=$(echo "$result" | jq -r '.stats.tools.byName // {} | keys | join(", ") | if . == "" then "none" else . end')
echo "$(date): $total_tokens tokens, $tool_calls tool calls ($tools_used) used with models: $models_used" >> usage.log
echo "$result" | jq -r '.response' > schema-docs.md
echo "Recent usage trends:"
tail -5 usage.log
```

## リソース

- [CLI 構成](../get-started/configuration.md) - 完全な構成ガイド
- [認証](../get-started/authentication.md) - 認証のセットアップ
- [コマンド](./commands.md) - 対話型コマンドリファレンス
- [チュートリアル](./tutorials.md) - ステップバイステップの自動化ガイド