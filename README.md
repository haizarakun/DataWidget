# DataWidget（回線残量ウィジェット）

docomo / ahamo など日本の主要キャリア・MVNOの「残りデータ量」を、キャリア公式アプリを開かずホーム画面ウィジェットで確認できるAndroidアプリ。

- パッケージ名: `com.akun.docomodata`
- 対応: Android 5.0 (API21) 以上
- 提供形態: APK直接配布（Google Play非公開）

## 特徴

- プロファイル制で複数キャリア回線を同時管理、ウィジェットごとに紐付け可能
- ワンタップ更新 / しきい値通知 / 定時サマリー通知
- 6種テーマ + カスタムカラー対応
- 日本の主要キャリア/MVNO 20種のURLプリセット
- 設定JSONのエクスポート/インポート
- アプリロック（PIN、SHA-256ハッシュ保存）
- 日本語/English切替
- ウィジェットは1×1相当の極小サイズから横長サイズまで自動でレイアウト調整

## 必要な権限

| 権限 | 用途 |
|---|---|
| INTERNET | キャリアマイページへのアクセス |
| ACCESS_NETWORK_STATE | 通信状態の確認 |
| RECEIVE_BOOT_COMPLETED | 再起動後の定期更新再開 |
| FOREGROUND_SERVICE | データ取得中のフォアグラウンド処理 |
| WAKE_LOCK | 取得処理中のスリープ防止 |
| POST_NOTIFICATIONS | しきい値/定時サマリー通知の表示（Android 13+） |

WebViewでキャリアのマイページに直接ログインし、Cookieセッションを使ってページを解析する方式のため、ID/パスワードは本アプリ・開発者のいずれにも送信されません（すべて端末内・WebView内で完結）。

## インストール方法

Google Play非公開のため、APKを直接インストールする。

### 初回インストール

1. [Releases](../../releases) から最新の `DataWidget.apk` をダウンロード
2. 端末の設定で「提供元不明のアプリ」のインストールを許可
3. ダウンロードしたAPKをタップしてインストール
4. ホーム画面を長押し→ウィジェット→「回線残量ウィジェット」を配置
5. 初回はプロファイル作成（キャリアのログインURL/マイページURLを設定）が必要

### アップデート

v1.0.0以降は固定の署名鍵で配布しているため、[Releases](../../releases) から新しいAPKをダウンロードしてそのままインストールするだけでよい。アンインストールは不要で、プロファイル設定もそのまま引き継がれる。

> それより前の古いビルド（署名鍵が異なるバージョン）を使っている場合のみ、一度アンインストールしてから最新版を入れ直す必要があります。それ以降は本手順の対象外です。

## 免責

キャリア公式サイトの構造変更により、データ取得（正規表現）が動かなくなる場合があります。非公式ツールのため、利用は自己責任で。

## ライセンス

本リポジトリのコードは独自ライセンス（[LICENSE](./LICENSE)参照）です。
個人利用目的での閲覧・改変・他ソフトへの組み込みは自由ですが、
**再配布・販売は著作権者の事前許可が必要**です。
---

## GitHubでの配布手順（開発者向けメモ）

このリポジトリはソース非公開でAPKのみをリリースとして配布する運用を想定。

### 1. リリースの作成とAPKの添付

```bash
git tag v1.0.0
git push origin v1.0.0

gh release create v1.0.0 DataWidget.apk \
  --title "v1.0.0" \
  --notes "初回リリース"
```

以降、新しいビルドができたらバージョンを上げて同じ手順を繰り返す:

```bash
git tag v1.1.0
git push origin v1.1.0
gh release create v1.1.0 DataWidget.apk --title "v1.1.0" --notes "更新内容をここに"
```

### 2. READMEのダウンロードリンク

```markdown
[⬇ 最新版をダウンロード](https://github.com/haizarakun/DataWidget/releases/latest/download/DataWidget.apk)
```

### 注意点

- 本プロジェクトはソースがJavaではなくsmali（apktool decode）ベースで管理されているため、`git`にはビルド成果物（APK）とこのREADMEのみをコミットする運用が現実的
- 継続配布用に固定のリリースkeystore（`datawidget-release.keystore`）を使用。**紛失すると次回以降は別の鍵で署名することになり、ユーザーは更新のたびにアンインストール→再インストールが必要になります**。安定リリースを維持するため、この鍵は必ず保管し毎回使い回すこと

#### リリース鍵での署名コマンド（次回以降のビルド時）

```bash
java -jar uber-apk-signer.jar --apks DataWidget_unsigned.apk \
  --out signed \
  --ks datawidget-release.keystore --ksAlias datawidget \
  --ksPass "DataWidget2026Release!" --ksKeyPass "DataWidget2026Release!"
```
