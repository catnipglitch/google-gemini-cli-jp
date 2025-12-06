# Gemini CLI: ライセンス、利用規約、プライバシー通知

Gemini CLI は、Google の強力な AI サービスとコマンドラインインターフェイスから直接対話できるようにするオープンソースツールです。Gemini CLI ソフトウェアは [Apache 2.0 ライセンス](https://github.com/google-gemini/gemini-cli/blob/main/LICENSE)の下でライセンスされています。
Gemini CLI を使用して Google のサービスにアクセスまたは使用する場合、それらのサービスに適用される利用規約およびプライバシー通知が、そのようなアクセスおよび使用に適用されます。

Gemini CLI の使用統計情報は、Google のプライバシーポリシーに従って取り扱われます。

**注意:** Gemini CLI の使用に適用される割り当てと価格の詳細については、[割り当てと価格](/docs_j/quota-and-pricing.md)を参照してください。

## サポートされている認証方法

認証方法とは、Gemini CLI を使用して Google のサービスにログインし、アクセスするために使用する方法を指します。サポートされている認証方法は次のとおりです：

- Google アカウントで Gemini Code Assist にログインする。
- Gemini Developer API で API キーを使用する。
- Vertex AI GenAI API で API キーを使用する。

上記の Google サービスに適用される利用規約およびプライバシー通知は、以下の表に記載されています。

Google アカウントでログインし、その Google アカウントに関連付けられた Gemini Code Assist アカウントをまだお持ちでない場合は、個人向け Gemini Code Assist のサインアップフローに誘導されます。Google アカウントが組織によって管理されている場合、管理者が個人向け Gemini Code Assist へのアクセスを許可していない可能性があります。詳細については、[個人向け Gemini Code Assist のよくある質問](https://developers.google.com/gemini-code-assist/resources/faqs)を参照してください。

| 認証方法 | サービス | 利用規約 | プライバシー通知 |
| :--- | :--- | :--- | :--- |
| Google アカウント | Gemini Code Assist サービス | [利用規約](https://developers.google.com/gemini-code-assist/resources/privacy-notices) | [プライバシー通知](https://developers.google.com/gemini-code-assist/resources/privacy-notices) |
| Gemini Developer API キー | Gemini API - 無料サービス | [Gemini API 利用規約 - 無料サービス](https://ai.google.dev/gemini-api/terms#unpaid-services) | [Google プライバシーポリシー](https://policies.google.com/privacy) |
| Gemini Developer API キー | Gemini API - 有料サービス | [Gemini API 利用規約 - 有料サービス](https://ai.google.dev/gemini-api/terms#paid-services) | [Google プライバシーポリシー](https://policies.google.com/privacy) |
| Vertex AI GenAI API キー | Vertex AI GenAI API | [Google Cloud Platform 利用規約](https://cloud.google.com/terms/service-terms/) | [Google Cloud プライバシー通知](https://cloud.google.com/terms/cloud-privacy-notice) |

## 1. Google アカウントで Gemini Code Assist にログインした場合

[Gemini Code Assist](https://codeassist.google) にアクセスするために Google アカウントを使用するユーザーには、以下の利用規約およびプライバシー通知文書が適用されます：

- 個人向け Gemini Code Assist:
  [Google 利用規約](https://policies.google.com/terms)および[個人向け Gemini Code Assist プライバシー通知](https://developers.google.com/gemini-code-assist/resources/privacy-notice-gemini-code-assist-individuals)。
- Google AI Pro または Ultra サブスクリプションを伴う Gemini Code Assist:
  [Google 利用規約](https://policies.google.com/terms)、[Google One 追加利用規約](https://one.google.com/terms-of-service)、および [Google プライバシーポリシー\*](https://policies.google.com/privacy)。
- Gemini Code Assist Standard および Enterprise エディション:
  [Google Cloud Platform 利用規約](https://cloud.google.com/terms)および[Google Cloud プライバシー通知](https://cloud.google.com/terms/cloud-privacy-notice)。

*\* アカウントが Gemini Code Assist Standard または Enterprise エディションのアクティブなサブスクリプションにも関連付けられている場合、Gemini Code Assist のすべての使用には、Gemini Code Assist Standard または Enterprise エディションの規約とプライバシーポリシーが適用されます。*

## 2. Gemini Developer API で Gemini API キーを使用してログインした場合

[Gemini Developer API](https://ai.google.dev/gemini-api/docs) の認証に Gemini API キーを使用している場合、以下の利用規約およびプライバシー通知文書が適用されます：

- 利用規約: Gemini CLI の使用は、[Gemini API 利用規約](https://ai.google.dev/gemini-api/terms)に準拠します。これらの規約は、無料サービスを使用しているか有料サービスを使用しているかによって異なる場合があります：
  - 無料サービスについては、[Gemini API 利用規約 - 無料サービス](https://ai.google.dev/gemini-api/terms#unpaid-services)を参照してください。
  - 有料サービスについては、[Gemini API 利用規約 - 有料サービス](https://ai.google.dev/gemini-api/terms#paid-services)を参照してください。
- プライバシー通知: データの収集と使用については、[Google プライバシーポリシー](https://policies.google.com/privacy)で説明されています。

## 3. Vertex AI GenAI API で Gemini API キーを使用してログインした場合

[Vertex AI GenAI API](https://cloud.google.com/vertex-ai/generative-ai/docs/reference/rest) バックエンドの認証に Gemini API キーを使用している場合、以下の利用規約およびプライバシー通知文書が適用されます：

- 利用規約: Gemini CLI の使用は、[Google Cloud Platform サービス規約](https://cloud.google.com/terms/service-terms/)に準拠します。
- プライバシー通知: データの収集と使用については、[Google Cloud プライバシー通知](https://cloud.google.com/terms/cloud-privacy-notice)で説明されています。

## 使用統計情報のオプトアウト

Gemini CLI の使用統計情報を Google に送信しないようにするには、こちらの手順に従ってオプトアウトできます：
[使用統計情報の設定](https://github.com/google-gemini/gemini-cli/blob/main/docs_j/get-started/configuration.md#usage-statistics)