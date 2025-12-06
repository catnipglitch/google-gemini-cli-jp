# Gemini CLI 設定 (`/settings` コマンド)

Control your Gemini CLI experience with the `/settings` command. The `/settings`
command opens a dialog to view and edit all your Gemini CLI settings, including
your UI experience, keybindings, and accessibility features.

Your Gemini CLI settings are stored in a `settings.json` file. In addition to
using the `/settings` command, you can also edit them in one of the following
locations:

- **User settings**: `~/.gemini/settings.json`
- **Workspace settings**: `your-project/.gemini/settings.json`

Note: Workspace settings override user settings.

## Settings reference

Here is a list of all the available settings, grouped by category and ordered as
they appear in the UI.

### General

| UI ラベル                       | 設定                            | 説明                                                                  | デフォルト     |
| ------------------------------- | ---------------------------------- | ---------------------------------------------------------------------------- | ----------- |
| プレビュー機能（例: モデル）       | `general.previewFeatures`          | プレビュー機能（例: プレビューモデル）を有効にします。                      | `false`     |
| Vim モード                      | `general.vimMode`                  | Vim キーバインディングを有効にします。                                                      | `false`     |
| 自動更新を無効にする               | `general.disableAutoUpdate`        | 自動更新を無効にします。                                                   | `false`     |
| プロンプト補完を有効にする       | `general.enablePromptCompletion`   | 入力中に AI を利用したプロンプト補完の提案を有効にします。                | `false`     |
| キーストロークロギングをデバッグする | `general.debugKeystrokeLogging`    | キーストロークのデバッグログをコンソールに有効にします。                           | `false`     |
| セッション保持                    | `general.sessionRetention`         | 自動セッションクリーンアップの設定。この機能はデフォルトで無効になっています。 | `undefined` |
| セッションクリーンアップを有効にする | `general.sessionRetention.enabled` | 自動セッションクリーンアップを有効にします。                                            | `false`     |

### Output

| UI ラベル      | 設定         | 説明                                            | デフォルト |
| ------------- | --------------- | ------------------------------------------------------ | ------- |
| 出力形式        | `output.format` | CLI 出力形式。`text` または `json` にすることができます。 | `text`  |

### UI

| UI ラベル                          | 設定                                  | 説明                                                          | デフォルト |
| ---------------------------------- | ---------------------------------------- | -------------------------------------------------------------------- | ------- |
| ウィンドウタイトルを非表示にする      | `ui.hideWindowTitle`                     | ウィンドウのタイトルバーを非表示にします。                                           | `false` |
| タイトルにステータスを表示する      | `ui.showStatusInTitle`                   | ターミナルウィンドウのタイトルに Gemini CLI のステータスと思考を表示します。    | `false` |
| ヒントを非表示にする                 | `ui.hideTips`                            | UI の役立つヒントを非表示にします。                                         | `false` |
| バナーを非表示にする                 | `ui.hideBanner`                          | アプリケーションバナーを非表示にします。                                         | `false` |
| コンテキストサマリーを非表示にする   | `ui.hideContextSummary`                  | 入力の上にあるコンテキストサマリー（GEMINI.md、MCP サーバー）を非表示にします。   | `false` |
| CWD を非表示にする                   | `ui.footer.hideCWD`                      | フッターの現在の作業ディレクトリパスを非表示にします。               | `false` |
| サンドボックスステータスを非表示にする | `ui.footer.hideSandboxStatus`            | フッターのサンドボックスステータスインジケーターを非表示にします。                     | `false` |
| モデル情報を非表示にする             | `ui.footer.hideModelInfo`                | フッターのモデル名とコンテキスト使用量を非表示にします。                 | `false` |
| コンテキストウィンドウのパーセンテージを非表示にする | `ui.footer.hideContextPercentage`        | コンテキストウィンドウの残りパーセンテージを非表示にします。                       | `true`  |
| フッターを非表示にする               | `ui.hideFooter`                          | UI からフッターを非表示にします。                                         | `false` |
| メモリ使用量を表示する             | `ui.showMemoryUsage`                     | UI にメモリ使用量情報を表示します。                          | `false` |
| 行番号を表示する                   | `ui.showLineNumbers`                     | チャットに行番号を表示します。                                       | `false` |
| 引用を表示する                     | `ui.showCitations`                       | チャットで生成されたテキストの引用を表示します。                       | `false` |
| 全幅を使用する                     | `ui.useFullWidth`                        | 出力にターミナルの全幅を使用します。                     | `true`  |
| 代替スクリーンバッファを使用する     | `ui.useAlternateBuffer`                  | シェル履歴を保持するために、UI に代替スクリーンバッファを使用します。 | `true`  |
| ローディングフレーズを無効にする     | `ui.accessibility.disableLoadingPhrases` | アクセシビリティのためにローディングフレーズを無効にします。                           | `false` |
| スクリーンリーダーモード             | `ui.accessibility.screenReader`          | スクリーンリーダーでアクセスしやすくするために、出力をプレーンテキストでレンダリングします。     | `false` |

### IDE

