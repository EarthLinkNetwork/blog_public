---
title: "Claude Code の cloud session に最大 $250 のクレジットが付きます ── 申請期限は日本時間 10月8日 15:59"
emoji: "📝"
type: "tech"
topics: ["claudecode", "ai", "anthropic", "github"]
published: true
---

<!-- このファイルは gen-publish.mjs が生成した発行用スケルトン。翻訳(海外媒体)は Claude が en.md 経由で行う。 -->
<!-- gen-publish:src-sha256=060c2c9d57a623951fa7de8789c1614f1ebe61aa4dea9e77a04f8fb77fbec021 -->

上原正吉（EarthLink Network Co., Ltd.）。Claude Codeを開発の主体に据え、20を超えるプロダクトを1人で同時に開発・運用しています。これは、その現場の実測記です。

<!-- TODO(Claude): この zenn 版は媒体トーン定義（/docs/platform-tone）に合わせてトーン・長さを調整する。方向性: 技術・実装重視。設計判断と数字を厚く。開発者向け。 以下は基となる本文。調整後にこのコメントを消す。 -->

# Claude Code の cloud session に最大 $250 のクレジットが付きます ── 申請期限は日本時間 10月8日 15:59

![Claude Code の cloud session に最大 $250 のクレジットが付きます ── 申請期限は日本時間 10月8日 15:59](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-001/hero-ogp.png)

## 結論

Claude Code を Pro か Max で契約している方は、**cloud session 専用のクレジットを無料で受け取れます**。Max は $250、Pro は $100 で、一回限りです。

- **申請期限**: 日本時間 2026年10月8日 15:59（米国太平洋時間 10月7日 23:59）
- **使える期限**: 日本時間 2026年11月5日 16:59（米国太平洋時間 11月4日 23:59）。使い残しは消えます
- **対象**: キャンペーンが始まった 2026年9月23日 14:00（米国太平洋時間。日本時間では9月24日 6:00）の時点で、Pro / Max を契約していた個人。Team / Enterprise、無料トライアル、支払いが滞っているアカウントは対象外です
- **使える範囲**: cloud session だけです。Anthropic のヘルプページには、対象外として「Projects と Routines」「Remote Control のセッション」「チャットと Cowork のセッション」「cloud session 以外の Claude の利用すべて」が挙がっています。手元のパソコンで動かす普段の Claude Code にも使えません
- **使うための準備**: GitHub の連携が必要です

申請は claude.ai の設定画面（左下のユーザー名 → Settings → Usage）に出ている「Claim credit」から、数分で終わります。ヘルプページには「自動で付与されるアカウントもある」とありますが、付いていなければ申請しない限り受け取れません。まず Usage 画面を確かめて、申請だけ済ませておくのがおすすめです。

この記事は、**期限が切れる前にクレジットを受け取ってもらうこと**を目的に、先に書き上げました。そのため、受け取り方を中心にまとめています。cloud session の詳しい使い方は、別の記事で改めて紹介します。

この後の記事では、cloud session にどういう仕事をさせると、どれくらいのクレジットが使われるのか、クレジット使用量の実測値を載せていくつもりです。どういう仕事をするとどうなるのか、具体的な使い方やコストもまとめます。

続きを見逃さないように、フォローや記事のお気に入り登録をしておくと、更新を受け取りやすいと思います。

## 本文（読了 約6分）

### cloud session とは何か

cloud session は、Claude Code を自分のパソコンではなく、Anthropic が管理するクラウド上の仮想マシンで動かす仕組みです。

いつもの Claude Code は、手元のパソコンの中でファイルを読み書きします。cloud session では、GitHub のリポジトリをクラウド側に複製（clone）して、専用のブランチで作業し、終わったらプルリクエスト（変更の取り込み依頼）として返してきます。

便利なのは、**パソコンを閉じても作業が止まらない**ことです。長いリファクタリングを頼んで、そのまま移動したり寝たりしても、戻ってきたらプルリクエストができています。進み具合はブラウザやスマートフォンの Claude アプリからも見られます。

2026年9月23日、この cloud session が試験提供（research preview）から正式提供になりました。同じ日に、今回のクレジットのキャンペーンも始まりました。

