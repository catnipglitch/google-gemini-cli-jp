# Repository Guidelines

## Project Structure & Module Organization
- Root scripts in `scripts/` handle build/bundle/release; do not edit generated `bundle/`.
- Workspaces under `packages/`: `cli` (user CLI), `core` (shared logic/settings), `a2a-server` (agent server), `test-utils` (helpers), `vscode-ide-companion` (editor companion).
- Integration tests live in `integration-tests`; docs in `docs` and `docs_j`; schemas in `schemas`; vendored code in `third_party`.

## Build, Test, and Development Commands
- `npm run start` — run the CLI in dev; `npm run start:a2a-server` starts the agent server.
- `npm run build` — build all workspaces; `npm run build:all` also builds sandbox and VS Code companion; `npm run bundle` regenerates the distributable.
- `npm test` — workspace unit tests; `npm run test:ci` adds script-level tests.
- Integration: `npm run test:integration:sandbox:none`, or `:docker`/`:podman`; `npm run test:e2e` = verbose integration smoke.
- `npm run lint` / `npm run lint:fix`, `npm run format`, and `npm run typecheck` keep style/types aligned.

## Coding Style & Naming Conventions
- TypeScript (ESM) with 2-space indent and Prettier defaults; run `npm run format` before commits.
- ESLint (`eslint.config.js`) enforces imports, React/Ink hooks, and license headers; fix with `npm run lint:fix`.
- Use kebab-case filenames (e.g., `prompt-runner.ts`) and lowerCamelCase for variables/functions; keep modules small and reusable via `core`.

## Testing Guidelines
- Vitest for unit/integration; co-locate unit specs near source or in `packages/*/test-utils`.
- Integration specs live in `integration-tests`; pick sandbox via `GEMINI_SANDBOX=false|docker|podman`.
- Cover new commands/flags/failure paths; keep snapshots minimal and stable.

## Commit & Pull Request Guidelines
- Match history: short imperative subject, sentence case, optional period, issue reference in parentheses (e.g., `Add retry backoff (#123)`).
- In PRs, describe scope, testing (`npm test`, specific integration command), and linked issues; attach CLI UX screenshots or clips when useful.
- Keep commits focused and avoid committing `bundle/` or other generated artifacts; prefer repo scripts over ad-hoc tools.

## Security & Configuration Tips
- Node.js ≥20; use `npm ci` for reproducible installs. Never commit secrets; keep env values in your shell or `.env` (ignored).
- Use `npm run auth` only when pulling from private artifact registries; prefer sandboxed integration runs unless Docker/Podman is required.

## オペレーションモード (Codex向け)
- Plan Mode (デフォルト): 計画/要件整理/ドキュメント作成に限定。コード生成・修正は禁止。Markdown/TXTの編集のみ許可。
- Edit Mode: 実装・コーディングを実施。Plan→Editへの切替は「Edit Modeへ切り替えます」の宣言とユーザー同意を経て行う。

## タスク管理ルール
- `AGENT_TASK_LIST.md` にタスクを記録する際は重複しないIDを付与（例: `[T-001]`）。`PRD.md` 更新後、新規作業発生時、完了時に更新。
- ファイルが100行を超える場合は完了タスク削除前にユーザーへ確認。マイクロタスクは記載せず、意味のあるマイルストーン単位で管理。

## 言語ポリシー
- ユーザーへの返答は常に日本語（ユーザーが英語で問いかけても日本語で回答）。ドキュメント内の英語セクションは必要最小限に留める。
