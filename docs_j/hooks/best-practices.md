# Gemini CLI フック：ベストプラクティス

このガイドでは、セキュリティ・パフォーマンス・デバッグ・プライバシーの観点からフックを運用する際の留意点をまとめます。

## セキュリティ

- **入力検証:** 受け取る JSON は `jq` などで構造を確認し、`tool_name` や `tool_input` が存在するか確認する。
- **タイムアウト:** すべてのフックに適切なタイムアウトを設定（例: 短い検証は 1-5 秒、ネットワークは 10-30 秒、重い処理は 30-60 秒）。
- **最小権限:** root として実行せず、書き込み前にはパスの書き込み権限を確認。
- **シークレット検出:** `BeforeTool` で API キーやパスワードを示す正規表現を使って拒否。
- **外部スクリプトのレビュー:** third-party フックは `curl|wget|ssh|eval` などを検査し、`git status` で信頼性を確認。疑わしければ Docker などでサンドボックス化して実行。

## パフォーマンス

- **高速化:** フックは同期実行なので `Promise.all` などで並列処理。シリアルは遅延源。
- **キャッシュ:** 計算コストの高い処理は `.gemini/hook-cache.json` 等へキャッシュしクエリ時間を短縮。
- **適切なイベント選択:** 例: 終了時処理は `AfterAgent`、モデルごとのフックは `AfterModel` など。不要な繰り返し呼び出しを避ける。
- **matcher の絞り込み:** `WriteFile|Edit` など具体的なツール名のみ対象にして無駄な呼び出しを減らす。
- **JSON 処理:** 大きい入力は `JSONStream` でストリーム処理しメモリを節約。

## デバッグ

- **ログ出力:** `.gemini/hooks/debug.log` へタイムスタンプ付きでログを書き、後から追跡。
- **stderr でエラー:** `process.exit(2)` や `>&2` でブロッキングエラーを示し、CLI に理由を通知。
- **単体テスト:** サンプル JSON (`test-input.json`) を作って `cat test-input.json | .gemini/hooks/hook.sh` で事前に試験。
- **終了コード:** 成功は `exit 0`、拒否は `exit 2`。`set -e` を使う場合は注意。
- **テレメトリ:** `telemetry.logPrompts: true` でフックデータがログされるので、ログを監視。
- **/hooks panel:** このコマンドで実行結果、成功/失敗、時間、出力を確認。

## 開発上の留意点

- **簡単な出力から開始:** まず入力をファイルへ書き出すだけのフックで構造を把握。
- **JSON ライブラリ利用:** `jq` や `JSON.parse` で堅牢に解析。`grep` に頼らない。
- **実行権限:** `chmod +x .gemini/hooks/*.sh` を忘れず。
- **バージョン管理:** `.gemini/hooks` や `.gemini/settings.json` を Git に含めて共有。ログや一時ファイルは `.gitignore` へ。
- **説明を添える:** `settings.json` の `description` でフックの目的を明示し、スクリプト内にもコメントを追加。

## トラブルシューティング

- `/hooks panel` でフック一覧と最新出力を確認。フック名・ステータス・エラーを見る。
- マッチャーの正規表現を手動でテストし、期待通りに動作しているか確認。
- `hooks.disabled` で名前が登録されていないか、`chmod` で実行権限があるかを確認。
- スクリプトがタイムアウトする場合は `timeout` を延ばすか処理の並列化、キャッシュ、分割を考慮。
- JSON 出力は `jq` で検証し、制御文字を除去。`
- 環境変数がない場合は `env > .gemini/hooks/env.log` で確認。

## プライバシーとデータ収集

- `telemetry.logPrompts` が true のときは入力/出力がテレメトリに含まれる。企業向けに無効化 (`telemetry.logPrompts: false` または `GEMINI_TELEMETRY_LOG_PROMPTS=false`) 可能。
- 敏感データを扱う場合は:
  1. ログ化を最小限に。
  2. 出力前に `client.apiKey` などを削除・マスク。
  3. 保存先を暗号化。
  4. 実行権限を厳しく制限。

## 参考資料

- [Hooks Reference](index.md)
- [Writing Hooks](writing-hooks.md)
- [Configuration](../cli/configuration.md)
- [Hooks Design Document](../hooks-design.md)
