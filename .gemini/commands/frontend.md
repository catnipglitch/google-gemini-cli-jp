# Frontend Context

**説明**  
`packages/cli` の全ソースと代表的テスト（`InputPrompt.test.tsx`, `App.test.tsx`）を読み込み、フロントエンドに関する設計・テスト・設定のガイドラインを守るための文脈を提供するコマンドです。

## 何を注視すべきか
* `packages/cli` 以下の `.ts`/`.tsx`（`.test.` や `.d.ts` を除く）を丸ごと対象とする。
* テストファイルは `packages/cli/src/ui/components/InputPrompt.test.tsx` と `App.test.tsx` を特に重要視する。
* フロントエンドに新規コードを加える際は、以下のスタイルとアーキテクチャ要件を厳守する。

## 主なルール
1. **テスト基準**
   - `waitFor` は `packages/cli/src/test-utils/async.ts` から必ず利用し、`vi.waitFor` や固定待機 (`delay(100)`) は使わない。
   - 状態変更を伴うテストブロックは `act` でラップ。
   - スナップショット検証は `toMatchSnapshot`。
   - `render` は `render`/`renderWithProviders` を `packages/cli/src/test-utils/render.tsx` から使用。`ink-testing-library` へ直接依存しない。
   - モックは既存のものを使い、`fs`/`os`/`child_process` は可能な限りファイル上部でのみモック。
2. **React & Ink アーキテクチャ**
   - キーボード操作は必ず `useKeyPress.ts` を使い、複数イベントの扱いには `text-buffer.ts` 風の reducer を採用。
   - `setState` のコールバック内では副作用を発生させず、必要なら reducer や `useRef` を用いる。
   - 状態は明示的に初期化し、同期的 I/O や過剰な prop drilling を避ける。
3. **設定・ドキュメント**
   - ユーザー設定は settings を使い、CLI 引数を増やさない。
   - 新設定は `packages/cli/src/config/settingsSchema.ts` に追加。
   - `showInDialog: true` のときは `docs/get-started/configuration.md` に記載し、`requiresRestart` を設定。
4. **キーボードショートカット**
   - `packages/cli/src/config/keyBindings.ts` に登録し、`docs/cli/keyboard-shortcuts.md` にドキュメント化。
   - Meta キー、関数キー、VS Code 既存ショートカットとの競合に注意し、VSCode/iTerm2/Ghostty/Windows で動作確認を指示。
5. **その他**
   - エラーは `debugLogger` で記録し、`console.log/warn/error` を残さない。
   - TypeScript では非 null アサーション (`!`) を極力避ける。

## プロンプト
`{{args}}` を挿入しつつ、上記の文脈と厳格なガイドラインのもとで応答してください。
