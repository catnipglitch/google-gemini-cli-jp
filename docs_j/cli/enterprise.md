# エンタープライズ向け Gemini CLI

このドキュメントは、エンタープライズ環境で Gemini CLI をデプロイおよび管理するための構成パターンとベストプラクティスを概説します。システムレベルの設定を活用することで、管理者はセキュリティポリシーを適用し、ツールアクセスを管理し、すべてのユーザーに一貫したエクスペリエンスを保証できます。

> **セキュリティに関する注意:** このドキュメントで説明されているパターンは、管理者が Gemini CLI を使用するためのより制御された安全な環境を作成するのに役立つことを意図しています。ただし、これらは完璧なセキュリティ境界と見なされるべきではありません。ローカルマシンに十分な権限を持つ決意の固いユーザーは、これらの構成を回避できる可能性があります。これらの措置は、意図しない誤用を防ぎ、管理された環境で企業ポリシーを適用することを目的としており、ローカルの管理者権限を持つ悪意のあるアクターから防御することを目的としていません。

## 一元化された構成: システム設定ファイル

エンタープライズ管理のための最も強力なツールは、システム全体の設定ファイルです。これらのファイルを使用すると、ベースライン構成（`system-defaults.json`）と、マシン上のすべてのユーザーに適用される一連のオーバーライド（`settings.json`）を定義できます。構成オプションの完全な概要については、[構成ドキュメント](../get-started/configuration.md)を参照してください。

設定は4つのファイルからマージされます。単一値設定（`theme` など）の優先順位は次のとおりです。

1.  システムデフォルト（`system-defaults.json`）
2.  ユーザー設定（`~/.gemini/settings.json`）
3.  ワークスペース設定（`<project>/.gemini/settings.json`）
4.  システムオーバーライド（`settings.json`）

これは、システムオーバーライドファイルが最終的な決定権を持つことを意味します。配列（`includeDirectories`）またはオブジェクト（`mcpServers`）である設定の場合、値はマージされます。

**マージと優先順位の例:**

さまざまなレベルからの設定がどのように結合されるかを次に示します。

- **システムデフォルト `system-defaults.json`:**

  ```json
  {
    "ui": {
      "theme": "default-corporate-theme"
    },
    "context": {
      "includeDirectories": ["/etc/gemini-cli/common-context"]
    }
  }
  ```

- **ユーザー `settings.json` (`~/.gemini/settings.json`):**

  ```json
  {
    "ui": {
      "theme": "user-preferred-dark-theme"
    },
    "mcpServers": {
      "corp-server": {
        "command": "/usr/local/bin/corp-server-dev"
      },
      "user-tool": {
        "command": "npm start --prefix ~/tools/my-tool"
      }
    },
    "context": {
      "includeDirectories": ["~/gemini-context"]
    }
  }
  ```

- **ワークスペース `settings.json` (`<project>/.gemini/settings.json`):**

  ```json
  {
    "ui": {
      "theme": "project-specific-light-theme"
    },
    "mcpServers": {
      "project-tool": {
        "command": "npm start"
      }
    },
    "context": {
      "includeDirectories": ["./project-context"]
    }
  }
  ```

- **システムオーバーライド `settings.json`:**

  ```json
  {
    "ui": {
      "theme": "system-enforced-theme"
    },
    "mcpServers": {
      "corp-server": {
        "command": "/usr/local/bin/corp-server-prod"
      }
    },
    "context": {
      "includeDirectories": ["/etc/gemini-cli/global-context"]
    }
  }
  ```

これにより、次のマージされた構成が得られます。

- **最終的なマージされた構成:**

  ```json
  {
    "ui": {
      "theme": "system-enforced-theme"
    },
    "mcpServers": {
      "corp-server": {
        "command": "/usr/local/bin/corp-server-prod"
      },
      "user-tool": {
        "command": "npm start --prefix ~/tools/my-tool"
      },
      "project-tool": {
        "command": "npm start"
      }
    },
    "context": {
      "includeDirectories": [
        "/etc/gemini-cli/common-context",
        "~/gemini-context",
        "./project-context",
        "/etc/gemini-cli/global-context"
      ]
    }
  }
  ```

**理由:**

- **`theme`**: 最高優先順位を持つシステムオーバーライドからの値（`system-enforced-theme`）が使用されます。
- **`mcpServers`**: オブジェクトはマージされます。システムオーバーライドからの `corp-server` 定義は、ユーザーの定義よりも優先されます。一意の `user-tool` と `project-tool` が含まれます。
- **`includeDirectories`**: 配列は、システムデフォルト、ユーザー、ワークスペース、システムオーバーライドの順に連結されます。

