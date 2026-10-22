---
title: "セキュアな環境変数のチーム共有のすすめ ── dotvault とは何か、この連載で何ができるようになるか"
emoji: "📝"
type: "tech"
topics: ["dotvault", "secret-management", "cli"]
published: true
---

<!-- このファイルは gen-publish.mjs が生成した発行用スケルトン。翻訳(海外媒体)は Claude が en.md 経由で行う。 -->
<!-- gen-publish:src-sha256=8ab1ab8e0ae7dc56ab3e12388f4e8ffd24e48f4ce4717e85e6b6075b5fad742a -->

上原正吉（EarthLink Network Co., Ltd.）。Claude Codeを開発の主体に据え、20を超えるプロダクトを1人で同時に開発・運用しています。これは、その現場の実測記です。

<!-- TODO(Claude): この zenn 版は媒体トーン定義（/docs/platform-tone）に合わせてトーン・長さを調整する。方向性: 技術・実装重視。設計判断と数字を厚く。開発者向け。 以下は基となる本文。調整後にこのコメントを消す。 -->

# セキュアな環境変数のチーム共有のすすめ ── dotvault とは何か、この連載で何ができるようになるか

![セキュアな環境変数のチーム共有のすすめ ── dotvault とは何か、この連載で何ができるようになるか](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/dvlt-001/hero-ogp.png)

この記事は、連載「dotvault の使い方 ── .env を手で配るのをやめ、CI・チーム・AI エージェントに 1 コマンドで配るまで」の第 1 回です。この連載が終わる時に、dotvault を誰でも無料で使える形で公開する予定です。

新しく入った人に、`.env` をどう渡していますか。Slack の DM に貼る。1Password の共有フォルダに置く。隣の席なら USB メモリで渡す。私も、長いあいだ Slack の DM で渡していました。渡した後に値が変わると、また DM で知らせます。CI（コードの変更を自動で検証する仕組み）には、設定画面に 1 つずつ貼ります。AI エージェントに手元の開発を手伝ってもらう時は、チャットに `.env` の中身を貼ることになります。

「secret を管理するサービスを入れれば直る」と分かっていても、手が止まる理由があります。大きなチーム向けに見える。新しい運用を全員に求めることになる。無料だと監査が付かない。その結果、Slack の DM が今日も続きます。

この連載で取り上げる dotvault は、その手が止まる理由を 1 つずつ無くす形で作りました。受け取る側は、email とパスワードで一度 `dotvault login` をすれば、あとは `dotvault pull` の 1 コマンドで受け取れます。そして 11 月に、誰でもアカウントを作って無料で使える形で公開します。

この 1 回目では、dotvault が何か、なぜ作ったか、用語、そして 2 回目から 12 回目で何ができるようになるかを書きます。手順は書きません。手順は 2 回目から、画面を 1 枚ずつ付けて進めます。

## 結論

- **dotvault は、`.env`・設定ファイル・key と value の secret をサーバー側に置き、権限のある人・CI・AI エージェントが 1 コマンドで取り出すサービスです。** email とパスワードでログインし、ログイン後の token は OS の keychain（macOS なら「キーチェーン」。パスワードを安全にしまう OS の仕組み）に入ります。
- **受け取る側は、ログインした後は `dotvault pull` の 1 コマンドです。** `.env` のファイルを作らずに起動したい時は `dotvault run -- <コマンド>` を使います。手順は 2 回目で、画面と一緒に書きます。
- **CI と AI エージェントは、人のパスワード無しで受け取ります。** CI には service token を 1 つ渡します。AI エージェントには、project を作る token と secret を読む token の 2 本を渡します。それぞれ 7 回目と 8 回目で書きます。
- **人を入れ、見せる範囲を決め、抜けた人を止めるところまで、同じ画面でできます。** 役割は owner / admin / writer / reader の 4 つです。誰にどこまで見せるかの付け方は、9 回目と 10 回目で画面と一緒に書きます。
- **11 月に、dotvault を無料で公開します。** 以前の記事で紹介した dotvault を、誰でも自分でアカウントを作り、無料で使える形で一般公開します。無料の範囲は、自分だけで使う Personal で project と key が無制限、チームで使う Company の無料プランで 10 人・25 project・監査の記録 14 日です。

## 本文（読了 約9分）

## 前提と注意

