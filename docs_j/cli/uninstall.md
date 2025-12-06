# CLI のアンインストール

アンインストール方法は、CLI の実行方法によって異なります。npx またはグローバル npm インストールのいずれかの手順に従ってください。

## 方法1: npx を使用する

npx は、永続的なインストールなしに一時的なキャッシュからパッケージを実行します。CLI を「アンインストール」するには、このキャッシュをクリアする必要があります。これにより、gemini-cli と npx で以前に実行された他のすべてのパッケージが削除されます。

npx キャッシュは、メインの npm キャッシュフォルダ内にある `_npx` という名前のディレクトリです。`npm config get cache` を実行することで、npm キャッシュパスを見つけることができます。

**macOS / Linux の場合**

```bash
# パスは通常 ~/.npm/_npx です
rm -rf "$(npm config get cache)/_npx"
```

**Windows の場合**

_コマンドプロンプト_

```cmd
:: パスは通常 %LocalAppData%\npm-cache\_npx です
rmdir /s /q "%LocalAppData%\npm-cache\_npx"
```

_PowerShell_

```powershell
# パスは通常 $env:LocalAppData\npm-cache\_npx です
Remove-Item -Path (Join-Path $env:LocalAppData "npm-cache\_npx") -Recurse -Force
```

## 方法2: npm を使用する (グローバルインストール)

CLI をグローバルにインストールした場合（例: `npm install -g @google/gemini-cli`）、`-g` フラグを付けて `npm uninstall` コマンドを使用して削除します。

```bash
npm uninstall -g @google/gemini-cli
```

このコマンドは、システムからパッケージを完全に削除します。