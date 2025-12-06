# Gemini CLI: 割り当てと料金

Gemini CLI は、多くの個人開発者のユースケースをカバーする十分な無料枠を提供しています。企業またはプロフェッショナルな利用、あるいはより高い制限が必要な場合は、認証アカウントの種類に応じていくつかのオプションが利用できます。

プライバシーポリシーと利用規約の詳細については、[プライバシーと規約](./tos-privacy.md)を参照してください。

> **注:** 公開されている価格は定価です。追加の交渉による商業割引が適用される場合があります。

この記事では、Gemini CLI を異なる認証方法で使用する際に適用される特定の割り当てと料金を概説します。

一般的に、選択できるカテゴリは3つあります。

- 無料利用: 実験や軽い使用に最適です。
- 有料ティア (固定価格): より寛大な日次割り当てと予測可能なコストを必要とする個人開発者または企業向けです。
- 従量課金制: プロフェッショナルな使用、長時間実行されるタスク、または使用量を完全に制御したい場合に最も柔軟なオプションです。

## 無料利用

あなたの旅は、実験や軽い使用に最適な十分な無料枠から始まります。

無料利用の制限は、認証タイプによって異なります。

### Google でログイン (個人向け Gemini Code Assist)

Google アカウントを使用して個人向け Gemini Code Assist にアクセスして認証するユーザーの場合。これには以下が含まれます。

- 1日あたりユーザーあたり1000モデルリクエスト
- 1分あたりユーザーあたり60モデルリクエスト
- モデルリクエストは、Gemini CLI が決定するGeminiモデルファミリー全体で行われます。

詳細については、[個人向け Gemini Code Assist の制限](https://developers.google.com/gemini-code-assist/resources/quotas#quotas-for-agent-mode-gemini-cli)をご覧ください。

### Gemini API キーでログイン (無料)

Gemini API キーを使用している場合も、無料枠を利用できます。これには以下が含まれます。

- 1日あたりユーザーあたり250モデルリクエスト
- 1分あたりユーザーあたり10モデルリクエスト
- Flash モデルのみへのモデルリクエスト。

詳細については、[Gemini API レート制限](https://ai.google.dev/gemini-api/docs/rate-limits)をご覧ください。

### Vertex AI でログイン (Express モード)

Vertex AI は、課金を有効にする必要がない Express モードを提供しています。これには以下が含まれます。

- 課金を有効にするまで90日間。
- 割り当てとモデルは変動し、アカウント固有です。

詳細については、[Vertex AI Express モードの制限](https://cloud.google.com/vertex-ai/generative-ai/docs/start/express-mode/overview#quotas)をご覧ください。

## 有料ティア: 固定費用でより高い制限

最初のリクエスト数を使い果たした場合でも、以下のいずれかのサブスクリプションにアップグレードすることで、Gemini CLI を引き続き利用できます。

- [Google AI Pro および AI Ultra](https://gemini.google/subscriptions/)。これは個人開発者に推奨されます。割り当てと料金は固定料金のサブスクリプションに基づいています。

  予測可能なコストのために、Google でログインできます。

  詳細については、[Gemini Code Assist の割り当てと制限](https://developers.google.com/gemini-code-assist/resources/quotas)をご覧ください。

- Google Cloud コンソールで登録することで、[Google Cloud を介して Gemini Code Assist サブスクリプションを購入](https://cloud.google.com/gemini/docs/codeassist/overview)します。詳細については、[Gemini Code Assist の設定](https://cloud.google.com/gemini/docs/discover/set-up-gemini)をご覧ください。

  割り当てと料金は、割り当てられたライセンスシートを持つ固定料金のサブスクリプションに基づいています。予測可能なコストのために、Google でサインインできます。

  これには以下が含まれます。
  - Gemini Code Assist Standard エディション:
    - 1日あたりユーザーあたり1500モデルリクエスト
    - 1分あたりユーザーあたり120モデルリクエスト
  - Gemini Code Assist Enterprise エディション:
    - 1日あたりユーザーあたり2000モデルリクエスト
    - 1分あたりユーザーあたり120モデルリクエスト
  - モデルリクエストは、Gemini CLI が決定する Gemini モデルファミリー全体で行われます。

  [Gemini Code Assist Standard および Enterprise ライセンスの制限](https://developers.google.com/gemini-code-assist/resources/quotas#quotas-for-agent-mode-gemini-cli)について詳しくはこちらをご覧ください。

## 従量課金制

日次リクエスト制限に達した場合、またはアップグレード後も Gemini Pro の割り当てを使い果たした場合、最も柔軟な解決策は、使用した処理量に応じて支払う従量課金制モデルに切り替えることです。これは、中断のないアクセスに推奨されるパスです。

これを行うには、Gemini API キーまたは Vertex AI を使用してログインします。

- Vertex AI (通常モード):
  - 割り当て: 動的な共有割り当てシステムまたは事前に購入されたプロビジョンドスループットによって管理されます。
  - コスト: モデルとトークンの使用量に基づきます。

詳細については、[Vertex AI の動的共有割り当て](https://cloud.google.com/vertex-ai/generative-ai/docs/resources/dynamic-shared-quota)および[Vertex AI の料金](https://cloud.google.com/vertex-ai/pricing)をご覧ください。

- Gemini API キー:
  - 割り当て: 料金ティアによって異なります。
  - コスト: 料金ティアおよびモデル/トークンの使用量によって異なります。

詳細については、[Gemini API レート制限](https://ai.google.dev/gemini-api/docs/rate-limits)、[Gemini API 料金](https://ai.google.dev/gemini-api/docs/pricing)をご覧ください。

API キーを使用する場合、トークン/呼び出しごとに支払うことを強調することが重要です。これは、少量のトークンで多くの小さな呼び出しを行う場合、より高価になる可能性がありますが、割り当て制限によってワークフローが中断されないようにする唯一の方法です。

## Workspace プランの Gemini

これらのプランは現在、Google が提供する Gemini ウェブベース製品（たとえば、Gemini ウェブアプリや Flow ビデオエディタ）の使用にのみ適用されます。これらのプランは、Gemini CLI を駆動する API の使用には適用されません。これらのプランのサポートは、将来のサポートのために積極的に検討されています。

## 高額な費用を避けるためのヒント

従量課金制の API キーを使用する場合、予期しない費用を避けるために使用量に注意してください。

- 特に大規模なコードベースのリファクタリングなど、計算量の多いタスクの場合、すべての提案を盲目的に受け入れないでください。
- プロンプトとコマンドを意図的に使用してください。呼び出しごとに料金を支払うため、最も効率的な方法で作業を完了する方法を検討してください。

## Gemini API と Vertex の比較

- Gemini API (Gemini Developer API): これは、Gemini モデルを直接使用する最速の方法です。
- Vertex AI: これは、特定のセキュリティと制御要件を持つ Gemini モデルを構築、デプロイ、管理するためのエンタープライズグレードのプラットフォームです。

## 使用状況の把握

モデル使用量の概要は、`/stats` コマンドを通じて利用でき、セッション終了時に終了時に表示されます。