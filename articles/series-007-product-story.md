---
title: "Figma Make で作ったデザインを引き継いで Claude Code で開発する方法"
emoji: "🎨"
type: "tech"
topics: ["figma", "claudecode", "nextjs", "frontend", "design"]
published: true
---

![Figma Make で作ったデザインを引き継いで Claude Code で開発する方法](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/series-007/hero-ogp.png)

<!-- このファイルは gen-publish.mjs が生成した発行用スケルトン。翻訳(海外媒体)は Claude が en.md 経由で行う。 -->

上原正吉（EarthLink Network Co., Ltd.）。Claude Codeを開発の主体に据え、20を超えるプロダクトを1人で同時に開発・運用しています。これは、その現場の実測記です。

<!-- TODO(Claude): この zenn 版は媒体トーン定義（/docs/platform-tone）に合わせてトーン・長さを調整する。方向性: 技術・実装重視。設計判断と数字を厚く。開発者向け。 以下は基となる本文。調整後にこのコメントを消す。 -->

# Figma Make で作ったデザインを引き継いで Claude Code で開発する方法

## 結論

2026 年の初めから、私は Figma Make が書き出したコードを起点にした Web アプリを何本も立ち上げました。美容キュレーションサイト、贈り物記録アプリ、ぬいぐるみアルバム、AI 開発ツールの UI モック、SEO 運用ツール。どれも「見た目は完成している」状態でリポジトリが始まります。

![Figma Make が作ったモバイル UI の一例（実プレビュー）。プロンプトで指示するだけで、ここまで「見た目は完成」した画面が出てくる。問題は、この裏にあるコードをどう Claude Code へ引き継ぐか、です（実スクリーンショット）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/series-007/figma-ui-result.png)

この記事は、Figma Make から Claude Code へデザインを引き継いで開発する「渡し方」と、そのために Figma Make へ最初から言っておくべき指示をまとめたものです。後半では、この手順で実際に作った複数のプロジェクトが、どこまで進んでどこで止まったのかを、設定ファイルとコミット履歴から振り返ります。読了は約 12 分。

> **📌 この記事の最後に、私が実際に Figma Make へ貼り付けたプロンプトの完全な実例を置いています。**
>
> 先に完成したプロンプトの実例から見たい方は、そこから読んでも構いません。

先に要点を置きます。

- **GitHub を通じて渡す** — Figma Make を GitHub に接続してリポジトリにし、それを Claude Code に読ませて「解析して開発を引き継げる状態にして」と頼む。既存プロジェクトに取り込むときも、その design リポジトリを Claude Code に読ませる、という点は同じです。
- **肝は「引き継ぎやすい構成にしておくこと」——それを prompt で作らせる** — Figma Make はデザインを作るツールです。だから、引き継ぎやすい成果物（atomic design の構成・デザインから起こしたトークン・フォルダ構成・規約・残したプロンプト・README の要約）は、こちらが prompt で指示して初めて出てきます。**この記事の最後に、その指示を 1 枚にまとめた完全なプロンプトを置きます。**
- **やるのは「引き継ぎ前」か「デザインが一息ついた時」** — 最初のプロンプトに全部を盛る必要はありません。デザインが固まってきたところで「atomic design にして・デザインからトークンを起こして・フォルダ構成をこうして・規約とプロンプトを残して」と整えておくと、引き継ぎが軽くなります。
- **引き継いだ直後の最初の仕事は、機能追加ではなく DRY 化** — 重複の統合・トークンの配線・巨大画面の分解・ビルドの再接続。ここを飛ばすと、見た目は完成しているのに何ヶ月も進みません。

## 本文（読了 約12分）

![Figma Make → GitHub → Claude Code の引き継ぎフロー。渡し方は 1 つ（GitHub 経由）。最初の履歴では作者が figma[bot] で、`Add files from Figma Make` が出発点。引き継ぎ直後の最初の仕事は機能追加ではなく DRY 化。既存プロジェクトでも同じで、design リポジトリを Claude Code に読ませ、スナップショットとして残す](../assets/SERIES-007/handoff-flow.png)

