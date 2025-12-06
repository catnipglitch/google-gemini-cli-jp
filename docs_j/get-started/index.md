# Gemini CLI を始めよう

Gemini CLI へようこそ！このガイドではターミナルから Gemini CLI をインストール、
認証、設定、利用する手順を紹介し、ワークフローを強化する方法を説明します。

## クイックスタート: インストール・認証・設定・利用

Gemini CLI は高度な言語モデルの力をコマンドラインに直結させる AI アシスタントです。
コードの理解や生成、ドキュメントのレビューや編集など、さまざまな作業を支援します。

## インストール

標準的なインストール方法は `npm` です。

```bash
npm install -g @google/gemini-cli
```

インストール後は通常通り以下で起動します。

```bash
gemini
```

他のインストール手段は [Gemini CLI インストール](./installation.md) を参照してください。

## 認証

Gemini CLI を使い始めるには Google のサービスで認証が必要です。ほとんどの場合、既存の Google アカウントでログインできます。

1. インストール済みの Gemini CLI を起動します。

   ```bash
   gemini
   ```

2. 「このプロジェクトの認証方法は？」で **1. Login with Google** を選択。
3. Google アカウントを選択。
4. **Sign in** をクリックして認証を完了。

一部のアカウントでは Google Cloud プロジェクトの設定が必要です。
その他の認証方法を含め、詳しくは [Gemini CLI 認証セットアップ](./authentication.md) をご覧ください。

## 設定

Gemini CLI は環境変数、コマンドライン引数、設定ファイルなど複数の方法で挙動を調整できます。

設定オプションの詳細は [Gemini CLI 設定](./configuration.md) を参照してください。

## 利用

インストールと認証が済んだら、ターミナルでコマンドやプロンプトを入力して Gemini CLI を活用できます。コード生成やファイルの説明など用途は多彩です。

Gemini CLI の活用例は [Gemini CLI 例](./examples.md) をご確認ください。

## 次にやること

- [Gemini CLI のツール](../tools/index.md) を詳しく確認する。
- [Gemini CLI のコマンド](../cli/commands.md) をレビューする。
- [Gemini 3 の始め方](./gemini-3.md) を学ぶ。
