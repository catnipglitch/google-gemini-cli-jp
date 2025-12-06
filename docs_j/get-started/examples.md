# Gemini CLI の活用例

Gemini CLI を使い始めるにはどこから？このドキュメントでは実際のユースケースを紹介します。

> **注:** 以下の結果は例示です。実際の出力は入力や環境により異なります。

## 写真を内容でリネーム

`photos/` に `photo1.png`, `photo2.png`, `photo3.png` があるとします。

```cli
Rename the photos in my "photos" directory based on their contents.
```

CLI はファイルリネームの許可を求め、承認すると内容に応じた名前に変更します。たとえば `yellow_flowers.png` など。

## リポジトリのコードを読んで説明

`chalk` リポジトリをクローンし、重要ファイルを読んで仕組みを説明させる例:

```cli
Clone the 'chalk' repository from https://github.com/chalk/chalk, read its key source files, and explain how it works.
```

Gemini CLI は `git clone`、ファイル読み取りを順に許可を求め、最終的にソース分析から要点を要約します。

実際の応答では、チェーン可能 API や ANSI エスケープの組立、`toString()` によるスタイル出力などが整理されて提示されます。

## 2 つのスプレッドシートを統合

`Revenue - 2023.csv` と `Revenue - 2024.csv` を結合して年ごとの列を持つ CSV を作る例:

```cli
Combine the two .csv files into a single .csv file, with each year a different column.
```

Gemini CLI は両ファイルを読み取り、書き込み許可を得て次のような CSV を作成します。

```csv
Month,2023,2024
January,0,1000
February,0,1200
March,0,2400
April,900,500
May,1000,800
June,1000,900
July,1200,1000
August,1800,400
September,2000,2000
October,2400,3400
November,3400,1800
December,2100,9000
```

## ユニットテストの作成

`Login.js` に対するテストを書く例:

```cli
Write unit tests for Login.js.
```

Gemini CLI は新規ファイルの作成許可を求め、`testing-library/react` や `jest` を使ってフォーム描画、エラー表示、正常ログイン、送信中の無効化などを検証するテストを生成します。

```javascript
// 省略: Login コンポーネントのレンダリング、フォーム操作、モック API など
```

これらの例を参考に、独自のプロンプトやファイル構成へ応用できます。