## 渡し方: GitHub を通じて Claude Code へ

手順はシンプルです。Figma Make で画面を作ったら、画面右上から **GitHub リポジトリに接続**します。すると Figma Make が自分でリポジトリへ書き込みます。最初のコミット履歴を見ると、作者が `figma[bot]` になっています。

```text
30bb071 Initial commit                    (figma[bot], 2025-12-10)
5ba6dfe Add files from Figma Make         (figma[bot], 同日)
81d2e7e Update files from Figma Make       (figma[bot], 同日)
```

この `Add files from Figma Make` が引き継ぎの出発点です。あとはこのリポジトリを Claude Code で読み込んで、「このコードを解析して、開発を引き継げる状態にして」と頼めば始められます。書き出されたコードはそのまま `npm i && npm run dev` で動きます（`vite.config.ts` が版番号付きの import をローカルのパッケージへ alias しているため、追加設定なしで起動します）。

Figma Make の画面右上には、デザインのプレビュー（👁）とは別に **`<>`（コード）のビュー**があります。ここを開くと、**デザインだけでなく、実際に動くコードが atomic design のフォルダ構成で作られている**のが見えます。これがそのまま Claude Code に渡せる資産です。

```text
src/
├─ app/
│  ├─ App.tsx                 # ルーティング（全ページの定義）
│  └─ components/
│     ├─ atoms/               # Button / Badge / Input …（最小単位）
│     ├─ molecules/           # SearchBar / FormField / Card …
│     ├─ organisms/           # Header / ThreadList / ChatView …
│     ├─ templates/           # BaseLayout / TwoColumnLayout …
│     ├─ ui/                  # shadcn/ui プリミティブ一式
│     └─ figma/               # ImageWithFallback（Figma Make 定番）
│  ├─ data/                   # モックデータ
│  └─ styles/                 # theme.css（デザイントークン）ほか
├─ imports/                   # ← Figma に貼ったプロンプトを保存させた場所
│  └─ pasted_text/
├─ guidelines/Guidelines.md   # ← 規約を書かせる場所
├─ README.md                  # ← Quick Summary / Ask Human を書かせる
├─ FIGMA_MAKE_PROMPT.md       # ← 次に渡すプロンプトそのもの
├─ vite.config.ts  package.json
```

この構成は Figma Make が勝手にこう並べたのではなく、**「atomic design で・このフォルダ構成で・プロンプトと規約を残して」と指示した結果**です（指示の中身は次節）。裏がこういうコードになっているからこそ、Claude Code は「どこが atoms で、どこにトークンがあって、なぜこの UI なのか」を読み解けます。

![Figma Make の実画面（`<>` コードビュー）。① 左上の `<>` がコードビュー（プレビューの隣）、② 赤枠が atomic design のフォルダ（atoms / molecules / organisms / ui …）、右は App.tsx が `./components/organisms/…` から import している実コード。デザインの裏はそのままコードで、この atomic 構成は「そう指示したから」この形で残っている（実スクリーンショット）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/series-007/figma-code-view.png)

**すでに動いているプロダクトに取り込む場合も、やることは同じ**です。Figma のデザインを別リポジトリとして受け取り、メインのリポジトリの側から「この design リポジトリを読んで解析し、うちの構成に取り込め」と Claude Code に頼むだけ。実際、ある SEO 運用ツールでは `design/prototype/`（Figma Make から受け取ったコードを保存したスナップショット）と `apps/seo-admin/`（本開発）を分けて、コミット履歴に引き継ぎの流れをそのまま残しました。

```text
Add files from Figma Make
migrate Figma prototype UI to seo-admin
promote Figma prototype to seo-admin, backup legacy
```

受け取ったコードをスナップショットとして残したまま本開発を別ディレクトリで進めれば、後からデザインの出発点を参照でき、Figma を再生成してもメインのコードを上書きしません。ここは実際に 180 コミットまで育ち、モックから実バックエンド（マルチテナント・外部データ取り込み・インフラ）まで進みました。新規でも既存でも、**「Claude Code に Figma Make が書き出したリポジトリを読ませる」という一点は変わりません**。

