# Gemini CLI の認証セットアップ

Gemini CLI を使うためには Google での認証が必要です。このガイドはアカウント種類や利用方法に応じた最適なサインイン方法を提示します。

多くのユーザーはローカルマシンで Gemini CLI を起動し、個人の Google アカウントでログインすることを推奨します。

## 認証方法を選択する <a id="auth-methods"></a>

以下の表から自分に合った認証方法を選びます。

| 利用シーン / ユーザータイプ | 推奨認証方法 | Google Cloud プロジェクトが必要か |
| --- | --- | --- |
| 個人の Google アカウント | [Login with Google](#login-google) | いいえ（例外あり） |
| 企業／学校／Google Workspace アカウント | [Login with Google](#login-google) | [はい](#set-gcp) |
| Gemini API キーを使う AI Studio ユーザー | [Gemini API Key](#gemini-api) | いいえ |
| Google Cloud Vertex AI ユーザー | [Vertex AI](#vertex-ai) | [はい](#set-gcp) |
| [ヘッドレスモード](#headless) | [Gemini API Key](#gemini-api) または [Vertex AI](#vertex-ai) | (Gemini API Key は不要、Vertex AI は要) |

### Google アカウントの種類を確認する

- **個人アカウント:** [無料利用枠](../quota-and-pricing/#free-usage) を含むすべてのアカウントおよび [Google AI Pro / Ultra](https://gemini.google/subscriptions/) の有料サブスクリプション。
- **組織アカウント:** 企業、学校、[Google Workspace](https://workspace.google.com/) などの組織でライセンスされたアカウント。[Google AI Ultra for Business](https://support.google.com/a/answer/16345165) も含む。

## (推奨) Google アカウントでログイン <a id="login-google"></a>

ローカルマシンで Gemini CLI を使う場合、最も簡単な方法は Google アカウントでログインすることです。ブラウザ付きのマシンから Gemini CLI を実行してください。

> **重要:** **Google AI Pro** または **Google AI Ultra** のサブスクライバーは、サブスクライブした Google アカウントでログインしてください。

認証手順:

1. Gemini CLI を起動します。

   ```bash
   gemini
   ```
2. 「このプロジェクトでどのように認証しますか？」で **1. Login with Google** を選択。
3. Google アカウントを選ぶ。
4. **Sign in** をクリックし、資格情報がローカルにキャッシュされます。

### Google Cloud プロジェクトは必要か？

個人アカウントの多くは認証時に Google Cloud プロジェクトを要求されませんが、以下の条件に該当する場合はプロジェクトを設定する必要があります。

- 企業／学校／Google Workspace アカウントを使用している。
- Gemini Code Assist ライセンスを Google Developer Program 経由で利用している。
- Gemini Code Assist サブスクリプションのライセンスを利用している。

設定方法は [Google Cloud プロジェクトを設定する](#set-gcp) を参照してください。

## Gemini API キーを使う <a id="gemini-api"></a>

Google アカウントでの認証を避けたい場合は Google AI Studio の API キーを使えます。

1. [Google AI Studio](https://aistudio.google.com/app/apikey) から API キーを取得。
2. 取得したキーを `GEMINI_API_KEY` 環境変数へ設定（例: `GEMINI_API_KEY=xxxx gemini`）。

## Vertex AI 認証 <a id="vertex-ai"></a>

Vertex AI や Google Cloud のサービスアカウントを使う場合は、`GOOGLE_APPLICATION_CREDENTIALS` に JSON キーファイルを指定し、`GOOGLE_CLOUD_PROJECT` でプロジェクト ID を設定します。より柔軟なアクセス制御と監査を行えます。

## ヘッドレスモード <a id="headless"></a>

GUI やブラウザなしで CLI を使う場合、Gemini API キーまたは Vertex AI を使って認証できます。`GEMINI_API_KEY` のみの環境を整えれば、同じマシン上で認証画面を開かずに利用できます。

## Google Cloud プロジェクトの設定 <a id="set-gcp"></a>

1. [Google Cloud Console](https://console.cloud.google.com/) でプロジェクトを作成または選択。
2. `GOOGLE_CLOUD_PROJECT` 環境変数にプロジェクト ID を設定。
3. 必要なら [IAM](https://console.cloud.google.com/iam-admin) でサービスアカウント権限を調整。

---

その他の認証オプションは [Gemini CLI 設定](./configuration.md) や [Vertex AI ドキュメント](https://cloud.google.com/vertex-ai/docs) を参照してください。
