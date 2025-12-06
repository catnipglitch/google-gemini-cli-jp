# Web Fetch ツール (`web_fetch`)

`web_fetch` は Web ページの要約や比較、情報抽出を行うツールで、最大 20 件の URL を含むプロンプトを処理します。

## 引数

- `prompt`（文字列, 必須）: URL（`http://` / `https://` で始まる）と具体的な指示を含む自然文。例: `Summarize https://example.com/article …`。

## 利用方法

URL を含むプロンプトを投げると、CLI は URL をフェッチする確認を表示し、承認後に Gemini API の `urlContext` で処理し、ソース表記付きの応答を返します。API からアクセスできない場合、ローカルマシン経由で取得を試みます。

```
web_fetch(prompt="Summarize https://example.com/news/latest")
```

## 活用例

- 単一記事を要約
- 2 件の論文の結論を比較：`web_fetch(prompt="…arxiv.org/abs/2401.0001…2401.0002…")`

## 補足

- URL の処理能力は Gemini API に依存。
- 出力の品質はプロンプトの明確さに比例。
