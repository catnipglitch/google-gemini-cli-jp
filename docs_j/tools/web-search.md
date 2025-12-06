# Web Search ツール (`google_web_search`)

`google_web_search` は Gemini API 経由で Google 検索を実行し、検索結果の要約と引用元を返すツールです。

## 引数

- `query`（文字列, 必須）: 検索クエリ。

## 利用例

```
google_web_search(query="latest advancements in AI-powered code generation")
```

CLI は Gemini API へクエリを送り、結果の要約（生のリストではなく）とソースを含む応答を受け取ります。

## 補足

- 出力は加工済みの要約で、生のリンク一覧ではありません。
- 応答には使用したソースへの引用が付属します。