### 私の Max (20x) アカウントにも出ていました

私は Claude Code を Max (20x) で使っています。claude.ai の設定にある Usage 画面を開くと、上部に「$250 のボーナスクレジットを受け取れる」というバナーが出ていました。

![Usage 画面の上部に出ている cloud session 用 $250 クレジットのバナーと「Claim credit」ボタン（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-001/usage-claim-banner.png)

同じ画面には、今週の利用上限に対して「このペースだと、次のリセット前に今夜使い切る」という警告も出ていました。Max でも、重く使うと週の上限に当たります。

ここで効いてくるのが今回のクレジットです。バナーの文言は「on top of your plan limits」、つまり**プランの利用上限とは別に上乗せされる枠**です。cloud session を動かしている間は、まずこのクレジットから使われます。使い切るか期限が来ると、通常のプランの枠に戻ります。ただし Pro プランで Fable を使う場合は例外で、別途の従量課金クレジット（usage credits）を有効にして残高を入れておく必要があります。

### 申請の手順

申請の入口は次の4つです。デスクトップアプリ・IDE、ターミナル、申請用のリンクは Anthropic のヘルプページに載っているもので、設定画面のバナーは私の画面で確かめたものです。

- **claude.ai の設定画面**: Usage 画面に出ているバナー
- **デスクトップアプリや IDE**: 画面に出るバナー
- **ターミナルの Claude Code**: `/claim-credit` コマンド
- **申請用のリンク**: 告知に載っているリンク

ここでは、claude.ai の画面から申請する流れを順に説明します。

まず claude.ai を開き、**左下のユーザー名をクリック**します。メニューが開くので、**Settings** を選びます。

![claude.ai の左下にある自分の名前をクリックすると、Settings や Usage を含むメニューが開く（実画面・メールアドレスと名前は伏せています）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-001/user-menu.png)

設定画面が開いたら、左側のメニューから **Usage** を選びます（メニューにある **Usage** を直接押しても、同じ画面に入れます）。今のセッションと週ごとの使用率が並ぶ画面です。

![Settings の左メニューで Usage を選ぶと、今のセッションと週ごとの使用率が表示される（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-001/settings-menu.png)

まだ申請していなければ、この Usage 画面の一番上に、先ほどの「Claim a $250 bonus credit for cloud sessions」のバナーが出ています（上の画像は私が申請を済ませた後のものなので、バナーは消えています）。ここの **Claim credit** を押します。

claude.ai/code（Claude Code の Web 版）を開くだけでは、通常の Claude Code の画面が開くだけで、申請の画面が出ないことがあります。迷ったら、設定画面の Usage から入るのが確実です。

Claim credit を押すと、次の画面が出ます。

![「Claim $250 in credits for cloud sessions」の画面。申請、GitHub 連携とリポジトリ選択、cloud session の開始、の3段が並ぶ（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-001/claim-modal.png)

1. **クレジットを申請する**: 「Claim credit」を押します。Promotional Credit Offer Terms（販促クレジットの利用条件）への同意が必要です
2. **GitHub を連携して、リポジトリを選ぶ**: GitHub にサインインし、Claude GitHub App（Claude がリポジトリを読み書きするための GitHub アプリ）に、どのリポジトリを触らせるかを決めます
3. **cloud session を始める**: 最初の作業を頼むと、クレジットが自動で使われ始めます

すでに GitHub を連携している人は、ここで手順2と3がすぐ終わります。

まだ GitHub を連携していない人は、次の流れになります。

まず、Claude Code の Web 版の初回案内（オンボーディング）が開きます。上部に「Your $250 credit for cloud sessions is waiting」と出ているのを確かめて、**Continue with GitHub** を押します。

![Claude Code のオンボーディング画面。$250 のクレジットが待っているという案内と「Continue with GitHub」ボタンが出る（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-001/onboarding.png)

ターミナルで `gh` コマンド（GitHub の公式 CLI）を使っている人は、同じ画面の「Connect from your terminal」から、ターミナルで `/web-setup` を実行して連携する方法も選べます。

Continue with GitHub を押すと、GitHub のサインイン画面が開きます。いつもの GitHub アカウントでログインします。

