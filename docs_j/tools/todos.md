# Todo ツール (`write_todos`)

`write_todos` は複雑なリクエストを小タスクへ分解して進捗を可視化するためのツールです。

## 引数

- `todos`（オブジェクト配列, 必須）: 完全な todo リスト。各項目は `description`（説明）と `status`（`pending`, `in_progress`, `completed`, `cancelled`）を持ち、既存リストは置き換えられます。

## 動作

- エージェントは現在進行中のタスクを `in_progress` とし、完了時に `completed` へ更新。
- 同時に `in_progress` は 1 件のみで、ユーザーに現在の集中対象を提示。
- 計画は探索中に変化し、新規タスク追加や不要なキャンセルが発生。
- `Ctrl+T` で todo リストを表示/非表示でき、現在のタスクは入力欄上部に表示される。

## 注意点

- デフォルトで有効。無効化するには `settings.json` で `"useWriteTodos": false` と設定。
- 単発の質問ではなく、複数ターンのタスクで使うよう設計。
