# チュートリアル

このページには、Gemini CLI と対話するためのチュートリアルが含まれています。

## モデルコンテキストプロトコル (MCP) サーバーのセットアップ

> [!CAUTION] サードパーティの MCP サーバーを使用する前に、そのソースを信頼し、それが提供するツールを理解していることを確認してください。サードパーティのサーバーの使用は、自己責任で行ってください。

このチュートリアルでは、[GitHub MCP サーバー](https://github.com/github/github-mcp-server)を例として使用して、MCP サーバーをセットアップする方法を示します。GitHub MCP サーバーは、Issue の作成やプルリクエストへのコメントなど、GitHub リポジリと対話するためのツールを提供します。

### 前提条件

開始する前に、以下のものがインストールされ、構成されていることを確認してください。

- **Docker:** [Docker] をインストールして実行します。
- **GitHub 個人アクセストークン (PAT):** 必要なスコープを持つ新しい [classic] または [fine-grained] PAT を作成します。

[Docker]: https://www.docker.com/
[classic]: https://github.com/settings/tokens/new
[fine-grained]: https://github.com/settings/personal-access-tokens/new

### ガイド

#### `settings.json` で MCP サーバーを構成する

プロジェクトのルートディレクトリで、[`.gemini/settings.json` ファイル](../get-started/configuration.md)を作成または開きます。ファイル内に、GitHub MCP サーバーを起動する方法を説明する `mcpServers` 構成ブロックを追加します。

```json
{
  "mcpServers": {
    "github": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "GITHUB_PERSONAL_ACCESS_TOKEN",
        "ghcr.io/github/github-mcp-server"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PERSONAL_ACCESS_TOKEN}"
      }
    }
  }
}
```

#### GitHub トークンを設定する

> [!CAUTION] 個人リポジリとプライベートリポジリの両方へのアクセス権を持つ広範なスコープの個人アクセストークンを使用すると、プライベートリポジリからの情報がパブリックリポジリに漏洩する可能性があります。パブリックリポジリとプライベートリポジリの両方にアクセスを共有しない、きめ細かいアクセストークンを使用することをお勧めします。

環境変数を使用して GitHub PAT を保存します。

```bash
GITHUB_PERSONAL_ACCESS_TOKEN="pat_YourActualGitHubTokenHere"
```

Gemini CLI は、`settings.json` ファイルで定義した `mcpServers` 構成でこの値を使用します。

#### Gemini CLI を起動し、接続を確認する

Gemini CLI を起動すると、構成を自動的に読み取り、GitHub MCP サーバーをバックグラウンドで起動します。その後、自然言語プロンプトを使用して Gemini CLI に GitHub アクションを実行するよう依頼できます。例:

```bash
"get all open issues assigned to me in the 'foo/bar' repo and prioritize them"
```