| UI ラベル | 設定       | 説明                  | デフォルト |
| -------- | ------------- | ---------------------------- | ------- |
| IDE モード | `ide.enabled` | IDE 統合モードを有効にします。 | `false` |

### モデル

| UI ラベル                | 設定                      | 説明                                                                            | デフォルト |
| ----------------------- | ---------------------------- | -------------------------------------------------------------------------------------- | ------- |
| 最大セッションターン数    | `model.maxSessionTurns`      | セッションで保持するユーザー/モデル/ツールの最大ターン数。-1 は無制限を意味します。      | `-1`    |
| 圧縮閾値                  | `model.compressionThreshold` | コンテキスト圧縮をトリガーするコンテキスト使用量の割合（例: 0.2、0.3）。 | `0.2`   |
| 次のスピーカーチェックをスキップする | `model.skipNextSpeakerCheck` | 次のスピーカーチェックをスキップします。                                                           | `true`  |

### コンテキスト

| UI ラベル                             | 設定                                           | 説明                                                                                                                                     | デフォルト |
| ------------------------------------ | ------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | ------- |
| メモリ検出最大ディレクトリ数        | `context.discoveryMaxDirs`                        | 検索するメモリの最大ディレクトリ数。                                                                                             | `200`   |
| インクルードディレクトリからメモリをロードする | `context.loadMemoryFromIncludeDirectories`        | `/memory refresh` が GEMINI.md ファイルをロードする方法を制御します。true の場合、インクルードディレクトリがスキャンされます。false の場合、現在のディレクトリのみが使用されます。 | `false` |
| .gitignore を尊重する                | `context.fileFiltering.respectGitIgnore`          | 検索時に .gitignore ファイルを尊重します。                                                                                                        | `true`  |
| .geminiignore を尊重する             | `context.fileFiltering.respectGeminiIgnore`       | 検索時に .geminiignore ファイルを尊重します。                                                                                                     | `true`  |
| 再帰的なファイル検索を有効にする     | `context.fileFiltering.enableRecursiveFileSearch` | プロンプト内の @ 参照を補完するときに、再帰的なファイル検索機能を有効にします。                                                          | `true`  |
| ファジー検索を無効にする             | `context.fileFiltering.disableFuzzySearch`        | ファイル検索時にファジー検索を無効にします。                                                                                                  | `false` |

### ツール

| UI ラベル                         | 設定                              | 説明                                                                                                     | デフォルト |
| -------------------------------- | ------------------------------------ | --------------------------------------------------------------------------------------------------------------- | ------- |
| インタラクティブシェルを有効にする | `tools.shell.enableInteractiveShell` | 対話型シェルエクスペリエンスに `node-pty` を使用します。`child_process` へのフォールバックは引き続き適用されます。                      | `true`  |
| 色を表示する                     | `tools.shell.showColor`              | シェル出力に色を表示します。                                                                                     | `false` |
| 自動承認                         | `tools.autoAccept`                   | 安全と見なされるツール呼び出し（例: 読み取り専用操作）を自動的に承認して実行します。              | `false` |
| ripgrep を使用する                 | `tools.useRipgrep`                   | フォールバック実装の代わりに `ripgrep` をファイルコンテンツ検索に使用します。検索パフォーマンスが向上します。 | `true`  |
| ツール出力の切り詰めを有効にする | `tools.enableToolOutputTruncation`   | 大規模なツール出力の切り詰めを有効にします。                                                                        | `true`  |
| ツール出力切り詰め閾値           | `tools.truncateToolOutputThreshold`  | この文字数より大きい場合、ツール出力を切り詰めます。無効にするには -1 に設定します。                           | `10000` |
| ツール出力切り詰め行数           | `tools.truncateToolOutputLines`      | ツール出力を切り詰める際に保持する行数。                                                        | `100`   |
| メッセージバス統合を有効にする   | `tools.enableMessageBusIntegration`  | メッセージバス統合によるポリシーベースのツール確認を有効にします。                                              | `false` |

### セキュリティ

| UI ラベル                   | 設定                        | 説明                                        | デフォルト |
| -------------------------- | ------------------------------ | -------------------------------------------------- | ------- |
| YOLO モードを無効にする      | `security.disableYoloMode`     | YOLO モードを無効にします（フラグで有効になっている場合でも）。      | `false` |
| Git からの拡張機能をブロックする | `security.blockGitExtensions`  | Git からの拡張機能のインストールと読み込みをブロックします。 | `false` |
| フォルダ信頼                 | `security.folderTrust.enabled` | フォルダ信頼が有効になっているかどうかを追跡する設定。  | `false` |

### 実験的

| UI ラベル                            | 設定                                                 | 説明                                                  | デフォルト |
| ----------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------ | ------- |
| コードベースインベスティゲーターを有効にする | `experimental.codebaseInvestigatorSettings.enabled`     | Codebase Investigator エージェントを有効にします。                      | `true`  |
| コードベースインベスティゲーターの最大ターン数 | `experimental.codebaseInvestigatorSettings.maxNumTurns` | Codebase Investigator エージェントの最大ターン数。 | `10`    |