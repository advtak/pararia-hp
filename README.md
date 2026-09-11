# 株式会社パラリア コーポレートサイト

Wix から移行するための静的サイト。GitHub Pages で無料公開する前提で作ってある。
**JavaScript はフォーム2ページの送信完了表示にだけ使っている**（各10行）。他は HTML/CSS のみ。

## 構成

| ファイル | 内容 |
|---|---|
| `index.html` | トップ（ヒーロー・2つの入口・パラリアとは・実践と研究・実績・代表・問い合わせ2窓口） |
| `corporate.html` | 塾の運営支援（塾の内側から・塾の外側から・研究の立場から） |
| `online.html` | オンライン個別指導（代表直轄・特徴・料金・流れ・FAQ） |
| `profile.html` | 代表紹介（本文・実績・考え方） |
| `company.html` | 会社概要（MISSION/VISION/VALUES・事業内容・研究活動・発信・地域と業界での活動・会社情報） |
| `contact.html` | お問い合わせ（2つの窓口へ振り分け） |
| `form-online.html` | 個別指導の問い合わせフォーム |
| `form-corporate.html` | 塾・法人・メディア向けの問い合わせフォーム |
| `assets/style.css` | 全ページ共通のスタイル。**デザインの変更はここ1箇所** |
| `assets/img/` | ロゴ・代表写真・教室写真・研究の図 |

## お問い合わせフォームの仕組み

**見た目は自前HTML、受け皿だけ Google フォーム。** Google フォームを埋め込むと
テーマ色もヘッダーも変えられず（Forms API が未対応）、そこだけ別サイトの顔になるため。

- 送信は `<form action="…/formResponse" target="post-target">` で隠し iframe へ POST。
  ページは遷移せず、完了表示は自前で出す
- 各入力の `name` は `entry.〇〇`。**この番号はフォームごとに固定** なので、
  フォームの質問を作り直したら番号も変わる。変えたら HTML 側も直すこと
- 回答の置き場所（フォームID）
  - 個別指導 `1Iss_XtswJPppDGVKEjocCqXOcIBpcTWSGzGhSuS_HTc`
  - 塾・法人 `1UQG1zRxqAur8lPYKt_5LoK1XTAvZRIxuDvdau99EsnA`
- 作成に使ったスクリプトの考え方は `tools/gauth` の `forms()` 経由。
  再作成が必要なら Forms API のスコープ付きトークン（`gauth.py forms`）が要る

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

### 前提（2026-09-09 / 09-12 に一次情報で確定）

- **ドメインの登録先は Wix ではなく「お名前.com」**（JPRS WHOIS・登録年月日 2016/09/01）。
  Wix はネームサーバーを向けられているだけ。**プラン解約でドメインは失われない**
- 🔴 **有効期限 2026/09/30。** 自動更新がONかは浅見しか確認できない。**切替より先にここ**
- **DNS は Wix の画面を触らない。**お名前.com 側でネームサーバーを自社DNSへ戻し、
  そこにレコードを置く。こうすれば Wix 解約が DNS に一切影響しない
- **正規URLは `https://www.pararia.jp`**（apex は www へ 301。2026-09-12 実測）。
  だから `CNAME` ファイルは `www.pararia.jp`
- **ドメインメールは無い。**MX レコード未設定を実測確認済み（TXT に
  `v=spf1 include:_spf.heteml.jp ~all` が残っているが受信経路は無い）。
  **＝ネームサーバーを移してもメールは壊れない**

### 手順（**この順番**。Wix 解約は最後）

0. **お名前.com にログインし、`pararia.jp` の自動更新をONにする**（浅見のみ）
1. **`robots.txt` を削除する。** 仮URLを検索避けするために置いてある。
   消し忘れると、本番ドメインに切り替えても検索エンジンに載らない
2. リポジトリ直下に `CNAME` ファイルを作り、中身を `www.pararia.jp` の1行にして push
   （※ テスト中は作らない。作ると `advtak.github.io` の URL では見られなくなる）
3. お名前.com で**ネームサーバーをお名前.comのDNS（`01.dnsv.jp`〜`04.dnsv.jp`）に戻す**
4. お名前.com の「DNSレコード設定」で2種類だけ入れる
   - `www` の CNAME → `advtak.github.io`
   - ルート（`@`）の A レコード → `185.199.108.153` / `185.199.109.153` / `185.199.110.153` / `185.199.111.153`
5. GitHub のリポジトリ設定 → Pages → Custom domain に `www.pararia.jp` を入力
6. HTTPS が有効になる（証明書は GitHub が無料で発行）のを確認してから、**Wix のプランを解約する**

⚠️ 反映はネームサーバー変更で最大24〜48時間かかる。**その間は旧Wixサイトが出たままでよい**
（切替が完了してから解約するので、サイトが消える瞬間は作らない）。

## 更新のしかた

HTML を直接編集して push すれば反映される。ビルド作業は不要。