![GitHub のサインイン画面。「Sign in to GitHub to continue to Claude」と表示される（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-001/github-signin.png)

ログインすると、Claude GitHub App に、どのリポジトリへのアクセスを許可するかを選ぶ画面になります。全部のリポジトリを許可せず、cloud session で触らせたいリポジトリだけを選ぶのが安全です。

選び終わると「Connected to GitHub」と表示されます。私は GitHub アカウントを2つ連携したので「Linked 2 GitHub accounts」と出ています（アカウント名と組織名は伏せています）。**Continue** を押して次へ進みます。

![GitHub の連携が終わった画面。「Connected to GitHub」と表示され、Continue ボタンで次へ進む（実画面・アカウント名は伏せています）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-001/github-connected.png)

連携が終わると、次の画面になります。

![GitHub 連携が終わり、$250 のクレジットが使える状態になった画面。「Start a cloud session」ボタンが出ている（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-001/credits-available-modal.png)

Usage 画面には、cloud session 用のクレジットが通常の利用枠とは別の行で表示されます。

![Usage 画面の「Cloud session credits」。$250 のうち $250 が残っていて、期限も表示される（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-001/usage-cloud-credits.png)

申請の画面には期限が「3:59 PM GMT+9, October 8」と、日本時間で表示されていました。各社の報道では「10月7日まで」と書かれていますが、これは米国太平洋時間の10月7日 23:59 のことです。日本では**10月8日の午後3時59分**が締め切りになります。

使える期限も同じで、報道では11月4日説と11月5日説がありますが、画面の表示は「4:59 PM GMT+9, November 5」でした。11月1日に米国が冬時間に戻るため、米国太平洋時間の11月4日 23:59 が、日本時間では11月5日 16:59 になります。食い違いの正体は、時差の書き方の違いです。

### 何に使えて、何に使えないか

使えるのは cloud session だけです。始める場所はいくつかあります。

- **ブラウザ**: claude.ai/code
- **スマートフォン**: Claude アプリの Code タブ
- **デスクトップアプリ**: セッション開始時に Local ではなく Cloud を選ぶ
- **ターミナル**: `claude --cloud` コマンド

ターミナルから頼むと、こうなります。公式ドキュメントにある例です。

```bash
claude --cloud "Fix the authentication bug in src/auth/login.ts"
```

注意点が1つあります。`--cloud` が複製するのは、手元のフォルダではなく **GitHub 上の今のブランチ**です。手元でコミットしただけでまだ push していない変更は、クラウド側には届きません。先に push してから頼みます（リポジトリに Claude GitHub App が入っていない場合などは、例外的に手元のリポジトリをまとめて送る動きになります）。

逆に、クラウドで進んだ作業を手元に引き取ることもできます。

```bash
claude --teleport
```

セッションの一覧が出て、選ぶとそのブランチと会話の履歴が手元のターミナルに移ります。手元の作業中のセッションをクラウドへ送る方向は、ターミナルからはできません（デスクトップアプリには「Continue in」というメニューがあります）。

使えないものもはっきりしています。

- **手元の Claude Code、チャット、Cowork、Remote Control**: 今回のクレジットの対象外です。ヘルプページには「cloud session 以外の Claude の利用すべて」が対象外と書かれています
- **Projects と Routines**: 画面にも「Not eligible for Projects and Routines」と書かれていました。定期実行の Routines は cloud session として動きますが、今回のクレジットは使えません
- **Team / Enterprise プランと、キャンペーン開始（米国太平洋時間 9月23日 14:00）より後の新規契約**: ヘルプページでも対象外です。画面の表示も「Existing Pro and Max subscribers only」でした
- **Amazon Bedrock などの他社経由の構成**: cloud session そのものが使えません

### 頼む前に知っておきたい、クラウド側の環境

クレジットを無駄にしやすいのは、作業の中身ではなく、クラウド側の環境が手元と違うことに気づかず、準備のやり直しで時間を使ってしまう場面です。公式ドキュメントを読むと、手元との違いはかなりはっきり書かれています。

まず、クラウドの仮想マシンは毎回まっさらな Ubuntu 24.04（x86_64）です。手元が Mac でも Windows でも関係ありません。よく使う言語とツールは最初から入っています。

