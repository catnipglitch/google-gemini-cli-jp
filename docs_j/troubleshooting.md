# トラブルシューティングガイド

このガイドでは、一般的な問題の解決策とデバッグのヒントを提供します。以下のトピックが含まれます。

- 認証またはログインエラー
- よくある質問（FAQ）
- デバッグのヒント
- 既存の類似するGitHub Issue、または新しいIssueの作成

## 認証またはログインエラー

- **エラー:
  `You must be a named user on your organization's Gemini Code Assist Standard edition subscription to use this service. Please contact your administrator to request an entitlement to Gemini Code Assist Standard edition.`**
  - **原因:** このエラーは、Gemini CLIが `GOOGLE_CLOUD_PROJECT` または `GOOGLE_CLOUD_PROJECT_ID` 環境変数が定義されていることを検出した場合に発生する可能性があります。これらの変数を設定すると、組織のサブスクリプションチェックが強制されます。これは、個人のGoogleアカウントが組織のサブスクリプションにリンクされていない場合に問題となる可能性があります。

  - **解決策:**
    - **個人ユーザー:** `GOOGLE_CLOUD_PROJECT` および `GOOGLE_CLOUD_PROJECT_ID` 環境変数を設定解除します。これらの変数をシェル設定ファイル（例: `.bashrc`、`.zshrc`）およびすべての `.env` ファイルから確認し、削除してください。これで問題が解決しない場合は、別のGoogleアカウントの使用を試してください。

    - **組織ユーザー:** 組織のGoogle Cloud管理者にお問い合わせいただき、組織のGemini Code Assistサブスクリプションに追加してもらうよう依頼してください。