- **読者。** 開発を自動化したい開発者、個人事業主、小さなチームの方です。secret 管理の専門の方ではなく、今日も `.env` を手で渡している方を考えています。
- **日付。** 画面と数字は、2026 年 10 月 17 日に本番の app.dotvault.io と、公開されている最新の CLI（0.15.7）で確かめました。無料の範囲の数字は、公開に向けて決めた値です。公開の日に、実際の画面でもう一度確かめて 12 回目に書きます。
- **画面は英語です。** 管理コンソールは英語と日本語を選べます。この連載の画面は、読者の多い英語で撮っています。日本語の表示に切り替える操作は 2 回目の付録で書きます。
- **デモのアカウントです。** 画面に写るメールアドレスや値は、この連載のために作ったデモのものです。お客様の組織や値は写していません。
- **手順はこの回に書きません。** インストール・ログイン・最初の pull は、2 回目にまとめます。この回は、全体を 1 枚で見渡すための回です。
- **用語。** この記事では、API キーやパスワード、接続先の URL のように、外に出してはいけない値をまとめて「secret」と書きます。`.env` は、その secret を `KEY=VALUE` の形で 1 行ずつ並べたファイルです。

## dotvault とは何か

dotvault は、`.env`、任意の設定ファイル、そして key と value の形の secret をサーバー側（暗号化して保管）に置き、権限のある人・CI・AI エージェントが 1 コマンドで取り出すサービスです。管理コンソール（ブラウザで開く管理画面）と、端末で打つ CLI（command line interface。コマンドで操作する入口）の 2 つで使います。

管理コンソールのログイン画面の入力欄は、email とパスワードの 2 つです。

![app.dotvault.io のログイン画面（英語 UI・2026 年 10 月 17 日）。「Sign in with email and password」の見出しの下に Email と Password の 2 つの欄と Sign in のボタンがある。パスワードを忘れた時のリンクと、質問のリンクが下に並ぶ。](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/dvlt-001/1-1-login.png)

CLI では、`dotvault --help` の 1 行目が、そのまま説明になっています。

> dotvault — sync .env / .npmrc / .gitconfig and any config file across machines and CI/CD. No AWS account required.

`.env` だけでなく、`.npmrc` や `.gitconfig` のような設定ファイルも、そのまま同じ経路で配れます。ログインに使った token は OS の keychain に入り、`.env` のように平文のファイルで手元に残りません。

