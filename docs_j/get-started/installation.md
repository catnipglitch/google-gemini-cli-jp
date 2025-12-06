# Gemini CLI のインストール・実行・デプロイ

このドキュメントでは Gemini CLI のインストール手順とデプロイアーキテクチャを概説します。

## 実行方法

1. **標準インストール**（推奨）
   ```bash
   npm install -g @google/gemini-cli
   gemini
   ```
   または NPX で直接実行:
   ```bash
   npx @google/gemini-cli
   ```
2. **サンドボックス**（Docker/Podman）
   - レジストリから直接実行:
     ```bash
     docker run --rm -it us-docker.pkg.dev/gemini-code-dev/gemini-cli/sandbox:0.1.1
     ```
   - `--sandbox` フラグで既存 CLI をコンテナ内で実行:
     ```bash
     gemini --sandbox -y -p "your prompt here"
     ```
3. **ソースから実行**（貢献者向け）
   - 開発モード（ホットリロード）： `npm run start`
   - ローカルビルドを `npm link packages/cli` でグローバルにリンクし、`gemini` で実行。
4. **GitHub 直読み込み**
   ```bash
   npx https://github.com/google-gemini/gemini-cli
   ```

## デプロイアーキテクチャ

- **NPM パッケージ:** `@google/gemini-cli-core`（バックエンド）と `@google/gemini-cli`（CLI）が公開。
- **ビルド:** `tsc` で `dist/` を生成し NPM へ公開。GitHub 直実行は `prepare` スクリプトが `esbuild` でバンドルを生成。
- **Docker サンドボックス:** `gemini-cli-sandbox` イメージがコンテナレジストリに公開され、事前に CLI を含む環境を提供。

## リリースプロセス

GitHub Actions を使って次の手順を自動化:
1. `tsc` で NPM パッケージをビルド
2. Artifact Registry へ公開
3. GitHub Releases を作成しバンドルを添付
