# 高度なモデル構成

このガイドでは、Gemini CLI 内のモデル構成システムについて詳しく説明します。研究者、AI 品質エンジニア、上級ユーザー向けに設計されたこのシステムは、生成モデルのハイパーパラメータと動作を管理するための厳格なフレームワークを提供します。

> **警告**: これはパワーユーザー機能です。構成値は、最小限の検証でモデルプロバイダーに直接渡されます。設定が正しくない場合（例: 互換性のないパラメータの組み合わせ）、API からランタイムエラーが発生する可能性があります。

## 1. システム概要

モデル構成システム (`ModelConfigService`) は、モデル生成を決定論的に制御できるようにします。要求されたモデル識別子（例: CLI フラグまたはエージェント要求）を、基盤となる API 構成から切り離します。これにより、次のことが可能になります。

- **正確なハイパーパラメータチューニング**: `temperature`、`topP`、`thinkingBudget`、およびその他の SDK レベルのパラメータを直接制御します。
- **環境固有の動作**: さまざまな運用コンテキスト（例: テスト対本番）で異なる構成。
- **エージェントスコープのカスタマイズ**: 特定のエージェントがアクティブな場合にのみ特定の適用設定。

このシステムは、**エイリアス**と**オーバーライド**という2つのコアプリミティブで動作します。

## 2. 構成プリミティブ

これらの設定は、構成ファイルの `modelConfigs` キーの下にあります。

### エイリアス (`customAliases`)

エイリアスは、名前付きの再利用可能な構成プリセットです。ユーザーは、`customAliases` マップで独自のエイリアスを定義するか（またはシステムデフォルトをオーバーライドする）、システムデフォルトをオーバーライドする必要があります。

- **継承**: エイリアスは別のエイリアス（`chat-base` などのシステムデフォルトを含む）を `extends` でき、その `modelConfig` を継承します。子エイリアスは、継承された設定を上書きまたは拡張できます。
- **抽象エイリアス**: 他のエイリアスのベースとしてのみ機能する場合、エイリアスは具体的な `model` を指定する必要はありません。

**階層の例**:

```json
"modelConfigs": {
  "customAliases": {
    "base": {
      "modelConfig": {
        "generateContentConfig": { "temperature": 0.0 }
      }
    },
    "chat-base": {
      "extends": "base",
      "modelConfig": {
        "generateContentConfig": { "temperature": 0.7 }
      }
    }
  }
}
```

### オーバーライド (`overrides`)

オーバーライドは、実行時コンテキストに基づいて構成を挿入する条件付きルールです。これらは、各モデル要求に対して動的に評価されます。

- **一致基準**: 要求コンテキストが指定された `match` プロパティと一致する場合にオーバーライドが適用されます。
  - `model`: 要求されたモデル名またはエイリアスと一致します。
  - `overrideScope`: 要求の異なるスコープ（通常はエージェント名、例: `codebaseInvestigator`）と一致します。

**オーバーライドの例**:

```json
"modelConfigs": {
  "overrides": [
    {
      "match": {
        "overrideScope": "codebaseInvestigator"
      },
      "modelConfig": {
        "generateContentConfig": { "temperature": 0.1 }
      }
    }
  ]
}
```

## 3. 解決戦略

`ModelConfigService` は、2段階のプロセスで最終構成を解決します。

### ステップ1: エイリアス解決

要求されたモデル文字列は、システム `aliases` とユーザー `customAliases` のマージされたマップで検索されます。

1.  見つかった場合、システムは `extends` チェーンを再帰的に解決します。
2.  設定は親から子へとマージされます（子が優先されます）。
3.  これにより、ベース `ResolvedModelConfig` が生成されます。
4.  見つからない場合、要求された文字列は生のモデル名として扱われます。

### ステップ2: オーバーライド適用

システムは、要求コンテキスト（`model` と `overrideScope`）に対して `overrides` リストを評価します。

1.  **フィルタリング**: すべての一致するオーバーライドが識別されます。
2.  **ソート**: 一致は**特異性**（`match` オブジェクトで一致したキーの数）によって優先順位が付けられます。
    - 特定の一致（例: `model` + `overrideScope`）は、広範な一致（例: `model` のみ）をオーバーライドします。
    - タイブレーク: 特異性が等しい場合、`overrides` 配列での定義順序が保持されます（最後のものが優先されます）。
3.  **マージ**: ソートされたオーバーライドの構成は、ベース構成に順番にマージされます。

## 4. 構成リファレンス

構成は `ModelConfigServiceConfig` インターフェースに従います。

### `ModelConfig` オブジェクト

モデルの実際のパラメータを定義します。

| プロパティ                | タイプ     | 説明                                                        |
| :---------------------- | :------- | :----------------------------------------------------------------- |
| `model`                 | `string` | 呼び出すモデルの識別子（例: `gemini-2.5-pro`）。 |
| `generateContentConfig` | `object` | `@google/genai` SDK に渡される構成オブジェクト。        |

### `GenerateContentConfig` (共通パラメータ)

SDK の `GenerateContentConfig` に直接マップされます。共通パラメータには以下が含まれます。

- **`temperature`**: (`number`) 出力のランダム性を制御します。値が低いほど（0.0）決定論的であり、値が高いほど（>0.7）創造的です。
- **`topP`**: (`number`) 核サンプリング確率。
- **`maxOutputTokens`**: (`number`) 生成された応答長の制限。
- **`thinkingConfig`**: (`object`) 推論機能を持つモデルの構成（例: `thinkingBudget`、`includeThoughts`）。

## 5. 実践的な例

### 決定論的ベースラインの定義

高い精度を必要とするタスクのためにエイリアスを作成し、標準のチャット構成を拡張しますが、温度をゼロに強制します。

```json
"modelConfigs": {
  "customAliases": {
    "precise-mode": {
      "extends": "chat-base",
      "modelConfig": {
        "generateContentConfig": {
          "temperature": 0.0,
          "topP": 1.0
        }
      }
    }
  }
}
```

### エージェント固有のパラメータ挿入

グローバルデフォルトを変更せずに、特定の期間にわたる思考バジェットを特定のエージェント（例: `codebaseInvestigator`）に強制します。

```json
"modelConfigs": {
  "overrides": [
    {
      "match": {
        "overrideScope": "codebaseInvestigator"
      },
      "modelConfig": {
        "generateContentConfig": {
          "thinkingConfig": { "thinkingBudget": 4096 }
        }
      }
    }
  ]
}
```

### 実験的モデル評価

クライアントコードを変更せずに、特定のエイリアスのトラフィックを A/B テスト用のプレビューモデルにルーティングします。

```json
"modelConfigs": {
  "overrides": [
    {
      "match": {
        "model": "gemini-2.5-pro"
      },
      "modelConfig": {
        "model": "gemini-2.5-pro-experimental-001"
      }
    }
  ]
}
```