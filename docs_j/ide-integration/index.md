# IDE 統合

Gemini CLI は IDE との統合により、ワークスペースを把握しネイティブな差分表示などの高度な機能を提供します。対応 IDE は [Antigravity](https://antigravity.google)、[Visual Studio Code](https://code.visualstudio.com/) 及び VS Code 拡張をサポートするエディタです。その他のエディタ対応は [IDE Companion Extension Spec](./ide-companion-spec.md) を参照。

## 機能

- **ワークスペース文脈:** CLI は最近開いた 10 ファイル、カーソル位置、選択済みテキスト（16KB 上限）を把握。
- **ネイティブな差分表示:** 編集提案を IDE に差分ビューで表示し、承認・修正・拒否が可能。
- **VS Code コマンド:** `Cmd+Shift+P`/`Ctrl+Shift+P` から `Gemini CLI: Run`, `Accept Diff`, `Close Diff Editor`, `View Third-Party Notices` を利用。

## セットアップ

1. **自動通知（推奨）:** 対応エディタの統合ターミナルで CLI を起動すると接続案内が表示され、「はい」で拡張機能のインストールと接続を自動実行。
2. **CLI から手動インストール:** `gemini` 内から `/ide install` を実行すると自動で適切な拡張機能を導入。
3. **マーケットプレイスから手動インストール:**
   - VS Code: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=google.gemini-cli-vscode-ide-companion)
   - VS Code フォーク: [Open VSX Registry](https://open-vsx.org/extension/google/gemini-cli-vscode-ide-companion)
   手動インストール後は CLI で `/ide enable` を実行して連携を有効化する。

## 利用方法

- 接続を有効化: `/ide enable`
- 無効化: `/ide disable`
- ステータス確認: `/ide status`（接続先 IDE、認識済みファイル 10 件などを表示）。

## 差分の扱い

- **承認:** チェックマークアイコン、保存、`Gemini CLI: Accept Diff`、CLI で `yes` を入力。
- **拒否:** '×' アイコン、タブを閉じる、`Gemini CLI: Close Diff Editor`、CLI で `no` を入力。
- 差分ビュー内で直接変更し、再承認のフローにも対応。
- CLI で “Yes, allow always” を選ぶと IDE 側で自動承認され差分が表示されなくなる。

## サンドボックス下での注意

- **macOS:** IDE 拡張との通信にネットワークアクセスが必要なため、Seatbelt プロファイルでネットワークが許可されていること。
- **Docker/Podman:** CLI コンテナから `host.docker.internal` 経由でホスト上の拡張に接続。通常追加設定不要だが、ネットワーク構成でホストへの接続が許可されているか確認。

## トラブルシューティング

### 接続エラー

- `Failed to connect to IDE companion extension` → 拡張機能が起動していない可能性。IDE で拡張を有効化し、新しいターミナルで CLI を起動。
- `IDE connection error` → `/ide enable` による再接続。継続する場合は IDE を再起動。

### 設定エラー

- `Directory mismatch` → CLI の作業ディレクトリが IDE の workspace と一致していない。IDE で開いているディレクトリへ移動して再起動。
- `Open a workspace folder` → IDE でワークスペースを開き、再度 CLI を起動。

### 一般エラー

- `IDE integration is not supported` → 対応外環境（非 IDE ターミナル）で実行中。Antigravity や VS Code の統合ターミナルで起動。
- `No installer is available` → `/ide install` で自動インストールできない IDE の場合はマーケットプレイスから手動インストール（[手動インストール節](#3-manual-installation-from-a-marketplace) を参照）。