- **エラー: `Failed to login. Message: Request contains an invalid argument`**
  - **原因:** Google WorkspaceアカウントまたはGmailアカウントに関連付けられたGoogle Cloudアカウントを持つユーザーは、Google Code Assistプランの無料枠をアクティブ化できない場合があります。
  - **解決策:** Google Cloudアカウントの場合、`GOOGLE_CLOUD_PROJECT` をプロジェクトIDに設定することで回避できます。または、[Google AI Studio](http://aistudio.google.com/app/apikey)からGemini APIキーを取得することもできます。これには別の無料枠も含まれています。

- **エラー: `UNABLE_TO_GET_ISSUER_CERT_LOCALLY` または
  `unable to get local issuer certificate`**
  - **原因:** SSL/TLSトラフィックを傍受および検査するファイアウォールを備えた企業ネットワークにいる可能性があります。これは通常、カスタムルートCA証明書をNode.jsが信頼する必要があることを意味します。
  - **解決策:** `NODE_EXTRA_CA_CERTS` 環境変数に企業ルートCA証明書ファイルの絶対パスを設定してください。
    - 例: `export NODE_EXTRA_CA_CERTS=/path/to/your/corporate-ca.crt`

## 一般的なエラーメッセージと解決策

- **エラー: MCPサーバー起動時に `EADDRINUSE` (アドレスが既に使用中) が発生します。**
  - **原因:** MCPサーバーがバインドしようとしているポートを、別のプロセスが既に使用しています。
  - **解決策:** ポートを使用している別のプロセスを停止するか、MCPサーバーが別のポートを使用するように設定してください。

- **エラー: `gemini` でGemini CLIを実行しようとすると、コマンドが見つかりません。**
  - **原因:** Gemini CLIが正しくインストールされていないか、システムの `PATH` に含まれていません。
  - **解決策:** 更新は、Gemini CLIのインストール方法によって異なります。
    - `gemini` をグローバルにインストールした場合、`npm` のグローバルバイナリディレクトリが `PATH` に含まれていることを確認してください。`npm install -g @google/gemini-cli@latest` コマンドを使用してGemini CLIを更新できます。
    - ソースから `gemini` を実行している場合、正しいコマンドを使用して呼び出していることを確認してください（例: `node packages/cli/dist/index.js ...`）。Gemini CLIを更新するには、リポジトリから最新の変更をプルし、`npm run build` コマンドを使用して再ビルドしてください。

- **エラー: `MODULE_NOT_FOUND` またはインポートエラー。**
  - **原因:** 依存関係が正しくインストールされていないか、プロジェクトがビルドされていません。
  - **解決策:**
    1.  `npm install` を実行して、すべての依存関係が存在することを確認します。
    2.  `npm run build` を実行して、プロジェクトをコンパイルします。
    3.  `npm run start` でビルドが正常に完了したことを確認します。

- **エラー: 「Operation not permitted」、「Permission denied」など。**
  - **原因:** サンドボックスが有効になっている場合、Gemini CLIは、プロジェクトディレクトリまたはシステム一時ディレクトリ外への書き込みなど、サンドボックス構成によって制限されている操作を試行する可能性があります。
  - **解決策:** サンドボックス構成のカスタマイズ方法を含む詳細については、[Configuration: Sandboxing](./cli/sandbox.md) ドキュメントを参照してください。

- **「CI」環境でGemini CLIがインタラクティブモードで実行されません。**
  - **問題:** `CI_` で始まる環境変数（例: `CI_TOKEN`）が設定されている場合、Gemini CLIはインタラクティブモード（プロンプトが表示されない）に入りません。これは、基盤となるUIフレームワークが使用する `is-in-ci` パッケージがこれらの変数を検出し、非対話型CI環境であると想定するためです。
  - **原因:** `is-in-ci` パッケージは、`CI`、`CONTINUOUS_INTEGRATION`、または `CI_` プレフィックスを持つ任意の環境変数の存在をチェックします。これらのいずれかが見つかると、環境が非対話型であると判断され、Gemini CLIがインタラクティブモードで起動するのを防ぎます。
  - **解決策:** CLIの機能に `CI_` プレフィックスの変数が不要な場合は、コマンドに対して一時的に設定解除できます。例: `env -u CI_TOKEN gemini`

- **プロジェクトの `.env` ファイルからDEBUGモードが機能しません。**
  - **問題:** プロジェクトの `.env` ファイルで `DEBUG=true` を設定しても、gemini-cliのデバッグモードが有効になりません。
  - **原因:** `DEBUG` および `DEBUG_MODE` 変数は、gemini-cliの動作との干渉を防ぐために、プロジェクトの `.env` ファイルから自動的に除外されます。
  - **解決策:** 代わりに `.gemini/.env` ファイルを使用するか、`settings.json` の `advanced.excludedEnvVars` 設定を構成して、除外される変数を減らしてください。

## 終了コード

Gemini CLIは、終了理由を示すために特定の終了コードを使用します。これは、スクリプト作成や自動化に特に役立ちます。

| 終了コード | エラータイプ                 | 説明                                                                                         |
| ---------- | -------------------------- | --------------------------------------------------------------------------------------------------- |
| 41         | `FatalAuthenticationError` | 認証プロセス中にエラーが発生しました。                                                |
| 42         | `FatalInputError`          | CLIに無効な入力または不足している入力が提供されました。（非対話モードのみ）                       |
| 44         | `FatalSandboxError`        | サンドボックス環境（例: Docker、Podman、またはSeatbelt）でエラーが発生しました。              |
| 52         | `FatalConfigError`         | 設定ファイル（`settings.json`）が無効であるか、エラーが含まれています。                               |
| 53         | `FatalTurnLimitedError`    | セッションの会話の最大ターン数に達しました。（非対話モードのみ） |

## デバッグのヒント

- **CLIのデバッグ:**
  - `--debug` フラグを使用して、より詳細な出力を取得します。
  - ユーザー固有の構成ディレクトリまたはキャッシュディレクトリにあるCLIログを確認します。

- **コアのデバッグ:**
  - サーバーコンソール出力を確認して、エラーメッセージやスタックトレースを探します。
  - 設定可能な場合は、ログの詳細度を上げます。
  - サーバーサイドコードをステップ実行する必要がある場合は、Node.jsデバッグツール（例: `node --inspect`）を使用します。

- **ツールの問題:**
  - 特定のツールが失敗する場合は、ツールが実行するコマンドまたは操作の最もシンプルなバージョンを実行して、問題を切り分けます。
  - `run_shell_command` の場合、まずコマンドがシェルで直接機能するかどうかを確認します。
  - _ファイルシステムツール_ の場合、パスが正しいことを確認し、権限をチェックします。

- **プレフライトチェック:**
  - コードをコミットする前に必ず `npm run preflight` を実行してください。これにより、書式設定、リンティング、タイプエラーに関連する多くの一般的な問題を検出できます。

## 既存の類似するGitHub Issue、または新しいIssueの作成

この_トラブルシューティングガイド_でカバーされていない問題に遭遇した場合は、Gemini CLIの[GitHubのIssueトラッカー](https://github.com/google-gemini/gemini-cli/issues)を検索することを検討してください。類似するIssueが見つからない場合は、詳細な説明とともに新しいGitHub Issueを作成することを検討してください。プルリクエストも歓迎します！