## 「引き継ぎやすい成果物」は、そう指示したから出てくる

ここは誤解しやすいところです。Figma Make の裏側では Claude（Anthropic のモデル）が動いていて、出力は React + TypeScript、Tailwind CSS、shadcn/ui という Claude Code が扱い慣れたスタックで出てきます。ここまでは Figma Make の標準です。

しかし——**引き継ぎ用のドキュメントは、Figma Make が勝手に付けてくれるものではありません**。私が受け取ったリポジトリに付いていた次のものは、すべて私が「これを作れ」とプロンプトで指示したから存在します。

- `README.md` 末尾の **`Quick Summary for Claude Code`** 節（次に読む Claude Code へ向けた要約）
- 同じく README の **`Ask Human`** 節（バックエンドは Supabase か Firebase か、データ永続化・認証・本番環境をどうするか——人間に確認すべき未確定事項の列挙）
- `ATOMIC_DESIGN.md`、`SCREEN_SPEC.md`（ある案件では 921 行）、`DESIGN_SUMMARY.md`、そして **`FIGMA_MAKE_PROMPT.md`（次に渡すプロンプトそのものを repo に残したもの）**

![実際のファイルツリー（`<>` コードビュー）。赤枠が `imports/` に残った md 群（貼り付けたプロンプト・`DESIGN_SUMMARY.md`・`FIGMA_MAKE_PROMPT.md`・`MOBILE_DESIGN_BRIEF.md`）、橙枠が `guidelines/`。これらは Figma Make が自動で付けたものではなく、「これを作れ」とプロンプトで指示したから残っている（実スクリーンショット）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/series-007/figma-imports.png)

Figma Make はあくまで **デザインを作るシステム**であって、引き継ぎのために作られてはいません。だから発想を逆にします——「こういう成果物があると Claude Code に引き継ぎやすい」なら、**それを Figma Make にプロンプトで作らせればいい**。次の節が、その具体的な指示です。そして記事の最後に、指示を 1 つにまとめた完全なプロンプトを置きます。

## Figma Make に最初から言っておく指示

見た目の出力は変えなくても、**中身の作られ方を変える** prompt が通ります。最初にこれを言うかどうかで、引き継いだ後の苦労がまるで違います。

- **「atomic design で作れ」**。言わないと、同じような UI を別々に作り、1 か所直しても他に反映されない状態になります（実例は後述）。
- **「作ったデザインから design system（デザイントークン）を起こせ。色や余白をハードコードするな」**。ここが要点で、**デザインシステムをこちらで用意するのではなく、Figma Make が作ったデザインから Figma Make 自身にトークンを抽出させる**のです。実際に効いた指示（SEO 運用ツールの改善プロンプト）:

```text
0) design system — このデザインから抽出して token 化せよ:
   - 8px グリッド / カード余白 16・24 の2段階
   - タイポ階層（H1 24–28 Bold …）
   - 色の意味を固定（成功=Green / 注意=Amber / 失敗=Red / 情報=Blue / 保留=Gray）
   - バッジ（高さ24・角丸12）・テーブル（行高40・数値右寄せ）を標準化
   - 以後コンポーネントは token を参照する（hex/px の直書き禁止）
```