- **場所**:
  - **Linux**: `/etc/gemini-cli/settings.json`
  - **Windows**: `C:\ProgramData\gemini-cli\settings.json`
  - **macOS**: `/Library/Application Support/GeminiCli/settings.json`
  - パスは `GEMINI_CLI_SYSTEM_SETTINGS_PATH` 環境変数を使用してオーバーライドできます。
- **制御**: このファイルはシステム管理者によって管理され、ユーザーによる不正な変更を防ぐために適切なファイル権限で保護されるべきです。

システム設定ファイルを使用することで、以下に説明するセキュリティおよび構成パターンを適用できます。

### ラッパースクリプトでシステム設定を強制する

`GEMINI_CLI_SYSTEM_SETTINGS_PATH` 環境変数は柔軟性を提供しますが、ユーザーはそれを別の設定ファイルにポイントするようにオーバーライドし、一元管理された構成をバイパスする可能性があります。これを軽減するために、企業は、環境変数が常に企業制御パスに設定されることを保証するラッパースクリプトまたはエイリアスを展開できます。

このアプローチにより、ユーザーが `gemini` コマンドをどのように呼び出しても、エンタープライズ設定が常に最高の優先順位でロードされることが保証されます。

**ラッパースクリプトの例:**

管理者は `gemini` という名前のスクリプトを作成し、実際の Gemini CLI バイナリよりもユーザーの `PATH` の早い場所にあるディレクトリ（例: `/usr/local/bin/gemini`）に配置できます。

```bash
#!/bin/bash

# 企業システム設定ファイルへのパスを強制します。
# これにより、会社の構成が常に適用されます。
export GEMINI_CLI_SYSTEM_SETTINGS_PATH="/etc/gemini-cli/settings.json"

# 元の gemini 実行可能ファイルを見つけます。
# これは簡単な例です。インストール方法によっては、より堅牢なソリューションが必要になる場合があります。
REAL_GEMINI_PATH=$(type -aP gemini | grep -v "^$(type -P gemini)$" | head -n 1)

if [ -z "$REAL_GEMINI_PATH" ]; then
  echo "エラー: 元の 'gemini' 実行可能ファイルが見つかりませんでした。" >&2
  exit 1
fi

# すべての引数を実際の Gemini CLI 実行可能ファイルに渡します。
exec "$REAL_GEMINI_PATH" "$@"
```

このスクリプトを展開することで、`GEMINI_CLI_SYSTEM_SETTINGS_PATH` はスクリプトの環境内で設定され、`exec` コマンドはスクリプトプロセスを実際の Gemini CLI プロセスに置き換え、環境変数を継承します。これにより、ユーザーが強制された設定をバイパスすることが著しく困難になります。

## ツールアクセスの制限

Gemini モデルが使用できるツールを制御することで、セキュリティを大幅に強化できます。これは、`tools.core` と `tools.exclude` 設定を通じて実現されます。利用可能なツールのリストについては、[ツールドキュメント](../tools/index.md)を参照してください。

### `coreTools` を使用した許可リスト

最も安全なアプローチは、ユーザーが実行を許可されているツールとコマンドを明示的に許可リストに追加することです。これにより、承認されたリストにないツールの使用が防止されます。

**例:** 安全な読み取り専用ファイル操作とファイルリストのみを許可します。

```json
{
  "tools": {
    "core": ["ReadFileTool", "GlobTool", "ShellTool(ls)"]
  }
}
```

### `excludeTools` を使用したブロックリスト

代わりに、環境で危険と見なされる特定のツールをブロックリストに追加することもできます。

**例:** ファイルを削除するためのシェルツールの使用を防止します。

```json
{
  "tools": {
    "exclude": ["ShellTool(rm -rf)"]
  }
}
```

**セキュリティに関する注意:** `excludeTools` を使用したブロックリストは、既知の不正なコマンドをブロックすることに依存しているため、`coreTools` を使用した許可リストよりも安全性が低く、巧妙なユーザーは単純な文字列ベースのブロックを回避する方法を見つける可能性があります。**許可リストが推奨されるアプローチです。**

### YOLO モードの無効化

ツール実行の確認プロンプトをユーザーがバイパスできないようにするには、ポリシーレベルで YOLO モードを無効にできます。これは、明示的なユーザー承認なしにモデルがツールを実行するのを防ぐため、重要な安全レイヤーを追加します。

**例:** すべてのツール実行でユーザー確認を強制します。

```json
{
  "security": {
    "disableYoloMode": true
  }
}
```

この設定は、意図しないツール実行を防ぐために、エンタープライズ環境で強く推奨されます。

## カスタムツールの管理 (MCP サーバー)

組織が [モデルコンテキストプロトコル (MCP) サーバー](../core/tools-api.md)を介してカスタムツールを使用する場合、セキュリティポリシーを効果的に適用するために、サーバー構成がどのように管理されているかを理解することが重要です。

### MCP サーバー構成のマージ方法

