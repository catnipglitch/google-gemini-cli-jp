# Gemini CLI 拡張機能

_このドキュメントは v0.4.0 リリース時点の内容に準拠しています。_

Gemini CLI 拡張機能パッケージは、プロンプト、MCP サーバー、カスタムコマンドを
馴染み深い形式でまとめ、さまざまな能力を Gemini CLI に追加し、他者と共有できるようにします。
拡張機能はインストールや共有が簡単になるよう設計されています。

[Gemini CLI 拡張機能のギャラリー](https://geminicli.com/extensions/browse/) を参照すると
サンプルを確認できます。

[拡張機能を始める](getting-started-extensions.md) では最初の拡張機能の作成手順を解説しています。

[リリース手順](extension-releasing.md) では GitHub リリースの設定の高度なガイドを掲載しています。

## 拡張機能の管理

`gemini extensions` コマンド群で拡張機能を管理できます。
これらのコマンドは CLI 内からはサポートされていませんが、`/extensions list` でインストール済み拡張機能を確認できます。

これらの操作は CLI を再起動すると有効になります。

### 拡張機能のインストール

`gemini extensions install` で GitHub URL かローカルパスから拡張機能をインストールできます。
インストール先のコピーが作成されるため、ローカル或いは GitHub 上の変更を取り込むには
`gemini extensions update` を実行する必要があります。

GitHub から拡張機能をインストールする場合は、`git` がローカルマシンに必要です。
[git のインストール手順](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) を参照してください。

```
gemini extensions install <source> [--ref <ref>] [--auto-update] [--pre-release] [--consent]
```

- `<source>`: インストールする拡張機能の GitHub URL またはローカルパス。
- `--ref`: インストールする git リファレンス。
- `--auto-update`: 自動更新を有効化。
- `--pre-release`: プレリリース版を対象にする。
- `--consent`: セキュリティリスクを了承し確認プロンプトを省略。

### 拡張機能のアンインストール

```
gemini extensions uninstall gemini-cli-security gemini-cli-another-extension
```

```npm
gemini extensions uninstall <name...>
```

### 拡張機能の無効化

拡張機能は全ワークスペースで有効な状態が初期設定です。
特定の拡張機能を全体または特定のワークスペースで無効化できます。

```
gemini extensions disable <name> [--scope <scope>]
```

- `<name>`: 無効化する拡張機能名。
- `--scope`: `user` または `workspace` を指定。

### 拡張機能の有効化

```
gemini extensions enable <name> [--scope <scope>]
```

- `<name>`: 有効化する拡張機能名。
- `--scope`: `user` または `workspace` を指定。ワークスペース内から `--scope=workspace` を付けて有効化できます。

### 拡張機能の更新

ローカルパスや git リポジトリからインストールした拡張機能は、`gemini-extension.json` の
`version` を最新に更新するために以下を実行します。

```
gemini extensions update <name>
```

すべての拡張機能を最新にしたい場合は:

```
gemini extensions update --all
```

### ボイラープレート拡張機能の生成

`context`, `custom-commands`, `exclude-tools`, `mcp-server` などの例を用意しています。
[examples ディレクトリ](https://github.com/google-gemini/gemini-cli/tree/main/packages/cli/src/commands/extensions/examples) を参照してください。

テンプレートをコピーするには:

```
gemini extensions new <path> [template]
```

- `<path>`: 拡張機能を作成するパス。
- `[template]`: 利用するテンプレート名。

### ローカル拡張機能のリンク

`gemini extensions link` により、インストールディレクトリと開発パスの間にシンボリックリンクを作成できます。
変更をテストするたびに `gemini extensions update` を実行する必要がなくなります。

```
gemini extensions link <path>
```

- `<path>`: リンクしたい拡張機能のパス。

## 仕組み

起動時に Gemini CLI は `<home>/.gemini/extensions` から拡張機能を読み込みます。

拡張機能は `gemini-extension.json` を含むディレクトリとして存在します。
例: `<home>/.gemini/extensions/my-extension/gemini-extension.json`

### `gemini-extension.json`

ファイルには拡張機能の設定が含まれ、次のような構造です。

```json
{
  "name": "my-extension",
  "version": "1.0.0",
  "mcpServers": {
    "my-server": {
      "command": "node my-server.js"
    }
  },
  "contextFileName": "GEMINI.md",
  "excludeTools": ["run_shell_command"]
}
```

- `name`: 拡張機能の一意な名前。CLI 内で衝突を避けるためにディレクトリ名と一致させ、小文字とダッシュのみを使用します。
- `version`: 拡張機能のバージョン。
- `mcpServers`: MCP サーバー名をキー、設定を値としてマップ。`settings.json` 同様に起動時に読み込まれ、同名が両方にある場合は `settings.json` 側が優先されます。`trust` 以外のオプションはすべてサポート。
- `contextFileName`: 拡張機能の文脈ファイル名。指定がなくても `GEMINI.md` があれば読み込みます。
- `excludeTools`: モデルから除去するツールの名前一覧。`run_shell_command` のように引数制限も指定可能（例: `"run_shell_command(rm -rf)"`）。MCP サーバーの `excludeTools` とは機能が異なります。

Gemini CLI は拡張機能を読み込み、設定をマージします。コンフリクトが発生した場合はワークスペース設定が優先されます。

### 設定

_NOTE: 現在実験的機能です。コアフローで設定を導入する前に慎重に検討してください。_

拡張機能はインストール時にユーザーに入力を求める設定を定義できます。API キーや URL などの構成情報を指定するのに役立ちます。

`gemini-extension.json` に `settings` 配列を追加し、各オブジェクトで次のプロパティを指定します。

- `name`: 設定のユーザーフレンドリーな名称。
- `description`: 設定内容と用途の説明。
- `envVar`: 値を格納する環境変数名。
- `sensitive`: 任意の真偽値。`true` の場合、入力をマスキングしキーリングストレージに保存します。

**例**

```json
{
  "name": "my-api-extension",
  "version": "1.0.0",
  "settings": [
    {
      "name": "API Key",
      "description": "Your API key for the service.",
      "envVar": "MY_API_KEY"
    }
  ]
}
```

この拡張機能をインストールすると API キー入力プロンプトが表示され、拡張機能ディレクトリ（例: `<home>/.gemini/extensions/my-api-extension/.env`）に `.env` ファイルとして保存されます。

設定一覧は次のコマンドで確認できます。

```
gemini extensions settings list <extension name>
```

特定設定を変更するには:

```
gemini extensions settings set <extension name> <setting name>
```

### カスタムコマンド

拡張機能は `commands/` サブディレクトリに TOML ファイルを置くことで
[カスタムコマンド](../cli/custom-commands.md) を提供できます。形式はユーザー・プロジェクトコマンドと同じです。

**例**

拡張機能 `gcp` が以下の構成を持つとします。

```
.gemini/extensions/gcp/
├── gemini-extension.json
└── commands/
    ├── deploy.toml
    └── gcs/
        └── sync.toml
```

次のコマンドを提供します。

- `/deploy` - ヘルプでは `[gcp] Custom command from deploy.toml` と表示。
- `/gcs:sync` - ヘルプで `[gcp] Custom command from sync.toml` と表示。

### 衝突解決

拡張機能のコマンドは優先順位が最も低く、ユーザーやプロジェクトコマンドと衝突した場合は次のようになります。

1. **衝突なし:** 拡張機能コマンドは通常名を使う（例: `/deploy`）。
2. **衝突あり:** 拡張機能名をプレフィックスにした名前へリネーム（例: `/gcp.deploy`）。

たとえばユーザーと `gcp` 拡張機能の両方が `deploy` を定義している場合:

- `/deploy`: ユーザー定義コマンドを実行。
- `/gcp.deploy`: 拡張機能のコマンドを実行（`[gcp]` タグ付き）。

## 変数

拡張機能では `gemini-extension.json` 内で変数展開が可能です。例: MCP サーバーで現在ディレクトリを使うときに `"cwd": "${extensionPath}${/}run.ts"` のように書けます。

**サポートされている変数:**

| 変数 | 説明 |
| --- | --- |
| `${extensionPath}` | 拡張機能が配置されているファイルシステム上のフルパス（例: `/Users/username/.gemini/extensions/example-extension`）。シンボリックリンクは展開されません。 |
| `${workspacePath}` | 現在のワークスペースのフルパス。 |
| `${/}` または `${pathSeparator}` | パス区切り文字（OS 依存）。 |
