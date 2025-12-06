# Review Frontend PR

**説明**  
特定の GitHub PR をレビューするための手順とチェックリストを示します（`{{args}}` は PR 番号）。

## 手順
1. `gh pr view` で PR 情報を取得し、`gh pr diff` で差分を確認。PR の意図を説明文から読み取る。説明が不十分ならレビューで指摘する。
2. タイトルが Conventional Commits に従っているか確認。直近 5 件のコミット（`git log -n 5 --pretty=format:"%s"`）を参考にする。
3. 必要に応じてコードベース全体を検索し、変更内容の文脈を把握。
4. 提案は Gemini CLI のコード品質とベストプラクティスを念頭に置き、簡潔かつ前向きに行う。
5. 特に `Gemini.md` などの説明ファイル、React コードの一貫性（既存パターンを踏襲）に注意。

## テストと品質のチェックリスト
- `waitFor` は `packages/cli/src/test-utils/async.ts` を使い、`vi.waitFor` や固定待機は避ける。  
- 状態変更ブロックは `act` で包む。  
- レンダリング検証は `toMatchSnapshot`。  
- テストは `render`/`renderWithProviders` を `packages/cli/src/test-utils/render.tsx` から利用。  
- パラメータ化テストや型付けを重視。  
- `any` を避け、`fs`/`os`/`child_process` は原則モックしない。必要な場合はファイルの先頭でのみ。  
- `vi.restoreAllMocks()` を `afterEach` で呼び出し、タイマーは `vi.useFakeTimers()`。  
- スナップショットの変更は意図を確認。不審なものはコメント。  
- 固定待機 (`await delay(100)`) は禁止。`waitFor` に述語を与えるなどで安定化。  

## React コードの注意点
- `setState` のコールバックで副作用を出さない（reducers や `useRef` で代替）。  
- 無限レンダリングループや複数の連続イベントへの対応（`useKeyPress.ts` を利用し、Reducer で処理）。  
- `console.log/warn/error` を残さず、同期的なファイル I/O を避ける。  
- State 初期値は明示的に（未確定は `undefined`）。  
- `useEffect` の依存関係は正しく管理し、`react-hooks/exhaustive-deps` を無効化しない。  
- 型は不要な nullable を避け、Provider などで prop drilling を抑制。

## 全体指針
- 設定はユーザーが変更する可能性があるものだけを対象とし、引数ではなく `settingsSchema.ts` へ。  
- `showInDialog: true` の場合は `docs/get-started/configuration.md` へ記録し、`requiresRestart` を正しくセット。  
- 新しいキーボードショートカットは `keyBindings.ts` / `docs/cli/keyboard-shortcuts.md` に反映し、複数プラットフォームでテスト。当該ショートカットが VSCode の既存ショートカットや `Meta` キーと衝突しないか注意。  
- エラー再送は `debugLogger` を使い、重複ログを避ける。  
- TypeScript では `switch` の `default` に `checkExhaustive` を使い、`!` は最小限に。  
- Gemini CLI へのプロンプト影響がある変更は `anj-s` へのタグ付けを検討。  
- コメントを投稿する場合は `gh pr comment {{args}} --body {{review}}` を使う。