Gemini CLI は、システム、ワークスペース、ユーザーの3つのレベルから `settings.json` ファイルをロードします。`mcpServers` オブジェクトに関して言えば、これらの構成は**マージされます**。

1.  **マージ:** 3つのレベルすべてのサーバーリストが結合されて単一のリストになります。
2.  **優先順位:** 複数のレベルで**同じ名前**のサーバーが定義されている場合（例: `corp-api` という名前のサーバーがシステム設定とユーザー設定の両方に存在する場合）、優先順位が最も高いレベルの定義が使用されます。優先順位は次のとおりです。**システム > ワークスペース > ユーザー**。

これは、ユーザーがシステムレベルの設定で既に定義されているサーバーの定義をオーバーライド**できない**ことを意味します。ただし、一意の名前を持つ新しいサーバーを**追加する**ことはできます。

### ツールのカタログの強制

MCP ツールエコシステムのセキュリティは、正規サーバーの定義と、それらの名前を許可リストに追加することの組み合わせに依存します。

### MCP サーバー内のツールを制限する

さらに高いセキュリティのために、特にサードパーティの MCP サーバーを扱う場合、モデルに公開されるサーバーからの特定のツールを制限できます。これは、サーバーの定義内の `includeTools` と `excludeTools` プロパティを使用して行われます。これにより、潜在的に危険なツールを許可せずに、サーバーからツールのサブセットを使用できます。

最小特権の原則に従い、必要なツールのみの許可リストを作成するために `includeTools` を使用することを強くお勧めします。

**例:** サードパーティの MCP サーバーからの `code-search` と `get-ticket-details` ツールのみを許可し、サーバーが `delete-ticket` などの他のツールを提供していても許可しない場合。

```json
{
  "mcp": {
    "allowed": ["third-party-analyzer"]
  },
  "mcpServers": {
    "third-party-analyzer": {
      "command": "/usr/local/bin/start-3p-analyzer.sh",
      "includeTools": ["code-search", "get-ticket-details"]
    }
  }
}
```

#### より安全なパターン: システム設定で定義して許可リストに追加する

安全で一元管理されたツールのカタログを作成するには、システム管理者はシステムレベルの `settings.json` ファイルで次の両方を**実行する必要があります**。

1.  承認されたすべてのサーバーの**完全な構成**を `mcpServers` オブジェクトに定義します。これにより、ユーザーが同じ名前のサーバーを定義しても、安全なシステムレベルの定義が優先されます。
2.  これらのサーバーの**名前**を `mcp.allowed` 設定を使用して許可リストに追加します。これは、このリストにないサーバーをユーザーが実行するのを防ぐ重要なセキュリティ手順です。この設定が省略されている場合、CLI はユーザーが定義したすべてのサーバーをマージして許可します。

**システム `settings.json` の例:**

1. 承認されたすべてのサーバーの _名前_ を許可リストに追加します。これにより、ユーザーが独自のサーバーを追加するのを防ぐことができます。

2. 許可リスト上の各サーバーの正規の _定義_ を提供します。

```json
{
  "mcp": {
    "allowed": ["corp-data-api", "source-code-analyzer"]
  },
  "mcpServers": {
    "corp-data-api": {
      "command": "/usr/local/bin/start-corp-api.sh",
      "timeout": 5000
    },
    "source-code-analyzer": {
      "command": "/usr/local/bin/start-analyzer.sh"
    }
  }
}
```

このパターンは、定義と許可リストの両方を使用するため、より安全です。ユーザーが定義したサーバーは、システム定義によってオーバーライドされるか（同じ名前の場合）、`mcp.allowed` リストに名前がないためブロックされます。

### 安全性の低いパターン: 許可リストの省略

管理者が `mcpServers` オブジェクトを定義しているにもかかわらず、`mcp.allowed` 許可リストを指定しなかった場合、ユーザーは独自のサーバーを追加できる可能性があります。

**システム `settings.json` の例:**

この構成はサーバーを定義しますが、許可リストは強制しません。管理者は「mcp.allowed」設定を含めていません。

```json
{
  "mcpServers": {
    "corp-data-api": {
      "command": "/usr/local/bin/start-corp-api.sh"
    }
  }
}
```

このシナリオでは、ユーザーはローカルの `settings.json` に独自のサーバーを追加できます。マージされた結果をフィルタリングする `mcp.allowed` リストがないため、ユーザーのサーバーは利用可能なツールのリストに追加され、実行が許可されます。

## セキュリティのためにサンドボックスを強制する

潜在的に有害な操作のリスクを軽減するために、すべてのツール実行にサンドボックスの使用を強制できます。サンドボックスは、コンテナ化された環境でツールの実行を隔離します。

**例:** すべてのツール実行を Docker サンドボックス内で強制します。

```json
{
  "tools": {
    "sandbox": "docker"
  }
}
```

