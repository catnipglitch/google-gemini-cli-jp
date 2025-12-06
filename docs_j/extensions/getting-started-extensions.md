# Gemini CLI 拡張機能を始める

このガイドでは最初の Gemini CLI 拡張機能を作る手順を紹介します。
新しい拡張機能のセットアップ、MCP サーバーによるカスタムツールの追加、
カスタムコマンドの作成、`GEMINI.md` によるモデルへの文脈提供を学びます。

## 前提条件

Gemini CLI がインストール済みで、Node.js と TypeScript の基本的な理解があることを確認してください。

## ステップ 1: 拡張機能の作成

組み込みテンプレートを利用するのが最も簡単です。ここでは `mcp-server` の例を使います。

以下を実行して `my-first-extension` というディレクトリを作成し、テンプレートファイルを配置します。

```bash
gemini extensions new my-first-extension mcp-server
```

作成される構成は次の通りです。

```
my-first-extension/
├── example.ts
├── gemini-extension.json
├── package.json
└── tsconfig.json
```

## ステップ 2: 拡張機能ファイルを理解する

主要なファイルを見ていきましょう。

### `gemini-extension.json`

拡張機能のマニフェストです。Gemini CLI に拡張機能の読み込み方法を伝えます。

```json
{
  "name": "my-first-extension",
  "version": "1.0.0",
  "mcpServers": {
    "nodeServer": {
      "command": "node",
      "args": ["${extensionPath}${/}dist${/}example.js"],
      "cwd": "${extensionPath}"
    }
  }
}
```

- `name`: 拡張機能の一意な名前。
- `version`: 拡張機能のバージョン。
- `mcpServers`: Model Context Protocol (MCP) サーバーを定義し、モデルが利用するツールを追加可能にします。
  - `command`, `args`, `cwd`: サーバーの起動方法を定義します。`${extensionPath}` 変数は拡張機能のインストール先の絶対パスに置き換わるので、インストール先を問わず動作します。

### `example.ts`

MCP サーバーのソースコードです。`@modelcontextprotocol/sdk` を使ったシンプルな Node.js サーバーです。

```typescript
/**
 * @license
 * Copyright 2025 Google LLC
 * SPDX-License-Identifier: Apache-2.0
 */

import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';
import { z } from 'zod';

const server = new McpServer({
  name: 'prompt-server',
  version: '1.0.0',
});

server.registerTool(
  'fetch_posts',
  {
    description: 'Fetches a list of posts from a public API.',
    inputSchema: z.object({}).shape,
  },
  async () => {
    const apiResponse = await fetch(
      'https://jsonplaceholder.typicode.com/posts',
    );
    const posts = await apiResponse.json();
    const response = { posts: posts.slice(0, 5) };
    return {
      content: [
        {
          type: 'text',
          text: JSON.stringify(response),
        },
      ],
    };
  },
);

const transport = new StdioServerTransport();
await server.connect(transport);
```

このサーバーは `fetch_posts` というツールを定義し、公開 API からデータを取得して返します。

### `package.json` と `tsconfig.json`

TypeScript プロジェクトの標準ファイルです。`package.json` では依存関係と `build` スクリプトを定義し、`tsconfig.json` でコンパイラを設定します。

## ステップ 3: 拡張機能のビルドとリンク

利用する前に TypeScript をビルドし、開発中の拡張機能を Gemini CLI にリンクします。

1.  **依存関係のインストール**

    ```bash
    cd my-first-extension
    npm install
    ```

2.  **サーバーのビルド**

    ```bash
    npm run build
    ```

    これにより `example.ts` が `dist/example.js` にコンパイルされ、`gemini-extension.json` で参照されます。

3.  **拡張機能のリンク**

    `link` コマンドは Gemini CLI の拡張機能ディレクトリと開発ディレクトリの間にシンボリックリンクを作成します。

    ```bash
    gemini extensions link .
    ```

変更を加えるたびに再インストールする必要はなく、すぐに反映されます。

Gemini CLI セッションを再起動すると `fetch_posts` ツールが利用可能になります。「fetch posts」と尋ねて確認してください。

## ステップ 4: カスタムコマンドを追加

複雑なプロンプトをショートカット化するカスタムコマンドを作成します。

1.  `commands` ディレクトリとコマンドグループを作成します。

    ```bash
    mkdir -p commands/fs
    ```

2.  `commands/fs/grep-code.toml` を作成します。

    ```toml
    prompt = """
    Please summarize the findings for the pattern `{{args}}`.

    Search Results:
    !{grep -r {{args}} .}
    """
    ```

    この `/fs:grep-code` コマンドは引数を受け取り、`grep` コマンドを実行してその結果を要約します。

ファイルを保存し、Gemini CLI を再起動すると `/fs:grep-code "some pattern"` で新しいコマンドを使えます。

## ステップ 5: カスタム `GEMINI.md` を追加

モデルに永続的な文脈を提供するために、拡張機能のルートに `GEMINI.md` を追加します。ツールや振る舞いの説明に便利です。

1.  `GEMINI.md` を作成します。

    ```markdown
    # My First Extension Instructions

    You are an expert developer assistant. When the user asks you to fetch
    posts, use the `fetch_posts` tool. Be concise in your responses.
    ```

2.  `gemini-extension.json` を更新して CLI に読み込みを知らせます。

    ```json
    {
      "name": "my-first-extension",
      "version": "1.0.0",
      "contextFileName": "GEMINI.md",
      "mcpServers": {
        "nodeServer": {
          "command": "node",
          "args": ["${extensionPath}${/}dist${/}example.js"],
          "cwd": "${extensionPath}"
        }
      }
    }
    ```

CLI を再起動すると、この `GEMINI.md` に書いた文脈が拡張機能有効時のすべてのセッションに渡されます。

## ステップ 6: 拡張機能のリリース

完成した拡張機能は他者と共有できます。主な方法は Git リポジトリか GitHub Releases を使うことです。公開リポジトリが最も手軽です。

両方の方法についての詳細は
[拡張機能リリースガイド](./extension-releasing.md) をご覧ください。

## まとめ

以下のことを学びました。

- テンプレートから拡張機能を起動する。
- MCP サーバーでカスタムツールを追加する。
- 便利なカスタムコマンドを作成する。
- モデルに対する永続的な文脈を提供する。
- 開発中の拡張機能をリンクする。

ここから応用機能を探求し、Gemini CLI に強力な能力を追加できます。