- **Python**: Python 3 と pip、poetry、uv、pytest、ruff など
- **Node.js**: 20・21・22（既定は 22）と npm、yarn、pnpm
- **その他の言語**: Ruby、PHP、Java、Go、Rust、C/C++
- **Docker とデータベース**: docker と docker compose、PostgreSQL 16、Redis 7.0
- **便利なツール**: git、gh、jq、ripgrep など

次に、**手元にだけある設定はクラウドに持ち込まれません**。ここが一番つまずきやすいところです。

- **持ち込まれるもの**: リポジトリにコミットされているもの。リポジトリの `CLAUDE.md`、`.claude/` 配下のスキル・エージェント・コマンド・ルール、`.mcp.json` など
- **持ち込まれないもの**: 自分のパソコンのホームにある `~/.claude/CLAUDE.md` や個人のスキル、ユーザー設定で有効にしたプラグイン、`claude mcp add` で手元だけに追加した MCP サーバー（Claude から外部サービスを使うための接続）
- **使えないもの**: AWS SSO のような、ブラウザでログインする認証

私のように、ホーム側の `CLAUDE.md` に運用ルールをたくさん書いている人は要注意です。クラウドで同じ振る舞いをさせたいなら、そのルールをリポジトリ側にコミットしておく必要があります。

足りないツールは、環境の設定画面にある「Setup script」に書いておくと、セッションの開始時に自動で入ります。公式の例はこうです。

```bash
#!/bin/bash
apt update && apt install -y shellcheck
```

このスクリプトが約5分以内に終わると、入れた状態が保存されて、次からのセッションはその状態から始まります。毎回インストールを待たなくて済みます。

ネットワークの初期設定は「Trusted」で、npm や PyPI などのパッケージ置き場、GitHub など、公式の許可リストにあるドメインには出られますが、それ以外のサイトには出られません。社内の API などに出たい場合は、環境の設定で許可するドメインを追加します。

### どう使うと得か

$250 で何時間ぶん動くのかは、私が確認した公式の資料からは分かりませんでした。私もまだ消費の具合を測れていないので、ここは断言できません。

ただ、性質から考えて向いている使い方ははっきりしています。

- **長い作業を任せて手を離す**: 依存ライブラリの更新とテスト、大きめのリファクタリング、移行作業など。パソコンを閉じても進みます
- **並列で試す**: `claude --cloud` を3回打てば、3つのセッションが別々に同時に動きます。手元のパソコンは1台のままです
- **週の上限に近いときの逃がし先**: 先ほどの警告のように、手元の利用が上限に近いときこそ、別枠のクレジットで動く cloud session に回す価値があります
- **プルリクエストの自動修正（Auto-fix）**: CI の失敗やレビューコメントに、Claude が自動で直して push する機能です。リポジトリに Claude GitHub App が入っている必要があります

公式ドキュメントには、効率のよい進め方として「手元で計画して、クラウドで実行する」という流れも紹介されています。手元の Claude Code を計画モード（ファイルを読んで計画を立てるだけで、コードは書き換えないモード）で起動し、方針を詰めます。

```bash
claude --permission-mode plan
```

決まった計画をファイルに保存してコミットし、push してから、クラウドに実行だけを頼みます。

```bash
claude --cloud "Execute the migration plan in docs/migration-plan.md"
```

計画づくりは、人が判断しながら手元で進める。時間のかかる実行は、手を離してクラウドに任せる。役割を分けると、クレジットを「待ち時間の長い作業」に集中して使えます。

走っている途中で指示を足したいときは、別のパソコンからでも、ターミナルから一言送れます。

```bash
claude -p "テストが通ったら、変更点を README にも追記して" --cloud <セッションID>
```

送ったら返事を待たずに終わります。セッションIDは claude.ai/code の一覧で確認できます。

期限は11月5日です。1か月ちょっとしかないので、申請したら早めに1本、普段なら手元で半日かかる作業を頼んでみるのがよいと思います。

### 注意しておくこと

