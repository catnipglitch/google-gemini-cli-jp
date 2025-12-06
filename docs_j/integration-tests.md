# 統合テスト

このドキュメントでは、このプロジェクトで使用されている統合テストフレームワークに関する情報を提供します。

## 概要

統合テストは、Gemini CLI のエンドツーエンド機能を検証するように設計されています。これらは、制御された環境でビルドされたバイナリを実行し、ファイルシステムと対話する際に期待どおりに動作することを確認します。

これらのテストは `integration-tests` ディレクトリに配置され、カスタムテストランナーを使用して実行されます。

## テストのビルド

統合テストを実行する前に、実際にテストしたいリリースバンドルを作成する必要があります。

```bash
npm run bundle
```

CLI ソースコードに変更を加えた後（テストに変更を加えた後ではない）、このコマンドを再実行する必要があります。

## テストの実行

統合テストは、デフォルトの `npm run test` コマンドの一部としては実行されません。`npm run test:integration:all` スクリプトを使用して明示的に実行する必要があります。

統合テストは、次のショートカットを使用して実行することもできます。

```bash
npm run test:e2e
```

## 特定のテストセットの実行

テストファイルのサブセットを実行するには、`npm run <integration test command> <file_name1> ....` を使用します。ここで、<integration test command> は `test:e2e` または `test:integration*` のいずれかで、`<file_name>` は `integration-tests/` ディレクトリ内の `.test.js` ファイルのいずれかです。たとえば、次のコマンドは `list_directory.test.js` と `write_file.test.js` を実行します。

```bash
npm run test:e2e list_directory write_file
```

### 名前で単一のテストを実行する

名前で単一のテストを実行するには、`--test-name-pattern` フラグを使用します。

```bash
npm run test:e2e -- --test-name-pattern "reads a file"
```

### モデル応答の再生成

一部の統合テストでは、偽のモデル応答を使用しており、実装が変更されると時々再生成する必要がある場合があります。

これらのゴールデンファイルを再生成するには、テストを実行するときに `REGENERATE_MODEL_GOLDENS` 環境変数を「true」に設定します。例:

**警告**: ローカルで実行する場合は、Gemini がこれらの応答に含めた可能性のある自分自身やシステムに関する情報がないか、これらの更新された応答を確認する必要があります。

```bash
REGENERATE_MODEL_GOLDENS="true" npm run test:e2e
```

**警告**: テストの最後に **await rig.cleanup()** を実行していることを確認してください。そうしないと、ゴールデンファイルは更新されません。

### テストのデフレイク

**新しい**統合テストを追加する前に、デフレイクスクリプトまたはワークフローで少なくとも5回テストして、不安定でないことを確認する必要があります。

### デフレイクスクリプト

```bash
npm run deflake -- --runs=5 --command="npm run test:e2e -- --test-name-pattern '<your-new-test-name>'"
```

#### デフレイクワークフロー

```bash
gh workflow run deflake.yml --ref <your-branch> -f test_name_pattern="<your-test-name-pattern>"
```

### すべてのテストを実行する

統合テストスイート全体を実行するには、次のコマンドを使用します。

```bash
npm run test:integration:all
```

### サンドボックスマトリックス

`all` コマンドは、`no sandboxing`、`docker`、`podman` のテストを実行します。各個別のタイプは、次のコマンドを使用して実行できます。

```bash
npm run test:integration:sandbox:none
```

```bash
npm run test:integration:sandbox:docker
```

```bash
npm run test:integration:sandbox:podman
```

## 診断

統合テストランナーは、テストの失敗を追跡するのに役立ついくつかの診断オプションを提供します。

### テスト出力の保持

テスト実行中に作成された一時ファイルを検査のために保持できます。これは、ファイルシステム操作の問題をデバッグするのに役立ちます。

テスト出力を保持するには、`KEEP_OUTPUT` 環境変数を `true` に設定します。

```bash
KEEP_OUTPUT=true npm run test:integration:sandbox:none
```

出力が保持されると、テストランナーはテスト実行のための一意のディレクトリへのパスを出力します。

### 詳細出力

より詳細なデバッグのために、`VERBOSE` 環境変数を `true` に設定します。

```bash
VERBOSE=true npm run test:integration:sandbox:none
```

同じコマンドで `VERBOSE=true` と `KEEP_OUTPUT=true` を使用する場合、出力はコンソールにストリーミングされ、テストの一時ディレクトリ内のログファイルにも保存されます。

詳細出力は、ログのソースを明確に識別するようにフォーマットされています。

```
--- TEST: <log dir>:<test-name> ---
... gemini コマンドからの出力 ...
--- END TEST: <log dir>:<test-name> ---
```

## リンティングとフォーマット

コードの品質と一貫性を確保するために、統合テストファイルはメインビルドプロセスの一部としてリンティングされます。リンターを手動で実行し、自動修正することもできます。

### リンターの実行

リンティングエラーをチェックするには、次のコマンドを実行します。

```bash
npm run lint
```

コマンドに `:fix` フラグを含めると、修正可能なリンティングエラーを自動的に修正できます。

```bash
npm run lint:fix
```

## ディレクトリ構造

統合テストは、`.integration-tests` ディレクトリ内にテスト実行ごとに一意のディレクトリを作成します。このディレクトリ内には、テストファイルごとにサブディレクトリが作成され、その中には個々のテストケースごとにサブディレクトリが作成されます。

この構造により、特定のテスト実行、ファイル、またはケースのアーティファクトを簡単に見つけることができます。

```
.integration-tests/
└── <run-id>/
    └── <test-file-name>.test.js/
        └── <test-case-name>/
            ├── output.log
            └── ...その他のテストアーティファクト...
```

## 継続的インテグレーション

統合テストが常に実行されることを保証するために、`.github/workflows/e2e.yml` に GitHub Actions ワークフローが定義されています。このワークフローは、`main` ブランチに対するプルリクエスト、またはプルリクエストがマージキューに追加されたときに、統合テストを自動的に実行します。

ワークフローは、異なるサンドボックス環境でテストを実行し、Gemini CLI がそれぞれでテストされることを保証します。

- `sandbox:none`: サンドボックスなしでテストを実行します。
- `sandbox:docker`: Docker コンテナでテストを実行します。
- `sandbox:podman`: Podman コンテナでテストを実行します。