# README 更新用テンプレート

## このドキュメントの目的

リポジトリのフォーク元と同期を行うと、`README.md` がオリジナルの内容で上書きされます。
このファイルは、同期や追加作業の完了後に、`README.md` を本リポジトリ独自の内容（日本語説明など）に戻すためのテンプレートです。

---

### 日本語説明

このリポジトリは、Google Gemini CLI ( [google-gemini/gemini-cli](https://github.com/google-gemini/gemini-cli) ) のフォークです。

**日本語翻訳された README は [README_J.md](README_J.md) をご覧ください。**

オリジナルの最新情報や詳細については、以下をご確認ください。
[Original README](https://github.com/google-gemini/gemini-cli/blob/main/README.md)

#### 独自拡張・変更点

- **ドキュメントの日本語化**:
  `docs` フォルダの内容を日本語に翻訳し、`docs_j` フォルダへ格納しています。
- **AGENT ルールと翻訳ガイド**:
  `AGENTS.md` に Codex 向けルール、AGENT_TASK_LIST への進捗管理ルール、日本語応答ポリシーを明記し、`.gemini/commands` 配下の `.toml` を参照しやすい `.md` 解説として追加しました。
- **追加ドキュメントの整備**:
  `extensions`, `get-started`, `hooks`, `ide-integration`, `tools`, `changelogs` などの `docs/` 内リソースを日本語化し、`docs_j` にアクセス用ファイルを用意しています。

### English Description

This repository is a fork of the Google Gemini CLI ( [google-gemini/gemini-cli](https://github.com/google-gemini/gemini-cli) ).

**For the Japanese translation of the README, please refer to [README_J.md](README_J.md).**

For the latest information and details about the original project, please refer to:
[Original README](https://github.com/google-gemini/gemini-cli/blob/main/README.md)

- #### Customizations & Changes

- **Japanese Documentation**:
  The contents of the `docs` folder have been translated into Japanese and stored in the `docs_j` folder, including the `extensions`, `get-started`, `hooks`, `ide-integration`, `tools`, and `changelogs` sections.
- **Agent Instructions & References**:
  Added `AGENTS.md` with Codex-specific operating rules, task-tracking guidelines, and a Japanese-response policy. `.gemini/commands` entries now also have matching Markdown summaries (e.g., `code-guide.md`, `find-docs.md`, `frontend.md`, `full-context.md`, `github/cleanup-back-to-main.md`, `oncall/pr-review.md`, `review-frontend.md`) describing their intent and key constraints.
