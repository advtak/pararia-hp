# 株式会社パラリア コーポレートサイト

Wix から移行するための静的サイト（HTML/CSS のみ・JavaScript なし）。
GitHub Pages で無料公開する前提で作ってある。

## 構成

| ファイル | 内容 |
|---|---|
| `index.html` | トップ（Statement・2つの入口・VMV・VALUES・代表・実績） |
| `corporate.html` | 法人の方へ（選ばれる理由・提供サービス・相談の流れ） |
| `online.html` | オンライン個別指導（代表直轄・特徴・料金・流れ・FAQ） |
| `profile.html` | 代表紹介（経歴・現在の活動・考え方） |
| `company.html` | 会社概要（MISSION/VISION/VALUES・事業内容・会社情報） |
| `contact.html` | お問い合わせ（LINE／メール／電話・2つの窓口） |
| `assets/style.css` | 全ページ共通のスタイル。**デザインの変更はここ1箇所** |
| `assets/img/` | ロゴ・代表写真・教室写真 |

## ブランドカラー

ロゴ原本（`pararia-yoko.png`）から実測した値を正とする。`assets/style.css` の `:root` に定義。

| 用途 | コード |
|---|---|
| ティール（主色・ロゴ本体） | `#279489` |
| ダークティール（ホバー） | `#1E7A71` |
| ミント（スウッシュ） | `#9AD4CC` |
| 罫線・表ヘッダ | `#C7E4E2` |
| 淡い面 | `#EEF7F6` |
| テキスト／サブ | `#333333` ／ `#727272` |

※ 旧資料にある `#1A9A8F` は Wix の変数から抽出した値で、ロゴ原本とは微妙に違う。ロゴ側を正とした。

## 公開手順（GitHub Pages）

```bash
# 1. リポジトリを作って push（advtak アカウント）
cd /c/Users/ataka/Desktop/shared/pararia-hp
gh repo create pararia-hp --public --source=. --remote=origin --push

# 2. GitHub Pages を有効化（main ブランチのルート）
gh api -X POST repos/advtak/pararia-hp/pages -f "source[branch]=main" -f "source[path]=/"
```

数分で `https://advtak.github.io/pararia-hp/` で見られるようになる。

## 独自ドメイン（pararia.jp）への切り替え

現在 `pararia.jp` は Wix のネームサーバー（`ns0.wixdns.net` / `ns1.wixdns.net`）を向いている。
**Wix のプランを解約する前に、この順番でやること。**

0. **`robots.txt` を削除する。** 仮URLを検索避けするために置いてある。
   消し忘れると、本番ドメインに切り替えても検索エンジンに載らない
1. リポジトリ直下に `CNAME` ファイルを作り、中身を `www.pararia.jp` の1行にして push
   （※ テスト中は作らない。作ると `advtak.github.io` の URL では見られなくなる）
2. Wix のドメイン管理画面で DNS レコードを変更
   - `www` の CNAME → `advtak.github.io`
   - ルート（`@`）の A レコード → `185.199.108.153` / `185.199.109.153` / `185.199.110.153` / `185.199.111.153`
3. GitHub のリポジトリ設定 → Pages → Custom domain に `www.pararia.jp` を入力
4. HTTPS が有効になる（証明書は GitHub が無料で発行）のを確認してから、Wix のプランを解約する

⚠️ ドメイン自体が Wix で取得したものである場合、プラン解約でドメインまで失う可能性がある。
解約前に「ドメインの登録は別契約として残るか」を Wix のサポートに確認すること。

## 更新のしかた

HTML を直接編集して push すれば反映される。ビルド作業は不要。
