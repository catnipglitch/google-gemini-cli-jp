> **注:** このページは [installation.md](installation.md) に置き換えられます。

# Gemini CLI のインストール・実行・デプロイ

このページでは Gemini CLI のインストール手段とデプロイアーキテクチャを概説します。

## 実行方法

状況に応じて複数の実行方法があります。

1. **標準インストール**（推奨）：最も簡単なエンドユーザー向け手順。
2. **サンドボックス内実行**：セキュリティと分離を重視する場合。
3. **ソースから実行**：貢献者が開発時に利用。

### 1. 標準インストール

```bash
npm install -g @google/gemini-cli
gemini
```

- または NPX で最新版を即実行:

```bash
npx @google/gemini-cli
```

### 2. サンドボックス内実行（Docker / Podman）

- レジストリから直接実行:

```bash
docker run --rm -it us-docker.pkg.dev/gemini-code-dev/gemini-cli/sandbox:0.1.1
```

- インストール済み CLI を `--sandbox` フラグで実行:

```bash
gemini --sandbox -y -p "your prompt here"
```

### 3. ソースから実行

- **開発モード:** ホットリロード付きで開発。

```bash
npm run start
```

- **リンク済みパッケージ:** `npm link packages/cli` でグローバルにリンク後、`gemini` コマンドでローカルビルドを利用。

### 4. GitHub 直読み込み

```bash
npx https://github.com/google-gemini/gemini-cli
```

開発中の最新コミットを試すのに便利。

## デプロイアーキテクチャ

- **NPM パッケージ:** `@google/gemini-cli-core`（バックエンド）、`@google/gemini-cli`（フロントエンド）が NPM へ公開されます。
- **ビルド:** `tsc` で `dist/` を生成し NPM へ公開。GitHub 直実行は `package.json` の `prepare` スクリプトが `esbuild` でバンドルを即時生成。
- **Docker サンドボックス:** `gemini-cli-sandbox` イメージが公開され、事前に CLI を含む状態で利用可能。

## リリースプロセス

GitHub Actions でビルドと公開を自動化。
1. `tsc` で NPM パッケージをビルド。
2. Artifact Registry へ公開。
3. GitHub Releases を作成しバンドルを添付。
