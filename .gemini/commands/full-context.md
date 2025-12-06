# Full Context

**説明**  
`packages/cli` と `packages/core` の全ソース、代表的なテストファイルを含む包括的なコードコンテキストを提供し、フロントエンドにおける開発ガイドラインを順守するためのコマンドです。

## コード対象
- `packages/cli` と `packages/core` の `.ts`/`.tsx`（`.test.` および `.d.ts` を除く）をすべて読み込み。
- 代表的テストとして `packages/cli/src/ui/components/InputPrompt.test.tsx` と `packages/cli/src/ui/App.test.tsx` を一緒に確認。

## フロントエンド開発の必須ルール
1. **テスト基準**
   - `waitFor` は必ず `packages/cli/src/test-utils/async.ts` から。
   - 状態変更の部分は `act` で囲む。
   - スナップショットは `toMatchSnapshot`。
   - `render`/`renderWithProviders` を `packages/cli/src/test-utils/render.tsx` から使い、`ink-testing-library` を直接使わない。
   - モック依存は既存のものを共有し、`fs`/`os`/`child_process` のモックはファイル冒頭のみで行う。
2. **React & Ink**
   - キーボード入力は `useKeyPress.ts` 経由で処理し、複数イベントを reducer で対応。
   - `setState` の中で副作用を起こさず、状態は明示的に初期化。同期 I/O や过度な prop drilling は禁止。
3. **設定・ドキュメント**
   - ユーザーが変更する可能性のあるオプションは `settings` にまとめ、CLI 引数を追加しない。
   - 新設定登録は `packages/cli/src/config/settingsSchema.ts` へ。
   - `showInDialog: true` なら `docs/get-started/configuration.md` へ記載、`requiresRestart` の設定も検証。
4. **キーボードショートカット & ロギング**
   - 新ショートカットは `packages/cli/src/config/keyBindings.ts` に登録し、`docs/cli/keyboard-shortcuts.md` に説明。VSCode/iTerm2/Ghostty/Windows でも動作確認。
   - `debugLogger` を使ってエラーを記録し、`console.log/warn/error` をコードに残さない。
5. **TypeScript + その他**
   - 非 null アサーション `!` は極力使わない。

## プロンプト利用
引数 `{{args}}` を参考に、上記の文脈とルールを守った回答を生成してください。