- **付いていなければ申請が必要**: 自動で付与されるアカウントもありますが、付いていなければ申請しない限り受け取れません。10月8日 15:59 を過ぎると申請できなくなります
- **使い残しは消える**: 11月5日 16:59 で失効し、繰り越しはありません
- **GitHub が前提**: リポジトリの複製とプルリクエストの作成は GitHub が必要です。GitLab などは、手元のリポジトリをまとめて送る方法（100MB 未満）はありますが、結果を push で戻せません
- **放置すると仮想マシンが回収される**: 一定時間なにもしないと、クラウド側の仮想マシンは止まります。会話の履歴は残り、開き直せば続けられますが、途中で動いていた処理は戻りません
- **GitHub の権限は絞る**: Claude GitHub App には、触らせたいリポジトリだけを選ぶのが安全です。自分が所有していないリポジトリでは、自動コードレビューなど一部の機能が使えないという注記も出ていました
- **セッションを公開するときは中身を確認する**: Pro / Max のセッション共有は「Private」か「Public」の2択で、Public にすると claude.ai にログインしている誰でも見られます。非公開リポジトリのコードが会話に残っていることがあるので、共有の前に中身を確かめます
- **環境変数にパスワードや API キーを入れない**: 環境に設定した変数とセットアップスクリプトは、その環境を使う人なら読めます。Pro / Max では、外部サービスの API キーは「API credentials」として登録すると、セッションの中からは見えない形で使われます
- **Auto-fix はコメントで動く仕組みと組み合わせない**: Auto-fix は、あなたの GitHub アカウント名でレビューコメントに返信することがあります。プルリクエストへのコメントでデプロイなどが動く仕組みを入れているリポジトリでは、意図せず動かしてしまうおそれがあると公式ドキュメントに注意書きがあります

### まとめ

- Claude Code の Pro / Max 契約者は、cloud session 専用のクレジット（Max $250・Pro $100）を一回限りで受け取れます
- 申請期限は**日本時間 10月8日 15:59**、使える期限は**日本時間 11月5日 16:59**です。報道の「10月7日」「11月4日」は米国太平洋時間です
- プランの利用上限とは別の枠なので、週の上限に近いときや、長い作業・並列作業を任せたいときに効きます
- まず申請だけ済ませて、期限までに一度、手を離して任せられる作業を頼んでみてください

この後の記事では、cloud session にどういう仕事をさせると、どれくらいのクレジットが使われるのか、クレジット使用量の実測値を載せていくつもりです。どういう仕事をするとどうなるのか、具体的な使い方やコストもまとめます。

続きを見逃さないように、フォローや記事のお気に入り登録をしておくと、更新を受け取りやすいと思います。

参考: Anthropic ヘルプページ「Cloud sessions bonus credit promotion」（support.claude.com/en/articles/17152539）、Claude Code 公式ドキュメント「Use Claude Code in the cloud」（code.claude.com/docs/en/claude-code-on-the-web）、AI Tools Review「Claude Code $250 Credit: Cloud Sessions Guide (October 2026)」（aitoolsreview.co.uk）

---

このシリーズでは、AI 開発ツールのクレジット配布・割引・無料枠など、受け取らないと損をする情報を、期限と手順つきで順次お届けします。
見逃したくない方は、ぜひ「いいね」と記事の購読をお願いいたします。

自社プロダクトは https://www.eln.ne.jp/products にまとめています。

## 筆者について

上原正吉。EarthLink Network Co., Ltd. でAI開発をしています。2025年からClaude Codeを開発の主体に据え、今は20を超えるプロダクトを1人で同時に開発・運用しています。この連載では、その現場で実際に起きたこと（うまくいったことも、失敗も）を、数字と一緒に書いていきます。

また、AIで業務や開発を組み替えたい会社・チーム向けに、AI活用のコンサルティングも受け付けています。ご相談は [www.eln.ne.jp](https://www.eln.ne.jp) からどうぞ。

---

**EarthLink Network** は、会社の全業務を AI で回すために、必要になったものを自社で作っています。いま作っているプロダクトの一覧と概要は、こちらにまとめています。

→ [EarthLink Network が自社でつくっている18のプロダクト](https://zenn.dev/chooser/articles/in-house-products)

会社と各プロダクトの詳細は、公式サイト [www.eln.ne.jp](https://www.eln.ne.jp) をご覧ください。

