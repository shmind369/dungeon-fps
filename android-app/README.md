# Wanderer's Dungeon - Android app

`rg.html`（ルートのゲーム本体）をCapacitorでラップし、Androidアプリとして
ビルドするためのプロジェクト。ゲームのロジック自体はここでは変更せず、
`www/index.html` はルートの `rg.html` のコピー＋three.jsをローカル同梱した
ものを置く。

## 構成

- `www/index.html` — `rg.html` のコピー（three.jsはCDNではなく `www/vendor/three.min.js` を参照）
- `www/vendor/three.min.js` — three.js 0.158.0（オフライン動作のためローカル同梱）
- `android/` — Capacitorが生成したネイティブAndroidプロジェクト
- `store-assets/play-store-icon-512.png` — Play Store掲載用アイコン（512x512）
- `store-assets/feature-graphic.png` — Play Store掲載用フィーチャーグラフィック（1024x500）
- `store-assets/screenshots/` — Play Store掲載用スクリーンショット（412x824、縦横比2:1以内）
- `store-assets/store-listing-ja.md` — ストア掲載文の下書き（アプリ名・説明文など）
- ルートの `privacy-policy.html` — プライバシーポリシー（GitHub Pagesで公開）

## rg.htmlを更新したら

ゲーム本体（ルートの `rg.html`）を更新した場合は、このプロジェクトにも
反映が必要:

```sh
cp ../rg.html www/index.html
# CDN参照をローカル同梱に戻す
sed -i "s#https://cdn.jsdelivr.net/npm/three@0.158.0/build/three.min.js#vendor/three.min.js#" www/index.html
npx cap sync android
```

## ビルド

このサンドボックス環境はネットワークポリシーでAndroid SDKの取得元
（dl.google.com）がブロックされているため、ここでは直接ビルドできない。
代わりに `.github/workflows/android-build.yml` のGitHub Actionsで
ビルドする（GitHubのランナーは制限なくインターネットにアクセスできる）。

- Actionsタブ →「Android Build」→「Run workflow」で手動実行
- 実行結果のArtifactsから `.aab`（Play Store提出用）と `.apk`（実機確認用）をダウンロード

## 署名

初回のみ、GitHub ActionsのSecretsに以下を設定する必要がある
（送付した `android-secrets-setup.txt` を参照）:

- `ANDROID_KEYSTORE_BASE64`
- `ANDROID_KEYSTORE_PASSWORD`
- `ANDROID_KEY_ALIAS`
- `ANDROID_KEY_PASSWORD`

署名鍵（upload-keystore.jks）は紛失するとアプリの更新が二度とできなく
なるため、リポジトリには含めず、送付したファイルを安全な場所に必ず
バックアップしておくこと。
