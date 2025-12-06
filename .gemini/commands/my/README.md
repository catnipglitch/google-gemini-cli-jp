# My Custom Commands

このディレクトリ (`.gemini/commands/my/`) には、個人のワークフローを効率化するためのカスタムコマンド定義ファイル (`.toml`) が格納されています。

各コマンドは `/my:<コマンド名>` の形式で呼び出せます。
各コマンドの詳細な日本語説明は、同名の `.md` ファイルを参照してください。

| コマンド名 | ファイル名 | 機能概要 | 引数 | 参考和訳 |
| :--- | :--- | :--- | :--- | :--- |
| `/my:ask-docs` | `ask-docs.toml` | ローカルドキュメントを検索して質問に回答 | `<質問>` | [ask-docs.md](ask-docs.md) |
| `/my:ask-gemini-cli` | `ask-gemini-cli.toml` | ドキュメントを全読み込みし、対話形式でCLIについて回答 | なし | [ask-gemini-cli.md](ask-gemini-cli.md) |
| `/my:ch` | `ch.toml` | Plan Mode / Edit Mode の切り替えと振る舞いの変更 | `plan` / `edit` | [ch.md](ch.md) |
| `/my:commit` | `commit.toml` | ステージング済みの変更からコミットメッセージを生成 | なし | [commit.md](commit.md) |
| `/my:proof` | `proof.toml` | ドキュメントの校正（誤字・表記ゆれ・Markdown修正） | `<ファイルパス>` | [proof.md](proof.md) |
| `/my:test` | `test-args.toml` | 引数テスト用コマンド | `<ファイルパス>` | [test-args.md](test-args.md) |
| `/my:trans-ask` | `trans-ask.toml` | 対話形式で翻訳対象と要望を確認し翻訳を実行 | なし | [trans-ask.md](trans-ask.md) |
| `/my:trans-ja-copy` | `trans-ja-copy.toml` | 日本語翻訳し、別ファイル(`_j`/`_J`)として保存 | `<ファイルパス>` | [trans-ja-copy.md](trans-ja-copy.md) |
| `/my:trans-ja-inplace` | `trans-ja-inplace.toml` | 日本語翻訳し、元のファイルを上書き更新 | `<ファイルパス>` | [trans-ja-inplace.md](trans-ja-inplace.md) |
| `/my:weekly-news` | `weekly-news.toml` | 指定トピックの週間ニュースを収集しファイル出力 | `<トピック>` | [weekly-news.md](weekly-news.md) |

## 使い方

Gemini CLI 上でコマンドを入力して実行します。

例:
```bash
/my:commit
/my:proof README.md
/my:weekly-news "Gemini CLI"
```
