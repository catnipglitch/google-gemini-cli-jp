# よくある質問 (FAQ)

このページでは、Gemini CLI の使用中に遭遇する一般的な質問への回答と、よくある問題の解決策を提供します。

## 一般的な問題

### `API error: 429 - Resource exhausted` というエラーが表示されるのはなぜですか？

このエラーは、API リクエスト制限を超過したことを示します。Gemini API には、乱用を防ぎ、公正な利用を保証するためのレート制限があります。

これを解決するには、次のことができます。

- **使用状況を確認する:** Google AI Studio または Google Cloud プロジェクトダッシュボードで API 使用状況を確認します。
- **プロンプトを最適化する:** 短期間に多くのリクエストを行っている場合は、プロンプトをバッチ処理するか、リクエスト間に遅延を導入してみてください。
- **割り当て増加をリクエストする:** 継続的に高い制限が必要な場合は、Google に割り当て増加をリクエストできます。

### `npm run start` を実行すると `ERR_REQUIRE_ESM` エラーが発生するのはなぜですか？

このエラーは通常、Node.js プロジェクトで CommonJS と ES Modules の間に不一致がある場合に発生します。

これは多くの場合、`package.json` または `tsconfig.json` の設定ミスが原因です。次のことを確認してください。

1.  `package.json` に `"type": "module"` がある。
2.  `tsconfig.json` の `compilerOptions` に `"module": "NodeNext"` または互換性のある設定がある。

問題が解決しない場合は、`node_modules` ディレクトリと `package-lock.json` ファイルを削除し、`package-lock.json` ファイルを削除し、`npm install` を再度実行してみてください。

### 統計出力にキャッシュされたトークンカウントが表示されないのはなぜですか？

キャッシュされたトークン情報は、キャッシュされたトークンが使用されている場合にのみ表示されます。この機能は API キーユーザー（Gemini API キーまたは Google Cloud Vertex AI）には利用できますが、OAuth ユーザー（Google Gmail や Google Workspace などの Google 個人/企業アカウントなど）には利用できません。これは、Gemini Code Assist API がキャッシュされたコンテンツの作成をサポートしていないためです。Gemini CLI の `/stats` コマンドを使用して、総トークン使用量を表示することはできます。

## インストールと更新

### Gemini CLI を最新バージョンに更新する方法は？

`npm` を介してグローバルにインストールした場合は、`npm install -g @google/gemini-cli@latest` コマンドを使用して更新します。ソースからコンパイルした場合は、リポジリから最新の変更をプルし、`npm run build` コマンドを使用して再ビルドします。

## プラットフォーム固有の問題

### `chmod +x` のようなコマンドを実行すると、Windows で CLI がクラッシュするのはなぜですか？

`chmod` のようなコマンドは、Unix 系オペレーティングシステム（Linux、macOS）に固有のものです。Windows ではデフォルトで利用できません。

これを解決するには、次のことができます。

- **Windows の同等コマンドを使用する:** `chmod` の代わりに、Windows でファイルのアクセス許可を変更するために `icacls` を使用できます。
- **互換性レイヤーを使用する:** Git Bash や Windows Subsystem for Linux (WSL) などのツールは、これらのコマンドが機能する Unix 系環境を Windows で提供します。

## 設定

### `GOOGLE_CLOUD_PROJECT` を構成するにはどうすればよいですか？

環境変数を使用して Google Cloud プロジェクト ID を構成できます。

シェルで `GOOGLE_CLOUD_PROJECT` 環境変数を設定します。

```bash
export GOOGLE_CLOUD_PROJECT="your-project-id"
```

この設定を永続化するには、この行をシェルの起動ファイル（例: `~/.bashrc`、`~/.zshrc`）に追加します。

### API キーを安全に保存する最善の方法は何ですか？

スクリプトで API キーを公開したり、ソース管理にチェックインしたりすることは、セキュリティリスクです。

