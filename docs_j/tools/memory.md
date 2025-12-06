# Memory ツール (`save_memory`)

`save_memory` は重要な情報をセッション間で記憶し、カスタマイズされた応答に活用します。

## 引数

- `fact`（文字列, 必須）: 自然言語で完結に記述された記憶したい事柄。

## 使い方

事実は `~/.gemini/GEMINI.md`（別名可能）に `## Gemini Added Memories` セクションとして追加され、次回以降のセッションで文脈として読み込まれます。

```
save_memory(fact="My preferred programming language is Python.")
```

### 利用例

- 好みの言語を記憶
- 現在取り組んでいるプロジェクト名を保存

## 補足

- 簡潔な事実向けで、会話履歴や大量データの保存には使わない。
- メモリファイルは Markdown なので手動編集も可能。
