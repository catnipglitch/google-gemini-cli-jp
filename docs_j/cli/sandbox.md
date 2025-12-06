# Gemini CLI のサンドボックス

このドキュメントは、前提条件、クイックスタート、設定を含む Gemini CLI のサンドボックスに関するガイドを提供します。

## 前提条件

サンドボックスを使用する前に、Gemini CLI をインストールして設定する必要があります。

```bash
npm install -g @google/gemini-cli
```

インストールを確認するには

```bash
gemini --version
```

## サンドボックスの概要

サンドボックスは、潜在的に危険な操作（シェルコマンドやファイル変更など）をホストシステムから隔離し、AI 操作と環境の間にセキュリティ境界を提供します。

サンドボックスの利点には以下が含まれます。

- **セキュリティ**: 偶発的なシステム損傷やデータ損失を防ぎます。
- **隔離**: ファイルシステムアクセスをプロジェクトディレクトリに制限します。
- **一貫性**: 異なるシステム間で再現可能な環境を保証します。
- **安全性**: 信頼できないコードや実験的なコマンドで作業する際のリスクを軽減します。

## サンドボックスの方法

サンドボックスの理想的な方法は、プラットフォームと好みのコンテナソリューションによって異なる場合があります。

### 1. macOS Seatbelt (macOS のみ)

`sandbox-exec` を使用した軽量な組み込みサンドボックス。

**デフォルトプロファイル**: `permissive-open` - プロジェクトディレクトリ外への書き込みを制限しますが、ほとんどの他の操作を許可します。

### 2. コンテナベース (Docker/Podman)

完全なプロセス隔離を備えたクロスプラットフォームのサンドボックス。

**注**: サンドボックスイメージをローカルでビルドするか、組織のレジストリから公開されたイメージを使用する必要があります。

## クイックスタート

```bash
# コマンドフラグでサンドボックスを有効にする
gemini -s -p "analyze the code structure"

# 環境変数を使用する
export GEMINI_SANDBOX=true
gemini -p "run the test suite"

# settings.json で構成する
{
  "tools": {
    "sandbox": "docker"
  }
}
```

## 設定

### サンドボックスを有効にする (優先順位順)

1.  **コマンドフラグ**: `-s` または `--sandbox`
2.  **環境変数**: `GEMINI_SANDBOX=true|docker|podman|sandbox-exec`
3.  **設定ファイル**: `settings.json` ファイルの `tools` オブジェクト内の `"sandbox": true`（例: `{"tools": {"sandbox": true}}`）。

### macOS Seatbelt プロファイル

組み込みプロファイル（`SEATBELT_PROFILE` 環境変数経由で設定）：

- `permissive-open` (デフォルト): 書き込み制限あり、ネットワーク許可
- `permissive-closed`: 書き込み制限あり、ネットワークなし
- `permissive-proxied`: 書き込み制限あり、プロキシ経由でネットワーク
- `restrictive-open`: 厳密な制限あり、ネットワーク許可
- `restrictive-closed`: 最大限の制限あり

### カスタムサンドボックスフラグ

コンテナベースのサンドボックスの場合、`SANDBOX_FLAGS` 環境変数を使用して、`docker` または `podman` コマンドにカスタムフラグを挿入できます。これは、特定のユースケースのセキュリティ機能を無効にするなどの高度な構成に役立ちます。

**例 (Podman)**:

ボリュームマウントの SELinux ラベリングを無効にするには、次のように設定できます。

```bash
export SANDBOX_FLAGS="--security-opt label=disable"
```

複数のフラグは、スペース区切りの文字列として提供できます。

```bash
export SANDBOX_FLAGS="--flag1 --flag2=value"
```

## Linux UID/GID 処理

サンドボックスは、Linux 上のユーザー権限を自動的に処理します。これらの権限をオーバーライドするには、次を使用します。

```bash
export SANDBOX_SET_UID_GID=true   # ホスト UID/GID を強制する
export SANDBOX_SET_UID_GID=false  # UID/GID マッピングを無効にする
```

## トラブルシューティング

### よくある問題

**「操作が許可されていません」**

- サンドボックス外へのアクセスを必要とする操作。
- より許容的なプロファイルを試すか、マウントポイントを追加します。

**コマンドが見つかりません**

- カスタム Dockerfile に追加します。
- `sandbox.bashrc` 経由でインストールします。

**ネットワークの問題**

- サンドボックスプロファイルがネットワークを許可していることを確認します。
- プロキシ設定を確認します。

### デバッグモード

```bash
DEBUG=1 gemini -s -p "debug command"
```

**注:** プロジェクトの `.env` ファイルに `DEBUG=true` がある場合、自動的に除外されるため gemini-cli には影響しません。gemini-cli 固有のデバッグ設定には `.gemini/.env` ファイルを使用してください。

### サンドボックスの検査

```bash
# 環境を確認する
gemini -s -p "run shell command: env | grep SANDBOX"

# マウントを一覧表示する
gemini -s -p "run shell command: mount | grep workspace"
```

## セキュリティに関する注意

- サンドボックスはすべてのリスクを軽減しますが、排除するわけではありません。
- 作業を許可する最も制限の厳しいプロファイルを使用してください。
- 最初のビルド後、コンテナのオーバーヘッドは最小限です。
- GUI アプリケーションはサンドボックスでは機能しない場合があります。

## 関連ドキュメント

- [設定](../get-started/configuration.md): すべての設定オプション。
- [コマンド](./commands.md): 利用可能なコマンド。
- [トラブルシューティング](../troubleshooting.md): 一般的なトラブルシューティング。