API キーを安全に保存するには、次のことができます。

- **`.env` ファイルを使用する:** プロジェクトの `.gemini` ディレクトリ（`.gemini/.env`）に `.env` ファイルを作成し、そこにキーを保存します。Gemini CLI はこれらの変数を自動的にロードします。
- **システムのキーリングを使用する:** 最も安全なストレージには、オペレーティングシステムのシークレット管理ツール（macOS キーチェーン、Windows 資格情報マネージャー、または Linux のシークレットマネージャーなど）を使用します。その後、スクリプトまたは環境で実行時に安全なストレージからキーをロードします。

### Gemini CLI の構成ファイルと設定ファイルはどこに保存されますか？

Gemini CLI の構成は、2つの `settings.json` ファイルに保存されます。

1.  ホームディレクトリ: `~/.gemini/settings.json`。
2.  プロジェクトのルートディレクトリ: `./.gemini/settings.json`。

詳細については、[Gemini CLI 構成](./get-started/configuration.md)を参照してください。

## Google AI Pro/Ultra およびサブスクリプション FAQ

### Google AI Pro または Google AI Ultra サブスクリプションについて詳しく知るにはどうすればよいですか？

Google AI Pro または Google AI Ultra サブスクリプションの詳細については、[サブスクリプション設定](https://one.google.com)で **サブスクリプションの管理** にアクセスしてください。

### Google AI Pro または Ultra の制限が引き上げられているかどうかを知るにはどうすればよいですか？

Google AI Pro または Ultra に加入している場合、Gemini Code Assist と Gemini CLI の制限は自動的に引き上げられます。これらは Gemini CLI と IDE のエージェントモードで共有される割り当てです。割り当てが引き上げられていることを確認するには、[サブスクリプション設定](https://one.google.com)で Google AI Pro または Ultra にまだ加入しているかどうかを確認します。

### Google AI Pro または Ultra に加入している場合、Gemini Code Assist または Gemini CLI を使用する際のプライバシーポリシーはどのようになりますか？

サブスクリプションによって管理されるプライバシーポリシーと利用規約の詳細については、[Gemini Code Assist: 利用規約とプライバシーポリシー](https://developers.google.com/gemini-code-assist/resources/privacy-notices)をご覧ください。

### Google AI Pro または Ultra にアップグレードしましたが、まだ割り当て制限に達していると表示されます。これはバグですか？

Google AI Pro または Ultra サブスクリプションの制限の引き上げは、Gemini 2.5 Pro と Flash の両方で Gemini 2.5 に適用されます。これらは Gemini CLI と Gemini Code Assist IDE 拡張機能のエージェントモードで共有される割り当てです。Gemini CLI、Gemini Code Assist、および Gemini Code Assist のエージェントモードの割り当て制限の詳細については、[割り当てと制限](https://developers.google.com/gemini-code-assist/resources/quotas)をご覧ください。

### Google AI Pro または Ultra サブスクリプションを購入して Gemini CLI と Gemini Code Assist の制限を引き上げた場合、Gemini は機械学習モデルを改善するために私のデータを使用し始めますか？

有料プランを購入した場合、Google は機械学習モデルを改善するためにあなたのデータを使用しません。注: Gemini Code Assist の無料版である個人向け Gemini Code Assist を引き続き使用する場合でも、Google の機械学習モデルを改善するためにあなたのデータを使用しないようにオプトアウトできます。詳細については、[個人向け Gemini Code Assist プライバシー通知](https://developers.google.com/gemini-code-assist/resources/privacy-notice-gemini-code-assist-individuals)をご覧ください。

## 質問が見つかりませんか？

[GitHub の Gemini CLI Q&A ディスカッション](https://github.com/google-gemini/gemini-cli/discussions/categories/q-a)を検索するか、[GitHub で新しいディスカッションを開始](https://github.com/google-gemini/gemini-cli/discussions/new?category=q-a)してください。