- **「既存のデザインテンプレート／テイストを踏襲しろ」**。ぬいぐるみアルバムでは、機能を追加するたびにプロンプト冒頭へ「既存のデザインテイスト（やさしい色合い、写真主体、余白多め、角丸カード UI）は維持する」と毎回置きました。これで反復しても見た目が一貫しました。密度の基準として「Linear / Vercel / Stripe くらいの密度で」と既存プロダクトを参照させるのも効きます。
- **「規約を `guidelines/Guidelines.md` に書け」**。Figma Make は空の `guidelines/Guidelines.md`（先頭が `Add your own guidelines here`）を必ず置きます。ここに規約を書かせれば Figma もそれに従いますが、私が見た全プロジェクトでこのファイルは空のまま放置されていました。**ここを埋めさせるだけで差が出ます**。
- **「打ったプロンプトを `src/imports/` に残せ」**。Figma Make は貼り付けたプロンプトをファイルとして保存できます。これを消さずに残させておくと、後から「なぜこの UI なのか」を Claude Code も人間も追えます。SEO 運用ツールでは 16 本のプロンプト（redesign → refine → refactor の反復）がそのまま残り、デザインの意図の記録になっていました。
- **「引き継ぎ用の要約（Quick Summary for Claude Code）と、人間に聞くべきこと（Ask Human）を README に書け」**。前節の 2 つの節は、この指示で出させたものです。「引き継ぎやすい成果物」は、作れと言って初めて出てきます。
- **フォルダ構成を先に渡す**。決まった構成があるなら「この構成にしろ」と最初に指定します。

これらの指示は、毎回チャットに打ち込む必要はありません。1 枚の md ファイルにまとめておき、`FIGMA_MAKE_PROMPT.md` として渡すのが実際のやり方です。

![プロンプトを md ファイル化して Figma Make に渡している場面。`FIGMA_MAKE_PROMPT.md` をチャットに添付し、「この md ファイルの内容で対応してください」と流している（赤枠・赤矢印は元画像のまま）。指示をファイルに残して渡せば、同じ指示を後からも再利用できる（実スクリーンショット）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/series-007/figma-paste.png)

## それでも起きる「1 か所直すと壊れる」——実例

ここが、上原が Figma に必ず atomic design を指示する理由です。**「atomic design で作れ」と言っても、フォルダ構造だけ atomic で、中身が配線されていない**ことが多い。引き継いだ Claude Code が最初にやるべきは、この配線を直すことです。実際に出たパターンを挙げます。

![Figma Make 自身に「既存のデザインにモバイルを足すには」と相談したときの回答。① 1 ファイルに breakpoint を足す案を「全 Organisms を触るので AI が介入するたびに壊れるリスクが高い」と自ら退け、② コンポーネントを分離する案を推奨している。裏の構成しだいで、後からの壊れやすさが変わる（実スクリーンショット）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/series-007/figma-existing-options.png)

- **同じ概念が 2 つの箱に重複していた**。贈り物記録アプリは個人モードと法人モードで別々の atomic ツリーを持ち、`EventCard` と `CorporateEventCard`、`PersonListItem` と `CorporateClientListItem` が二重に定義されていました。低層の部品を共有していないので、片方を直しても他方は変わりません。どこからも import されない部品（`CorporateStatCard`）も残っていました。
- **トークンを定義したのに配線していなかった**。ぬいぐるみアルバムは `theme.css` にアースカラーのトークンを定義したのに、コンポーネント側は同じ色を直接ハードコード（35 ファイル）。`var(--color-*)` を参照するファイルは 0 でした。だから `theme.css` の 1 トークンを直しても、画面には伝わりません。
- **2 系統のボタンが並立していた**。同じアプリで、同梱の shadcn/ui のボタンと独自のボタンが両方存在し、46 個の shadcn 部品はほとんど使われないまま残っていました。
- **ビルドが繋がっていなかった**。AI 開発ツールのモックは、実行時に効くのが焼き込み済みの巨大な CSS で、`tailwindcss` すらインストールされていませんでした。`globals.css` のトークンを直しても再ビルドされません。Claude Code で開発を始める前に、まず Tailwind ビルドの再接続が必要でした。
- **画面が巨大モノリスのまま**だった。1 画面が 800〜900 行（約 40 KB）まで肥大化して分割されずに残り（設定画面が 888 行・約 42 KB）、別の画面が共通部品と同名の `TypeBadge` を独自に再定義していたため、「バッジを 1 か所直しても、もう片方は直らない」状態でした。

