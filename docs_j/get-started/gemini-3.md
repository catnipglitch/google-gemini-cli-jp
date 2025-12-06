# Gemini CLI で Gemini 3 Pro を使う

Gemini 3 Pro は以下のユーザーが Gemini CLI で利用できます。

- Google AI Ultra (Business を除く)
- Google AI Pro
- Gemini Code Assist Standard / Enterprise（管理者がプレビューを有効化）
- 有料の Gemini API キー所有者
- 有料の Vertex API キー所有者

それ以外の方は [ウェイトリスト](https://goo.gle/geminicli-waitlist-signup) に申し込み、承認後にアクセスできます。

> **注意:** 自動付与/ウェイトリスト承認後も `/settings` で **Preview Features** を `true` にする必要があります。

## ウェイトリスト参加手順

1. Gemini CLI をインストール。
2. **Login with Google** で認証し、「Gemini 3 is now available」のバナーを確認（表示されない場合は CLI を最新に更新）。
3. [Access Gemini 3 in Gemini CLI](https://goo.gle/geminicli-waitlist-signup) で使用アカウントを入力。

承認はバッチで行われ、メールで通知されます。

> **注意:** 承認されるまで Preview Features を有効化しないでください。先に有効化すると Gemini 2.5 Pro へフォールバックします。

## 有効化手順

受諾メールを受け取ったらまたは自動付与されたら、CLI 内で `/settings` を実行し **Preview Features** を `true` にして Gemini 3 Pro を有効化します。
詳しくは [Gemini CLI 設定](../cli/settings.md) を参照してください。

### 利用制限とフォールバック

- Gemini 3 Pro の日次リミットを超えると通知が表示され、Gemini 2.5 Pro への切り替えやアップグレード案内が表示されます。リセット時間も通知されます。
- Gemini 2.5 Pro も同様に通過すると Gemini 2.5 Flash へのフォールバック案内が出ます。

### キャパシティエラー

Gemini 3 Pro が混雑中の場合、再試行（指数バックオフ）か Gemini 2.5 Pro へ切り替えるか選択できます。

### モデル選択 / ルーティング

デフォルトは **Auto** ルーティング。
- **Auto:** 簡単なプロンプトは Gemini 2.5 Flash、複雑なプロンプトでは Gemini 3 Pro を優先（有効なら）。
- **Pro:** `/model` で **Pro** を選ぶと最も能力の高いモデル（Gemini 3 Pro も含む）を優先します。

詳細は [Gemini CLI モデル選択](../cli/model.md) を参照。

## Gemini Code Assist での有効化

Standard/Enterprise 利用時はリリースチャネルの設定が必要。
詳細は [Configure release channels](https://developers.google.com/gemini-code-assist/docs/configure-release-channels) を参照。

### 管理者操作

1. 設定対象の Google Cloud プロジェクトにアクセス。
2. **Admin for Gemini > Settings** へ移動。
3. **Release channels for Gemini Code Assist in local IDEs** で **Preview** を選択し保存。

### ユーザー操作

管理者が Preview を有効化後 2〜3 分待ち、
1. Gemini CLI で `/settings` を実行。
2. **Preview Features** を `true` に設定。
3. 再起動すると Gemini 3 Pro が使えるようになります。

## 問題が起きたら

- 既存の [GitHub issue](https://github.com/google-gemini/gemini-cli/issues) を検索。
- 合致する issue なければ [新規作成](https://github.com/google-gemini/gemini-cli/issues/new/choose)。
- コメントやフィードバックは [GitHub Discussion](https://github.com/google-gemini/gemini-cli/discussions) もご利用ください。
