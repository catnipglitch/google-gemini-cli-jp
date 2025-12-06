# ファイルの無視

このドキュメントでは、Gemini CLI の Gemini Ignore (`.geminiignore`) 機能の概要を説明します。

Gemini CLI には、ファイル自動無視機能が含まれています。これは `.gitignore` (Git で使用) および `.aiexclude` (Gemini Code Assist で使用) と同様です。`.geminiignore` ファイルにパスを追加すると、この機能をサポートするツールから除外されますが、他のサービス (Git など) からは引き続き表示されます。

## 仕組み

`.geminiignore` ファイルにパスを追加すると、このファイルを尊重するツールは、一致するファイルとディレクトリを操作から除外します。たとえば、`@` コマンドを使用してファイルを共有する場合、`.geminiignore` ファイル内のパスは自動的に除外されます。

ほとんどの場合、`.geminiignore` は `.gitignore` ファイルの慣例に従います。

- 空行と `#` で始まる行は無視されます。
- 標準の glob パターンがサポートされています（`*`、`?`、`[]` など）。
- 末尾に `/` を付けると、ディレクトリのみが一致します。
- 先頭に `/` を付けると、`.geminiignore` ファイルに対する相対パスが固定されます。
- `!` はパターンを否定します。

`.geminiignore` ファイルはいつでも更新できます。変更を適用するには、Gemini CLI セッションを再起動する必要があります。

## `.geminiignore` の使用方法

`.geminiignore` を有効にするには:

1. プロジェクトディレクトリのルートに `.geminiignore` という名前のファイルを作成します。

ファイルまたはディレクトリを `.geminiignore` に追加するには:

1. `.geminiignore` ファイルを開きます。
2. 無視したいパスまたはファイルを追加します。例: `/archive/` または
   `apikeys.txt`。

### `.geminiignore` の例

`.geminiignore` を使用して、ディレクトリとファイルを無視できます。

```
# /packages/ ディレクトリとすべてのサブディレクトリを除外
/packages/

# apikeys.txt ファイルを除外
apikeys.txt
```

ワイルドカードを `.geminiignore` ファイルで `*` と一緒に使用できます。

```
# すべての .md ファイルを除外
*.md
```

最後に、`!` を使用して除外からファイルとディレクトリを除外できます。

```
# README.md を除くすべての .md ファイルを除外
*.md
!README.md
```

`.geminiignore` ファイルからパスを削除するには、関連する行を削除します。