だから **引き継ぎ直後の最初のコミットは、機能追加ではなく DRY 化から始めるのが正解**です。重複の統合、トークンの配線、巨大画面の分解、ビルドの再接続。ここを片付けてから機能に入ります。

## 実例: それぞれどこまで進んで、どこで止まったか

ここでは、先ほどの SEO 運用ツール（`design/prototype/` を凍結して本開発まで進めた大きめの例）とは別に、Figma Make 起点で立ち上げた 4 つのマイクロプロダクトを並べます。同じ起点でも、どこまで進むかは中身次第で分かれました。

**この 4 本の中で一番進んだのは美容キュレーションサイト**です。静的サイトとして成立するので、Next.js を静的書き出しにして Amplify に載せました。`CLAUDE.md` を置いて Claude Code への常設指示も整えています。

```ts
// next.config.ts — 静的サイトとして書き出す
const nextConfig: NextConfig = {
  output: "export",
  images: { unoptimized: true },
  trailingSlash: true,
};
```

`output: "export"` で SSR を持たない純静的サイトにし、`images.unoptimized` で画像最適化の Lambda も外す。こうすると配信側は書き出した `out/` を配るだけになり、運用が軽くなります。

**データ層の手前で止まったのが、贈り物記録アプリとぬいぐるみアルバム**です。画面と遷移は完成しているのに、永続化・認証・通知が未着手のまま。README の `Ask Human`（Supabase か Firebase か、認証方式は何か）が未回答で止まりました。見た目の残り 1 割に見える土台に、工数の大半があります。

**AI 開発ツールの UI モックは、モックのまま**です。Figma Make から取り込んだあと、機能開発のコミットは積まれておらず、UI の段階から先へは進んでいません。

4 本がどこまで進んだかを、git 履歴と設定ファイルから読み取れる事実だけで並べます（進捗率のような作った数値は載せません）。

| プロダクト | どこまで進んだか | どこで止まったか（根拠） |
|---|---|---|
| 美容キュレーションサイト | 静的サイトとして配信（Amplify に掲載） | 到達済み。`next.config.ts` が `output: "export"` ／ `CLAUDE.md` も設置 |
| 贈り物記録アプリ | 画面と遷移は完成 | 永続化・認証・通知が未着手。README の `Ask Human`（Supabase か Firebase か）が未回答 |
| ぬいぐるみアルバム | 画面と遷移は完成 | 同じくデータ層の手前で停止。トークンを定義しても `var(--color-*)` 参照は 0 ファイル |
| AI 開発ツールの UI モック | Figma Make から取り込み済み | 機能開発のコミットが積まれていない（UI の段階のまま） |

引き継いだリポジトリは、その後まとめて共通のセルフホスト・セキュリティ CI に載せました。このとき全リポジトリで同じ問題が出ます——Figma Make が書き出すコードは ESM 前提（`"type": "module"`）なので、CI に貼る CommonJS のスクリプトを `.js` のまま置くと `require is not defined` で落ちる。横断で `.cjs` にリネームして直しました。Figma Make で作ったコードを本番で使うには、機能を足すだけでなく、こうした運用ルールに合わせてそろえる必要があります。

## 転用できる教訓

- **渡し方は 1 つ**。Figma Make を GitHub に接続 → そのリポジトリを Claude Code に読ませて「解析して引き継げる状態にして」と頼む。既存プロジェクトでも同じ（design リポを読ませる）。受け取ったコードはスナップショットとして残す。
- **引き継ぎやすい成果物は、勝手には出てこない——作らせる**。atomic design で作れ／作ったデザインから design system を起こしハードコードするな／既存テイストを維持しろ／規約を `Guidelines.md` に書け／打ったプロンプトを `src/imports/` に残せ／引き継ぎ用の要約と Ask Human を README に書け。Figma Make はデザインを作るシステムであって、引き継ぎツールではない。
- **引き継ぎ直後の最初の仕事は DRY 化**。重複の統合・トークンの配線・巨大画面の分解・ビルドの再接続。機能追加はその後。
- **見た目の完成度に騙されない**。永続化・認証・通知が残ると、そこで止まります。README の `Ask Human` に早く答えることが、モックから製品への分かれ目になります。