[サンドボックスドキュメント](./sandbox.md)で説明されているように、カスタム `sandbox.Dockerfile` をビルドすることで、サンドボックス用のカスタムで強化された Docker イメージを指定することもできます。

## プロキシ経由でネットワークアクセスを制御する

厳格なネットワークポリシーを持つ企業環境では、すべての送信トラフィックを企業プロキシ経由でルーティングするように Gemini CLI を構成できます。これは環境変数で設定できますが、`mcpServers` 構成を介してカスタムツールにも強制できます。

**例 (MCP サーバーの場合):**

```json
{
  "mcpServers": {
    "proxied-server": {
      "command": "node",
      "args": ["mcp_server.js"],
      "env": {
        "HTTP_PROXY": "http://proxy.example.com:8080",
        "HTTPS_PROXY": "http://proxy.example.com:8080"
      }
    }
  }
}
```

## テレメトリと監査

監査および監視のために、Gemini CLI がテレメトリデータを中央の場所に送信するように構成できます。これにより、ツール使用状況やその他のイベントを追跡できます。詳細については、[テレメトリドキュメント](./telemetry.md)を参照してください。

**例:** テレメトリを有効にし、ローカルの OTLP コレクターに送信します。`otlpEndpoint` が指定されていない場合、デフォルトは `http://localhost:4317` です。

```json
{
  "telemetry": {
    "enabled": true,
    "target": "gcp",
    "logPrompts": false
  }
}
```

**注:** ユーザープロンプトから潜在的に機密性の高い情報が収集されるのを防ぐため、エンタープライズ設定では `logPrompts` を `false` に設定してください。

## 認証

システムレベルの `settings.json` ファイルで `enforcedAuthType` を設定することにより、すべてのユーザーに対して特定の認証方法を強制できます。これにより、ユーザーが別の認証方法を選択できなくなります。詳細については、[認証ドキュメント](./authentication.md)を参照してください。

**例:** すべてのユーザーに対して Google ログインの使用を強制します。

```json
{
  "enforcedAuthType": "oauth-personal"
}
```

If a user has a different authentication method configured, they will be
prompted to switch to the enforced method. In non-interactive mode, the CLI will
exit with an error if the configured authentication method does not match the
enforced one.

### Restricting logins to corporate domains

For enterprises using Google Workspace, you can enforce that users only
authenticate with their corporate Google accounts. This is a network-level
control that is configured on a proxy server, not within Gemini CLI itself. It
works by intercepting authentication requests to Google and adding a special
HTTP header.

This policy prevents users from logging in with personal Gmail accounts or other
non-corporate Google accounts.

For detailed instructions, see the Google Workspace Admin Help article on
[blocking access to consumer accounts](https://support.google.com/a/answer/1668854?hl=en#zippy=%2Cstep-choose-a-web-proxy-server%2Cstep-configure-the-network-to-block-certain-accounts).

The general steps are as follows:

1.  **Intercept Requests**: Configure your web proxy to intercept all requests
    to `google.com`.
2.  **Add HTTP Header**: For each intercepted request, add the
    `X-GoogApps-Allowed-Domains` HTTP header.
3.  **Specify Domains**: The value of the header should be a comma-separated
    list of your approved Google Workspace domain names.

**Example header:**

```
X-GoogApps-Allowed-Domains: my-corporate-domain.com, secondary-domain.com
```

When this header is present, Google's authentication service will only allow
logins from accounts belonging to the specified domains.

## Putting it all together: example system `settings.json`

Here is an example of a system `settings.json` file that combines several of the
patterns discussed above to create a secure, controlled environment for Gemini
CLI.

```json
{
  "tools": {
    "sandbox": "docker",
    "core": [
      "ReadFileTool",
      "GlobTool",
      "ShellTool(ls)",
      "ShellTool(cat)",
      "ShellTool(grep)"
    ]
  },
  "mcp": {
    "allowed": ["corp-tools"]
  },
  "mcpServers": {
    "corp-tools": {
      "command": "/opt/gemini-tools/start.sh",
      "timeout": 5000
    }
  },
  "telemetry": {
    "enabled": true,
    "target": "gcp",
    "otlpEndpoint": "https://telemetry-prod.example.com:4317",
    "logPrompts": false
  },
  "advanced": {
    "bugCommand": {
      "urlTemplate": "https://servicedesk.example.com/new-ticket?title={title}&details={info}"
    }
  },
  "privacy": {
    "usageStatisticsEnabled": false
  }
}
```

This configuration:

- Forces all tool execution into a Docker sandbox.
- Strictly uses an allowlist for a small set of safe shell commands and file
  tools.
- Defines and allows a single corporate MCP server for custom tools.
- Enables telemetry for auditing, without logging prompt content.
- Redirects the `/bug` command to an internal ticketing system.
- Disables general usage statistics collection.