この連載で言う「一般公開」の意味を、ここで 1 文にしておきます。**以前の記事「[secretを安全に配る ── .env を1コマンドで配る dotvault を作りました](https://note.com/chooser/n/nd28081283465)」で紹介した dotvault を、誰でも自分でアカウントを作り、無料で使える形にすることです。** 以前の記事の時点では、アカウントは私たちが作って渡す形でした。11 月に、誰でも自分でアカウントを作れる形で公開します。

## なぜ作ったか

私は長いあいだ、secret を AWS の Parameter Store（AWS の設定値の保管庫）に置き、値を変えるたびに `aws ssm put-parameter` を打っていました。新しいメンバーの `.env` は、Slack の DM で渡していました。配ることができないのに、配らなければいけない。この矛盾は、以前の記事に書いたとおりです。

既製の secret 管理サービスも見ました。Doppler と Infisical を比べましたが、欲しいものが 3 つ同時にそろいませんでした。組織の下にチームを置けること。無料でも監査の記録が付くこと。値の版（バージョン）に上限が無いこと。この 3 つです。それで、自分たちの組織の形に合わせて作りました。

保管・権限・監査の 3 つを 1 か所で管理する考え方は、以前の記事に 4 枚の画面と一緒に書いています。短くまとめると、secret の保管場所をコードのリポジトリから分けてサーバー側に置き、環境（development / staging / production）ごとに分け、人と機械のそれぞれに「どの project まで」という権限を付け、人の招待や権限の付与・取り消しといった操作を Audit Log に残す、という形です。この連載では、その考え方の説明を繰り返しません。代わりに、実際の画面と手順で、1 回に 1 つずつ押せる操作を増やしていきます。

## 受け取る側がやること

利用者の言葉で、dotvault の goal を 3 つに言い直します。

- **受け取る側は、ログインした後は pull の 1 コマンドだけ。** 新しいマシンで `dotvault login` を 1 回打ち、project のフォルダで `dotvault pull` を打つと、自分に許された secret だけが `.env` に書き出されます。`.env` のファイルを作りたくない時は、`dotvault run -- npm run dev` のように、起動するコマンドの前に付けます。
- **CI と AI エージェントは、人のパスワード無しで認証する。** CI には、役割・project・期限で絞った service token を 1 つ渡します。AI エージェントには、project を作る token と secret を読む token の 2 本を渡します。この 2 本で project の作成から pull までを任せる手順は 8 回目で書きます。
- **平文の secret を、必要な場所の外に残さない。** ログインの token は OS の keychain に入ります。`dotvault run` は `.env` を書かずに、起動した子プロセスの環境変数にだけ渡します。commit の前に、secret が混ざっていないかを `dotvault scan` で見つけます。

CLI の全体は、`dotvault --help` の 1 画面に収まります。この連載で押すコマンドは、ほぼすべてこの中にあります。

![`dotvault --help` の全文（CLI 0.15.7・2026 年 10 月 17 日）。init・login・pull・run・set・describe・delete・import・versions・rollback・list・global・push・files・scan・project・logout・whoami の各コマンドの 1 行説明に続き、Examples に login・pull・run・push・scan の例、Common workflows に初回設定・ローカル開発・CI/CD からの pull・履歴の確認・値の戻し・token の発行・組織共通の key の共有の 7 つの流れが並ぶ。画面は実際の出力を文字どおりに再構成したもので、内容は実出力と同じ。](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/dvlt-001/1-2-dotvault-help.png)

Common workflows の 7 行は、そのままこの連載の並びです。First-time setup と Local dev が 2 回目、Roll back a secret が 4 回目、Share a key org-wide が 6 回目、CI/CD pull と Mint a CI token が 7 回目、Audit history が 10 回目に当たります。

## 用語を 1 枚で

管理コンソールのサイドバーに、この連載で使う用語がそろっています。自分だけで使う Personal の組織でログインした時の画面です。

![Personal の組織でログインした時の管理コンソールのサイドバー（英語 UI・2026 年 10 月 17 日）に、各項目の意味を注釈で添えた図。上から Dashboard（最初の画面）、Members（組織にいる人と役割）、Projects（project → 環境 → file と key）、Global Keys（組織で共通に使う key）、Service Tokens（CI や AI エージェント用の token）、Audit Log（誰がいつ何を変えたかの記録）、Help、User Settings（表示言語と自分のアカウント）の 8 項目。見出しの PERSONAL は組織の種類で、Company の組織では Workspace になり Teams と My Organizations が増える。](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/dvlt-001/1-4-sidebar-terms-ja.png)

用語は、上から下に含まれる関係になっています。

| 用語 | 意味 | この連載で扱う回 |
|---|---|---|
| Organization（組織） | いちばん外側の入れ物。**Personal** は自分だけで、権限の設定がありません。**Company** は人を招いてチームで使う組織です。Personal から Company への切り替え方は 9 回目で書きます | 2 回目・9 回目 |
| Project | アプリやリポジトリの単位。`.env` はこの下に置きます | 3 回目 |
| Environment（環境） | project の中の development / staging / production の 3 つと、どの環境からも参照する **Common**（CLI では `_shared` という名前で表示）。自分で名前を付けた環境も足せます | 3 回目 |
| Key | `KEY=VALUE` の 1 行。値を書くたびに版が残り、前の版に戻せます | 4 回目 |
| File | `.npmrc` や kubeconfig のように、ファイルごと配るもの | 5 回目 |
| Global Keys | 組織で共通に使う key の置き場。project ごとに明示して結び付けます | 6 回目 |
| Service token | CI や AI エージェントに渡す token。役割・project・期限で範囲を絞ります | 7 回目・8 回目 |
| 役割（project の権限） | **owner / admin / writer / reader** の 4 つ。付け方は 9 回目で画面と一緒に書きます | 9 回目 |
| 組織の役割 | **org_admin / member / guest** の 3 つ。それぞれで見える範囲は 9 回目で画面と一緒に書きます | 9 回目 |
| Audit Log | 人の招待や権限の付与・取り消しなど、組織の操作の記録。何が記録されるかは 10 回目で画面と一緒に書きます | 10 回目 |

Personal の組織では、サイドバーに Teams と My Organizations が出ません。Company の組織に切り替えると、見出しが Workspace になり、この 2 つが増えます。9 回目で、その画面を撮ります。

## この連載で何ができるようになるか

2 回目から 12 回目までを、困りごとと、読み終えた時にできるようになることで並べます。各回で、押せる操作が 1 つ増えます。

| 回 | 困りごと | 読み終えた時にできること |
|---|---|---|
| 2 | ローカルで直した値が CI やクラウドの開発環境に反映されていない | どの環境でも `dotvault pull` の 1 回で同じ `.env` をそろえられる。install・login・init は、その場所で最初の 1 回だけ。付録に、パスワードの再設定・表示言語・logout |
| 3 | development と production の `.env` を手で持ち分けるのをやめたい | project を作り、3 つの環境と Common を使い分け、環境どうしの差分を見て値を写せる。使い終わった project を archive して delete できる |
| 4 | 値を間違えて上書きしてしまった | key を画面と CLI の両方から書き、取り出し、版の一覧から前の版に戻せる |
| 5 | `.npmrc` と kubeconfig も人から人へ渡している | ファイルごと push して配り、commit の前に scan で漏れを止められる |
| 6 | 同じ SENTRY_DSN を 8 個の project に貼っている | 組織共通の key を Global Keys に 1 回だけ置き、project ごとに結び付けて配れる |
| 7 | CI に人のパスワードを置きたくない | service token を発行し、GitHub Actions から production の値を取り出せる。漏れたら画面で止められる |
| 8 | Claude Code に `.env` を貼り付けて渡している | AI エージェントに token を 2 本渡し、project の作成から import・pull まで人が付かずに回せる |
| 9 | 退職者の `.env` をどう無効にするか分からない | Personal から Company に切り替え、メールで招き、4 つの役割と環境の範囲を付け、Team の単位でまとめて付け、抜けた人を 1 分で止められる |
| 10 | 誰が何を変えたか、後から追えない | Audit Log を読み、Team の変更の履歴を追い、自分の利用量と無料で使える範囲を確かめられる |
| 11 | 漏れた key を 1 コマンドで止めたい | 漏れた key を即時に失効させ、版の状態を読めるようになる |
| 12 | 新しいメンバーが初日 5 分で pull できるまで | 新しいアカウントで通しの手順を再現し、自分のチームにそのまま配れる手順書を持てる。この回が一般公開の日 |

読み終えた時の状態を、もう少しだけ具体的に言います。自分の project の `.env` を dotvault に置き、別のマシンでも 1 コマンドで取り出せる。CI に人のパスワードを置かない。AI エージェントに secret を貼り付けない。チームに人を入れ、見せる範囲を決め、抜けた人をすぐに止める。使い終わった key と project を安全に片付ける。この 5 つを、自分の手で再現できる状態です。

## 連載が終わる時に、無料で公開する

dotvault は 11 月に、誰でもアカウントを作って使える形で公開します。無料で使える範囲は次のとおりです。

- **Personal（自分だけで使う）。** project は無制限、環境は 1 つの project に 10 まで、key は無制限です。
- **Company の無料プラン（チームで使う）。** 10 人まで、25 project まで、環境は 1 つの project に 10 まで、key は無制限、監査の記録は 14 日間です。

無料で公開する理由は、単純です。secret を手で渡す現場を減らしたいからです。そして、使ってみた方に、自社のほかの製品も知ってもらいたいからです。

## 次回

2 回目は「ローカルで直した値が CI に反映されていない ── どの環境でも dotvault pull 1 回で同じ .env をそろえる」です。ローカル・CI/CD・サーバー・クラウドの開発環境で同じ値をそろえる考え方と、インストールから最初の pull までを、画面を 1 枚ずつ付けて進めます。

---

dotvault は、.env や設定ファイルの secret をサーバー側に置き、権限のある人・CI・AI エージェントが 1 コマンドで受け取るサービスです（https://dotvault.io）。この連載が終わる時に、dotvault を誰でも無料で使える形で公開する予定です。公開したら、この連載の手順どおりに使い始められます。

興味のある方は、ぜひ「いいね」と記事の購読をお願いいたします。

自社プロダクトは https://www.eln.ne.jp/products にまとめています。

## 筆者について

上原正吉。EarthLink Network Co., Ltd. でAI開発をしています。2025年からClaude Codeを開発の主体に据え、今は20を超えるプロダクトを1人で同時に開発・運用しています。この連載では、その現場で実際に起きたこと（うまくいったことも、失敗も）を、数字と一緒に書いていきます。

また、AIで業務や開発を組み替えたい会社・チーム向けに、AI活用のコンサルティングも受け付けています。ご相談は [www.eln.ne.jp](https://www.eln.ne.jp) からどうぞ。

---

**EarthLink Network** は、会社の全業務を AI で回すために、必要になったものを自社で作っています。いま作っているプロダクトの一覧と概要は、こちらにまとめています。

→ [EarthLink Network が自社でつくっている18のプロダクト](https://zenn.dev/chooser/articles/in-house-products)

会社と各プロダクトの詳細は、公式サイト [www.eln.ne.jp](https://www.eln.ne.jp) をご覧ください。
