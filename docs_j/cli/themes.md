# テーマ

Gemini CLI は、配色と外観をカスタマイズするためのさまざまなテーマをサポートしています。`/theme` コマンドまたは `"theme":` 設定を介して、好みに合わせてテーマを変更できます。

## 利用可能なテーマ

Gemini CLI には、事前定義されたテーマがいくつか付属しており、Gemini CLI 内で `/theme` コマンドを使用してリストできます。

- **ダークテーマ:**
  - `ANSI`
  - `Atom One`
  - `Ayu`
  - `Default`
  - `Dracula`
  - `GitHub`
- **ライトテーマ:**
  - `ANSI Light`
  - `Ayu Light`
  - `Default Light`
  - `GitHub Light`
  - `Google Code`
  - `Xcode`

### テーマの変更

1.  Gemini CLI に `/theme` と入力します。
2.  利用可能なテーマを一覧表示するダイアログまたは選択プロンプトが表示されます。
3.  矢印キーを使用してテーマを選択します。一部のインターフェースでは、選択時にライブプレビューまたはハイライトが表示される場合があります。
4.  選択を確認してテーマを適用します。

**注:** `settings.json` ファイルでテーマが定義されている場合（名前またはファイルパスのいずれかで）、`/theme` コマンドを使用してテーマを変更する前に、ファイルから `"theme"` 設定を削除する必要があります。

### テーマの永続性

選択されたテーマは Gemini CLI の[設定](../get-started/configuration.md)に保存されるため、設定はセッション間で記憶されます。

---

## カスタムカラーテーマ

Gemini CLI では、`settings.json` ファイルでカスタムカラーテーマを指定することで、独自のカスタムカラーテーマを作成できます。これにより、CLI で使用されるカラーパレットを完全に制御できます。

### カスタムテーマの定義方法

ユーザー、プロジェクト、またはシステムの `settings.json` ファイルに `customThemes` ブロックを追加します。各カスタムテーマは、一意の名前と色のセットを持つオブジェクトとして定義されます。例:

```json
{
  "ui": {
    "customThemes": {
      "MyCustomTheme": {
        "name": "MyCustomTheme",
        "type": "custom",
        "Background": "#181818",
        ...
      }
    }
  }
}
```

**カラーキー:**

- `Background`
- `Foreground`
- `LightBlue`
- `AccentBlue`
- `AccentPurple`
- `AccentCyan`
- `AccentGreen`
- `AccentYellow`
- `AccentRed`
- `Comment`
- `Gray`
- `DiffAdded` (オプション、差分の追加行用)
- `DiffRemoved` (オプション、差分の削除行用)
- `DiffModified` (オプション、差分の変更行用)

ネストされた `text` オブジェクトを追加することで、個々の UI テキストロールをオーバーライドすることもできます。このオブジェクトは、`primary`、`secondary`、`link`、`accent`、`response` のキーをサポートしています。`text.response` が提供されると、チャットでのモデル応答のレンダリングにおいて `text.primary` よりも優先されます。

**必須プロパティ:**

- `name` (`customThemes` オブジェクトのキーと一致する必要があり、文字列である必要があります)
- `type` (文字列 `"custom"` である必要があります)
- `Background`
- `Foreground`
- `LightBlue`
- `AccentBlue`
- `AccentPurple`
- `AccentCyan`
- `AccentGreen`
- `AccentYellow`
- `AccentRed`
- `Comment`
- `Gray`

任意の色の値には、HEX コード（例: `#FF0000`）**または**標準 CSS カラー名（例: `coral`、`teal`、`blue`）を使用できます。サポートされている名前の完全なリストについては、[CSS カラー名](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value#color_keywords)を参照してください。

`customThemes` オブジェクトにエントリを追加することで、複数のカスタムテーマを定義できます。

### ファイルからのテーマの読み込み

`settings.json` でカスタムテーマを定義するだけでなく、`settings.json` でファイルパスを指定することで、JSON ファイルからテーマを直接読み込むこともできます。これは、テーマを共有したり、メイン設定から分離したりする場合に便利です。

ファイルからテーマを読み込むには、`settings.json` の `theme` プロパティをテーマファイルのパスに設定します。

```json
{
  "ui": {
    "theme": "/path/to/your/theme.json"
  }
}
```

テーマファイルは、`settings.json` で定義されたカスタムテーマと同じ構造に従う有効な JSON ファイルである必要があります。

**例 `my-theme.json`:**

```json
{
  "name": "My File Theme",
  "type": "custom",
  "Background": "#282A36",
  "Foreground": "#F8F8F2",
  "LightBlue": "#82AAFF",
  "AccentBlue": "#61AFEF",
  "AccentPurple": "#BD93F9",
  "AccentCyan": "#8BE9FD",
  "AccentGreen": "#50FA7B",
  "AccentYellow": "#F1FA8C",
  "AccentRed": "#FF5555",
  "Comment": "#6272A4",
  "Gray": "#ABB2BF",
  "DiffAdded": "#A6E3A1",
  "DiffRemoved": "#F38BA8",
  "DiffModified": "#89B4FA",
  "GradientColors": ["#4796E4", "#847ACE", "#C3677F"]
}
```

**セキュリティに関する注意:** 安全のため、Gemini CLI はホームディレクトリ内にあるテーマファイルのみを読み込みます。ホームディレクトリ外からテーマを読み込もうとすると、警告が表示され、テーマは読み込まれません。これは、信頼できないソースから潜在的に悪意のあるテーマファイルを読み込むことを防ぐためです。

### カスタムテーマの例

<img src="./assets/theme-custom.png" alt="カスタムテーマの例" width="600" />

### カスタムテーマの使用

- Gemini CLI で `/theme` コマンドを使用してカスタムテーマを選択します。カスタムテーマはテーマ選択ダイアログに表示されます。
- または、`settings.json` の `ui` オブジェクトに `"theme": "MyCustomTheme"` を追加してデフォルトとして設定します。
- カスタムテーマは、ユーザー、プロジェクト、またはシステムレベルで設定でき、他の設定と同じ[設定の優先順位](../get-started/configuration.md)に従います。

---

## ダークテーマ

### ANSI

<img src="./assets/theme-ansi.png" alt="ANSI テーマ" width="600" />

### Atom OneDark

<img src="./assets/theme-atom-one.png" alt="Atom One テーマ" width="600">

### Ayu

<img src="./assets/theme-ayu.png" alt="Ayu テーマ" width="600">

### Default

<img src="./assets/theme-default.png" alt="Default テーマ" width="600">

### Dracula

<img src="./assets/theme-dracula.png" alt="Dracula テーマ" width="600">

### GitHub

<img src="./assets/theme-github.png" alt="GitHub テーマ" width="600">

## ライトテーマ

### ANSI Light

<img src="./assets/theme-ansi-light.png" alt="ANSI Light テーマ" width="600">

### Ayu Light

<img src="./assets/theme-ayu-light.png" alt="Ayu Light テーマ" width="600">

### Default Light

<img src="./assets/theme-default-light.png" alt="Default Light テーマ" width="600">

### GitHub Light

<img src="./assets/theme-github-light.png" alt="GitHub Light テーマ" width="600">

### Google Code

<img src="./assets/theme-google-light.png" alt="Google Code テーマ" width="600">

### Xcode

<img src="./assets/theme-xcode-light.png" alt="Xcode Light テーマ" width="600">