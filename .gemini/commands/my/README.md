# カスタムコマンド (My Commands)

このディレクトリ (`.gemini/commands/my/`) には、個人のワークフローを効率化するためのカスタムコマンド定義ファイル (`.toml`) が格納されています。

各コマンドは `/my:<コマンド名>` の形式で呼び出せます。
詳細は各 `.toml` ファイルを参照してください。

| コマンド名 | ファイル名 | 機能概要 | 引数 |
| :--- | :--- | :--- | :--- |
| `/my:ask-docs` | `ask-docs.toml` | ローカルドキュメントを検索して質問に回答 | `<質問>` |
| `/my:ask-gemini-cli` | `ask-gemini-cli.toml` | ドキュメントを全読み込みし、対話形式でCLIについて回答 | なし (対話開始) |
| `/my:commit` | `commit.toml` | ステージング済みの変更からコミットメッセージを生成 | なし |
| `/my:proof` | `proof.toml` | ドキュメントの校正（誤字・表記ゆれ・Markdown修正） | `<ファイルパス>` |
| `/my:test` | `test_args.toml` | 引数テスト用コマンド | `<ファイルパス>` |
| `/my:trans_ask` | `trans_ask.toml` | 対話形式で翻訳対象と要望を確認し翻訳を実行 | なし (対話開始) |
| `/my:trans_ja_copy` | `trans_ja_copy.toml` | 日本語翻訳し、別ファイル(`_j`/`_J`)として保存 | `<ファイルパス>` |
| `/my:trans_ja_inplace` | `trans_ja_inplace.toml` | 日本語翻訳し、元のファイルを上書き更新 | `<ファイルパス>` |
| `/my:weekly-news` | `weekly-news.toml` | 指定トピックの週間ニュースを収集しファイル出力 | `<トピック>` |

## 使い方

Gemini CLI 上でコマンドを入力して実行します。

例:
```bash
/my:commit
/my:proof README.md
/my:weekly-news "Gemini CLI"
```