## 最後に: Figma Make に渡すべきプロンプトの完全な実例

Figma Make に貼り付ける「引き継ぎ前提のプロンプト」は、次の形です。実際に私が Prompt Flow というツールのモバイル設計で使ったものを、汎用化して載せます（本物はこの骨格に全画面の仕様——画面ごとの ID・状態・遷移——を足した約 720 行で、それ自体を `FIGMA_MAKE_PROMPT.md` として repo に残しています）。

![実際の `FIGMA_MAKE_PROMPT.md`（右ペイン）。プロンプトそのものを repo に置き、左のチャットで「この md ファイルの内容で対応してください」と Figma Make に貼って流している。Goal / Core principle / Do NOT … / Global product rules と、引き継ぎに必要な制約が 1 ファイルにまとまっている（実スクリーンショット）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/series-007/figma-prompt-file.png)

下は、上のファイルを汎用化した骨格です。そのままコピーして、自分のプロダクト名と画面一覧を足せば使えます。

```text
You are producing a design AND a clean handoff for Claude Code.

Goal:
- Build the UI in React + TypeScript + Tailwind + shadcn/ui.
- The output will be handed to Claude Code for development, so make it
  legible and safe to take over — not just visually done.

Architecture:
- Use Atomic Design with REAL reuse, not just folders:
  atoms → molecules → organisms → templates → pages.
  Never define the same component twice (no Card and CardV2).
- Derive a design system FROM this design: put every color, spacing,
  radius, and type step into tokens (theme.css). Components must
  reference the tokens — never hardcode hex or px.
- Fix color meanings: success=Green / warning=Amber / error=Red /
  info=Blue / muted=Gray. Standardize badges and tables.

Handoff artifacts — create these files:
- README.md with a "Quick Summary for Claude Code" section and an
  "Ask Human" section listing every unresolved decision
  (backend? persistence? auth? hosting?).
- guidelines/Guidelines.md filled with the rules above
  (do not leave the template empty).
- Keep every prompt I give you under src/imports/, so the design
  intent stays inside the repo.
- SCREEN_SPEC.md: for each screen, its purpose, states
  (loading / empty / error), and transitions.

Constraints:
- Ship a runnable repo: `npm i && npm run dev` must work.
- Do not blindly port every screen; build only what belongs here.
- Every screen needs loading / empty / error / long-content variants.
```

この 1 枚があるだけで、Claude Code に渡したときに「まず何を DRY 化すべきか」「まだ決まっていないのは何か」が repo の中で完結します。逆にこれを言わずに作らせると——前述のとおり——見た目は完成しているのに配線されていないコードが出てきて、引き継ぎで止まります。渡すタイミングは最初のプロンプトである必要はありません。**引き継ぎの前、あるいはデザインが一息ついた時点で**、このプロンプトを一度通しておけばいい。Figma Make はデザインを作るところまで。そこから先を軽くするのは、この一手間です。

## 筆者について

上原正吉。EarthLink Network Co., Ltd. でAI開発をしています。2025年からClaude Codeを開発の主体に据え、今は20を超えるプロダクトを1人で同時に開発・運用しています。この連載では、その現場で実際に起きたこと（うまくいったことも、失敗も）を、数字と一緒に書いていきます。

また、AIで業務や開発を組み替えたい会社・チーム向けに、AI活用のコンサルティングも受け付けています。ご相談は [www.eln.ne.jp](https://www.eln.ne.jp) からどうぞ。

---

**EarthLink Network** は、会社の全業務を AI で回すために、必要になったものを自社で作っています。いま作っているプロダクトの一覧と概要は、こちらにまとめています。

→ [EarthLink Network が自社でつくっている18のプロダクト](https://zenn.dev/chooser/articles/in-house-products)

会社と各プロダクトの詳細は、公式サイト [www.eln.ne.jp](https://www.eln.ne.jp) をご覧ください。
