# 拡張機能のリリース

拡張機能の配布方法は主に以下の 2 つです。

- [Git リポジトリ経由](#releasing-through-a-git-repository)
- [GitHub Releases 経由](#releasing-through-github-releases)

Git リポジトリは最も柔軟で簡単な方法ですが、GitHub Releases は初回インストール時の速度が速く、プラットフォーム別のアーカイブも添付できます。

## Git リポジトリ経由のリリース

もっともシンプルかつ柔軟な選択です。公開リポジトリ（例: GitHub）を用意し、ユーザーは `gemini extensions install <your-repo-uri>` でインストールできます。
`--ref=<ref>` を付ければ特定のブランチ/タグ/コミットを参照でき、指定がなければデフォルトブランチが使われます。

依存しているリファレンスにコミットが追加されると、ユーザーには更新のプロンプトが表示されます。
`gemini-extension.json` 内のバージョンに関わらず、HEAD が常に最新と見なされるためロールバックも容易です。

### Git リポジトリでリリースチャネルを管理する

任意のブランチやタグを参照できるため、複数のチャネル（例: stable, preview, dev）を並行して提供できます。
たとえば `stable` ブランチを用意し、`gemini extensions install <repo> --ref=stable` でインストールさせる構成も可能です。

デフォルトブランチを stable にして、それ以外の開発を `dev` ブランチで行う構成もあります。自由にブランチ/タグを増やして、ユーザーごとに柔軟な提供ができます。

`--ref` にはタグ、ブランチ、特定コミットの SHA も指定できるので、精密にバージョンを管理できます。

### Git リポジトリを使ったリリース例

一般的にはデフォルトブランチを「stable」チャネルとして扱うことを推奨します。
したがって `gemini extensions install <repo>` のデフォルトは stable ブランチになります。

たとえば `stable`, `preview`, `dev` の 3 チャネルを持つ場合、
すべての開発作業は `dev` ブランチで行い、プレビュー版リリース準備ができたら `preview` にマージします。
プレビューを stable に昇格したいときは `preview` から `stable` へマージします。

`git cherry-pick` を使って別ブランチから変更を取り込むこともできますが、履歴が分岐し強制プッシュが必要になる可能性があります。
そのため cherry-pick を多用するならデフォルトブランチを stable にしない方が安全です。

## GitHub Releases 経由のリリース

[GitHub Releases](https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases) を通じて拡張機能を配布できます。この方法ではリポジトリのクローンが不要なため、初回インストールが高速で安定します。

各リリースには少なくとも 1 つのアーカイブが含まれ、そのタグ時点のリポジトリ全体が収められます。必要ならビルド済みアーカイブ（後述）やプラットフォーム別アーカイブも添付できます。

更新チェック時には GitHub 上の「latest」リリース（作成時にそのようにマーク）を探します。`--ref=<tag>` で特定リリースを指定した場合はそのバージョンを使用します。

`--pre-release` フラグを付けると latest マークに関係なく最新リリースを取得できるので、全ユーザーに公開する前の検証に便利です。

### カスタムビルド済みアーカイブ

カスタムアーカイブは GitHub リリースのアセットとして添付し、完全な拡張機能を自己完結型で含める必要があります。
[アーカイブ構成](#archive-structure) を参考にしてください。

拡張機能がプラットフォームに依存しない場合、1 つの汎用アセットだけで構いません。
大きなリポジトリ内で拡張機能を開発する場合は、サブディレクトリのみを含むアーカイブを作成してレイアウトを分けることも可能です。

#### プラットフォーム別アーカイブ

Gemini CLI が自動的に適切なアセットを選べるように、以下の命名規則に従ってください。CLI は次の順序で検索します。

1.  **プラットフォーム＋アーキテクチャ**: `{platform}.{arch}.{name}.{extension}`
2.  **プラットフォームのみ**: `{platform}.{name}.{extension}`
3.  **汎用**: アセットが 1 つだけの場合、そのファイルを使用します。

- `{name}`: 拡張機能名。
- `{platform}`: OS。`darwin`（macOS）、`linux`、`win32`（Windows）をサポート。
- `{arch}`: アーキテクチャ。`x64`、`arm64` をサポート。
- `{extension}`: アーカイブの拡張子（例: `.tar.gz`, `.zip`）。

**例**:

- `darwin.arm64.my-tool.tar.gz`（Apple Silicon Mac 向け）
- `darwin.my-tool.tar.gz`（すべての Mac）
- `linux.x64.my-tool.tar.gz`
- `win32.my-tool.zip`

#### アーカイブ構成

アーカイブは完全な拡張機能で、ルートに `gemini-extension.json` が含まれている必要があります。
他のファイル構成も通常の拡張機能と同じです。詳細は [extensions.md](./index.md) を参照してください。

#### GitHub Actions ワークフローの例

以下は複数プラットフォーム向けの Gemini CLI 拡張機能をビルドし、リリースする GitHub Actions の例です。

```yaml
name: Release Extension

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Build extension
        run: npm run build

      - name: Create release assets
        run: |
          npm run package -- --platform=darwin --arch=arm64
          npm run package -- --platform=linux --arch=x64
          npm run package -- --platform=win32 --arch=x64

      - name: Create GitHub Release
        uses: softprops/action-gh-release@v1
        with:
          files: |
            release/darwin.arm64.my-tool.tar.gz
            release/linux.arm64.my-tool.tar.gz
            release/win32.arm64.my-tool.zip
```
