# ローカル開発ガイド

このガイドでは、開発トレースなどのローカル開発機能をセットアップして使用するための手順を説明します。

## 開発トレース

開発トレース（dev traces）は、モデル呼び出し、ツールスケジューラ、ツール呼び出しなどの興味深いイベントをインストルメントすることで、コードのデバッグに役立つ OpenTelemetry（OTel）トレースです。

開発トレースは詳細であり、エージェントの動作を理解し、問題をデバッグするために特別に意図されています。デフォルトでは無効になっています。

開発トレースを有効にするには、Gemini CLI を実行するときに `GEMINI_DEV_TRACING=true` 環境変数を設定します。

### 開発トレースの表示

開発トレースは、Jaeger または Genkit Developer UI を使用して表示できます。

#### Genkit の使用

Genkit は、トレースやその他のテレメトリデータを表示するためのウェブベースの UI を提供します。

1.  **Genkit テレメトリサーバーを起動します。**

    次のコマンドを実行して、Genkit サーバーを起動します。

    ```bash
    npm run telemetry -- --target=genkit
    ```

    スクリプトは、Genkit Developer UI の URL を出力します。たとえば、次のようになります。

    ```
    Genkit Developer UI: http://localhost:4000
    ```

2.  **開発トレースで Gemini CLI を実行します。**

    別のターミナルで、`GEMINI_DEV_TRACING` 環境変数を使用して Gemini CLI コマンドを実行します。

    ```bash
    GEMINI_DEV_TRACING=true gemini
    ```

3.  **トレースを表示します。**

    コマンドを実行した後、ブラウザで Genkit Developer UI の URL を開き、**Traces** タブに移動してトレースを表示します。

#### Jaeger の使用

Jaeger UI で開発トレースを表示できます。開始するには、次の手順に従います。

1.  **テレメトリコレクタを起動します。**

    次のコマンドをターミナルで実行して、Jaeger と OTEL コレクタをダウンロードして起動します。

    ```bash
    npm run telemetry -- --target=local
    ```

    このコマンドは、ローカルテレメトリ用にワークスペースを構成し、Jaeger UI へのリンク（通常は `http://localhost:16686`）も提供します。

2.  **開発トレースで Gemini CLI を実行します。**

    別のターミナルで、`GEMINI_DEV_TRACING` 環境変数を使用して Gemini CLI コマンドを実行します。

    ```bash
    GEMINI_DEV_TRACING=true gemini
    ```

3.  **トレースを表示します。**

    コマンドを実行した後、ブラウザで Jaeger UI のリンクを開いてトレースを表示します。

テレメトリに関する詳細情報については、[テレメトリドキュメント](./cli/telemetry.md)を参照してください。

### 開発トレースでコードをインストルメントする

より詳細なインストルメンテーションのために、独自のコードに開発トレースを追加できます。これは、実行フローのデバッグと理解に役立ちます。

`runInDevTraceSpan` 関数を使用して、コードの任意のセクションをトレーススパンでラップします。

以下に基本的な例を示します。

```typescript
import { runInDevTraceSpan } from '@google/gemini-cli-core';

await runInDevTraceSpan({ name: 'my-custom-span' }, async ({ metadata }) => {
  // `metadata` オブジェクトを使用すると、操作の入力と出力、およびその他の属性を記録できます。
  metadata.input = { key: 'value' };
  // カスタム属性を設定します。
  metadata.attributes['gen_ai.request.model'] = 'gemini-4.0-mega';

  // トレースされるコードがここに入ります
  try {
    const output = await somethingRisky();
    metadata.output = output;
    return output;
  } catch (e) {
    metadata.error = e;
    throw e;
  }
});
```

この例では:

- `name`: トレースに表示されるスパンの名前。
- `metadata.input`: (オプション) トレースされた操作の入力データを含むオブジェクト。
- `metadata.output`: (オプション) トレースされた操作からの出力データを含むオブジェクト。
- `metadata.attributes`: (オプション) スパンに追加するカスタム属性のレコード。
- `metadata.error`: (オプション) 操作が失敗した場合に記録するエラーオブジェクト。