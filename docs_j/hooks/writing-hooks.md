# Gemini CLI フックの作成

このガイドでは、単純なログ出力フックから高度なワークフローアシスタントまでの作り方を紹介します。

## 前提条件

- Gemini CLI をインストール・設定済み
- シェルスクリプト、あるいは Node.js での作業経験
- JSON 入出力の理解

## クイックスタート: ログ記録フック

1. `.gemini/hooks` ディレクトリとスクリプトを作成。
   ```bash
   mkdir -p .gemini/hooks
   cat <<'EOF' >.gemini/hooks/log-tools.sh
   #!/usr/bin/env bash
   input=$(cat)
   tool_name=$(echo "$input" | jq -r '.tool_name')
   echo "[$(date)] Tool executed: $tool_name" >> .gemini/tool-log.txt
   echo "Logged: $tool_name"
   EOF
   chmod +x .gemini/hooks/log-tools.sh
   ```
2. `.gemini/settings.json` へ `AfterTool` フックを追加し、すべてのツール名をログ。
3. CLI を走らせると `.gemini/tool-log.txt` に履歴が蓄積されます。

## 実践的な例

### 秘密情報の検出
- `BeforeTool` で `WriteFile`, `Edit` を読み取り、`api_key`, `password` などにマッチする場合は `exit 2` で拒否。
- `settings.json` の `hooks.disabled` ではなく `hooks` で `command` フックを定義。

### コード変更後に自動テスト
- TypeScript ファイルの変更後、対応する `.test.ts` を `npx vitest run` で実行。成功/失敗を stdout でレポート。

### コンテキスト注入
- `BeforeAgent` で `git log -5` を取得し、`hookSpecificOutput` で追加文脈として返却。過去のコミットからプロジェクト意図が共有可能。

## 上級的な機能

### RAG ベースのツール選択
`BeforeToolSelection` で現在のリクエストからキーワードを抽出し、意味的に関連する 15 個程度のツールに制限。これによりモデルの混乱を減らし、応答が高速化。

### セッションを跨いだメモリ
- `SessionStart`: 過去の学習済みメモリを読み込み
- `AfterModel`: 重要なやり取りを JSON ログ化
- `SessionEnd`: LLM で要約し、ChromaDB へ格納

### フックのチェイン
同一イベント内で複数フックを定義でき、それぞれの出力を次に渡して複雑なパイプラインを構築。

## 総合例: スマート開発アシスタント

- RAG により 100+ ツールを ~15 個へフィルタリング
- セッションを跨いで学習（memories）
- `SessionStart` → `BeforeAgent` → `BeforeModel` → `BeforeToolSelection` → `BeforeTool` → `AfterTool` → `AfterModel` → `SessionEnd` を連携

必要なスクリプト（`init.js`, `inject-memories.js`, `rag-filter.js`, `security.js`, `auto-test.js`, `record.js`, `consolidate.js`）は Node.js で記述し、`./.gemini/hooks/*.js` から呼び出すだけ。

### 実装のポイント
- `chromadb` + `GoogleGenAI` でメモリと埋め込みを管理。
- `BeforeToolSelection` の許可ツールは `allowedFunctionNames` を絞り込み。
- セキュリティは正規表現で API キーやシークレットを検知。
- テストは `npx vitest run` を `execSync` で呼び出し結果を stdout に書き込む。

## まとめ

- `/hooks panel` で登録フックを確認。
- `/hooks enable/disable` で制御、`hooks.disabled` で恒久停止。
- Claude Code から `gemini hooks migrate --from-claude` で移行可能。
- 他ドキュメント: [index](index.md)、[best practices](best-practices.md)、[custom commands](../cli/custom-commands.md)、[configuration](../cli/configuration.md)
