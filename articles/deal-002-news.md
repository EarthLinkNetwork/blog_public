---
title: "Claude Max / Team プランで、API クレジットが毎月配布されるようになりました ── Max 20x は毎月 $200"
emoji: "📝"
type: "tech"
topics: ["claude", "ai", "anthropic", "api"]
published: true
---

<!-- このファイルは gen-publish.mjs が生成した発行用スケルトン。翻訳(海外媒体)は Claude が en.md 経由で行う。 -->
<!-- gen-publish:src-sha256=868ee2b2a82c33939ced75537b9bff3a1e86f753cdd396fba1e66aa099addf78 -->

上原正吉（EarthLink Network Co., Ltd.）。Claude Codeを開発の主体に据え、20を超えるプロダクトを1人で同時に開発・運用しています。これは、その現場の実測記です。

<!-- TODO(Claude): この zenn 版は媒体トーン定義（/docs/platform-tone）に合わせてトーン・長さを調整する。方向性: 技術・実装重視。設計判断と数字を厚く。開発者向け。 以下は基となる本文。調整後にこのコメントを消す。 -->

# Claude Max / Team プランで、API クレジットが毎月配布されるようになりました ── Max 20x は毎月 $200

![Claude Max / Team プランで、API クレジットが毎月配布されるようになりました ── Max 20x は毎月 $200](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-002/hero-ogp.png)

## 結論

Claude を Max か Team で契約している方は、**毎月、API 用のクレジットを受け取れる**ようになりました。一回限りではなく、請求のたびに補充されます。

- **金額**: Max 5x は月 $100、Max 20x は月 $200。Team は Standard の席が1席あたり $20、Premium の席が1席あたり $100 で、組織全体で月 $500 が上限です。Free、Pro、Enterprise は対象に入っていません
- **条件**: Max / Team の契約が有効で、そのプランを7日以上使っていること。支払い方法の登録は要りません
- **使える範囲**: Claude API（Messages API と Batches API）、Console の Playground、Managed Agents、Agent SDK
- **使えない範囲**: Claude Code を対話で使う分（ターミナル、IDE、デスクトップアプリ、Web のどれでも）、プランの追加使用量（Extra usage）、Amazon Bedrock、Google Cloud の Vertex AI、Microsoft Foundry
- **受け取り方**: Max は claude.ai の設定 → 請求 →「API クレジット」から、Claude Console の組織を1つリンクします。会社でなくても受け取れます。Console を「個人」で始めた場合も、その Console をリンクします。**リンクしない限り付きません**。**リンクした組織は自分では変えられない**ので、選ぶ組織は先に決めておきます
- **繰り越し**: ありません。使い残しは翌月に持ち越せません

私の Max (20x) アカウントでも、リンクして $200 を受け取りました。カードの登録は要りませんでした。

繰り越しがないので、受け取るのが遅れた月の分は、そのまま使われずに終わります。どの組織で受け取るかを決めたら、早めにリンクしておくのがおすすめです。

## 本文（読了 約6分）

### 何が始まったのか

Anthropic が、Max と Team のプランに毎月の API クレジットを付けました。金額と条件は、Anthropic のヘルプページ「Monthly API credits for Max and Team plans」にまとまっています。

これまで、claude.ai のチャットや Claude Code を使うための月額プランと、Claude API の従量課金は、まったく別の契約でした。Max を契約していても、自分で書いたプログラムから Claude API を呼べば、その分は Claude Console で別に支払う必要がありました。

今回のクレジットは、その **API 側の支払いに、月額プランから毎月お金が回ってくる**仕組みです。プランの利用上限とは別なので、チャットや Claude Code で使える量は減りません。

### 私の Max (20x) アカウントにも出ていました

claude.ai のホーム画面を開くと、右下に「Free API credits」という通知が出ていました。「Your plan now includes monthly API credits.」（ご利用のプランに毎月の API クレジットが含まれるようになりました）と書かれ、**Claim credits** のボタンが付いています。

![claude.ai のホーム画面の右下に出た「Free API credits」の通知。「Your plan now includes monthly API credits.」の文と「Claim credits」ボタンがある（実画面・英語表示・会話の履歴と名前はぼかしています）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-002/home-claim-card.png)

私はこのボタンからではなく、設定の請求画面から進めました。claude.ai の左下のユーザー名から「設定」を開き、左のメニューで「請求」を選ぶと、プランの下に「API クレジット」という欄が増えていました。

![claude.ai の設定 → 請求に増えた「API クレジット」の欄。「Max 20x プランには、毎月 $200 USD の API クレジットが含まれています」と「組織を作成」ボタンが出ている（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-002/billing-api-credits.png)

「Max 20x プランには、毎月 $200 USD の API クレジットが含まれています」とあり、その下に「まだどの API 組織にも所属していません」と出ています。私はそれまで Claude Console を使っていなかったので、組織がありません。この場合は、右の **組織を作成** から始めます。

### 受け取りの手順

「組織を作成」を押すと、別のタブで Claude Console（API を使うための管理画面）が開きます。Console には独自の言語設定がなく、claude.ai の表示言語に合わせて日本語で表示されました。

最初に、個人で使うか、組織で使うかを選びます。

![Claude Console の「Claude API をどのように使用しますか？」の画面。「個人」と「組織」の2つから選ぶ（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-002/console-account-type.png)

私は会社で使うので **組織** を選びました。組織を選ぶと「個人でご利用の場合は、個人アカウントから始めてください。個人アカウントはいつでも組織に変換できます」という案内が出ます。迷ったら個人から始めても、後から組織に変えられます。

次に、組織名、事業体の種類、所在地を入れます。

![「組織についてお聞かせください」の画面。組織名、事業体の種類（中小企業）、所在地（Japan）を入れて「続ける」を押す（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-002/org-form.png)

1つ気をつける点があります。画面は日本語でも、**所在地の一覧は英語**です。「日本」と打っても出てこないので、「Japan」で探します。

続いて、API の使い道を聞かれます。

![「Claude API をどのように使用しますか？」の画面。日本以外の国でも使うか、社内向けか社外向けか、どんな目的で使うかを答える（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-002/usage-survey.png)

- **日本以外の国でも使うか**: 「はい」を選ぶと、国を選ぶ欄が出ます
- **社内向けか、社外の顧客向けか、両方か**
- **どんな目的で使うか**: 自由に書く欄です。下の候補ボタン（コンサルティング、コピーライティング、教育など）を押すと、日本語の画面でも英語の言葉が入ります

最後に、確認の質問が2つ出ます。

![「確認が必要な項目がいくつかあります」の画面。法律・医療・財務の助言に使うか、18歳未満向けの製品で使うかを「はい」「いいえ」で答え、「アカウントを作成」を押す（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-002/policy-check.png)

消費者に法律・医療・財務の助言をするために使うか、18歳未満の利用者向けの製品やサービスで使うかを答えて、**アカウントを作成** を押します。

組織ができると、すぐにクレジットを受け取る画面になります。

![「ご利用の Max プランには、毎月 $200 分の API クレジットが含まれています」の画面。「リンクして $200 分を受け取る」と「今はしない」のボタンが並ぶ（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-002/link-claim.png)

**リンクして $200 分を受け取る** を押します。下の小さな文字にあるとおり、組織をリンクしてクレジットを受け取ると、補足クレジット規約（Supplemental Credit Terms）が適用されます。

押すと、クレジットが付いたことを知らせる画面になります。

![「EarthLink Network に、ご利用の Max プランから毎月 $200 分のクレジットが付与されます」の画面。「今後の購入用にカードを追加」と「カードなしで続行」のボタンが並ぶ（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-002/credits-added.png)

ここでカードの登録を勧められますが、**登録しなくても使えます**。「本日の請求はありません」と書かれているとおり、**カードなしで続行** を押せば、お金はかからないまま $200 分を使えます。

なお、同じ画面を英語で表示すると「Credits cover the Claude API, Claude Managed Agents, the Claude Agent SDK, and the Playground」と、使える範囲が1文で書かれていました。日本語の画面には、この1文がありません。

### 受け取ったあとの画面

Console のダッシュボードには、組織のクレジットとして $200.00 が入っていました。

![Claude Console のダッシュボード。「組織のクレジット $200.00」「今月の使用額 $0.00 / ティア上限 $500・11月1日にリセット」と表示されている（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-002/console-dashboard.png)

「今月の使用額」の欄にある「ティア上限 $500」は、API の利用段階（ティア）についての表示で、今回のクレジットの額とは別の数字です。

ダッシュボードの左側のメニューには、Playground、ファイル、Skills、バッチ、Managed Agents（クイックスタート、エージェント、セッション、デプロイメント、環境など）が並んでいました。画面の中ほどには、使えるモデルとして Fable 5.1、Opus 5.5、Sonnet 5.5、Haiku 5.5 が出ています。今回のクレジットは、ここに並んでいる API の機能に使えます。

右上の **API キーを取得** から API キーを作れば、自分のプログラムから呼べる状態になります。キーを作るところから先は、この記事では扱いません。

claude.ai の請求画面に戻ると、「API クレジット」の欄が変わっていました。

![claude.ai の設定 → 請求の「API クレジット」の欄。「EarthLink Network 2026年10月9日にリンク済み」「月間クレジット $200」「次回のクレジット 2026年10月30日」と表示されている（実画面）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/deal-002/billing-linked.png)

リンクした組織名と「リンク済み」の表示、月間クレジット $200、次回のクレジットの日付が出ています。

ここで1つ気づいたことがあります。私のサブスクリプションの更新日は10月28日ですが、**次回のクレジットは10月30日**と表示されていました。クレジットが補充される日は、プランの更新日と同じとは限らないようです。月末に使い切る計画を立てるときは、この「次回のクレジット」の日付を見るのが確実です。

リンクしたあとは、claude.ai の設定のメニューに「API キー」の入口も増えていました。ここから Console の API キーの画面に移れます。

### 3種類のお金を分けて考える

今回のクレジットが増えたことで、Claude に払うお金は、少なくとも次の3つに分かれました。混同しやすいので、整理しておきます。

- **プランの利用上限**: claude.ai のチャットや、普段の Claude Code が使う分です。セッションごとや週ごとの上限があり、月額料金に含まれています
- **毎月の API クレジット（今回のもの）**: Console の組織に毎月入るクレジットです。Claude API、Playground、Managed Agents、Agent SDK に使われます。繰り越しはありません
- **買った API クレジット**: Console で自分で購入するクレジットです。毎月の API クレジットを使い切ったあとに、こちらが減ります

このシリーズの前回の記事で紹介した、cloud session 用の $250 のクレジットは、これとは別の、一回限りのものです。そちらは claude.ai の設定の「使用量」に出るもので、Claude Code をクラウドで動かす分だけに使われます。今回の毎月の API クレジットとは、受け取る場所も、使える先も違います。

### 何に使えて、何に使えないか

ヘルプページに書かれている範囲は次のとおりです。

- **使える**: Claude API（Messages API・Batches API）、Console の Playground、Managed Agents、Agent SDK
- **使えない**: Claude Code を対話で使う分（ターミナル、IDE、デスクトップアプリ、Web のどれでも）、Claude Code の GitHub Action から動かした分、プランの追加使用量（Extra usage）、Amazon Bedrock、Google Cloud の Vertex AI、Microsoft Foundry

Claude Code を普段どおり使う分は、これまでどおりプランの利用上限の中で使われます。今回のクレジットは、自分で書いたプログラムや Agent SDK で作ったエージェントから Claude API を呼ぶ分に使われる、と考えると分かりやすいです。

なお、ヘルプページのよくある質問によると、`claude -p` や Agent SDK を、リンクした組織の API キーで**自分で動かす**場合はクレジットの対象になります。ただし、Claude Code の GitHub Action、IDE の拡張機能、デスクトップアプリから動かした分は Claude Code の利用として扱われ、`-p` を付けても対象になりません。

ヘルプページには、ほかにも次のことが書かれています。

- **購入したクレジットより先に使われる**: すでに API のクレジットを買っている組織なら、毎月のクレジットから先に減ります
- **使い切ったとき**: 買ったクレジットも自動チャージ（auto-reload）もない組織では、次の付与まで API が止まります。プランの支払いに上乗せして請求されることはありません。続けて使いたい場合は、クレジットを買うか、自動チャージを設定します。営業経由の請求書払いの組織は、超えた分が通常どおり請求されます
- **リンクできる組織は1つ**: 1つのプランからリンクできる Console の組織は1つで、1つの組織が受け取れるのも1つのプランからだけです。**リンクした組織は自分では変えられず、変えるにはサポートへの連絡が必要**です。ヘルプページでも、開発に使う組織を選ぶよう書かれています
- **組織の全員で同じ残高を使う**: リンクした組織の API キーを持っている人は、全員が同じクレジットから使います。会社の組織をリンクする場合は、誰がキーを持っているかを確かめておきます
- **リンクするには組織での権限が要る**: Console 側では、その組織の Owner、Admin、Billing のどれかの役割が必要です
- **プランを変えたとき**: Max 5x から 20x に上げると、差額が日割りですぐ付きます。Free や Pro など対象外のプランに下げたり、解約や返金をしたりすると、次からの付与は止まります（Max 20x から 5x のように、対象のプランどうしで下げる場合は当たりません）。止まっても、すでに付いた分は期限まで使えます。Max から Team に移ると、リンクは外れます

### どう使うと得か

繰り越しがないので、**毎月、何かに使い切る**前提で考えるのが得です。私が確かめた範囲で、使い道として考えやすいものを挙げます。

- **自分で作ったツールの API 代**: そのツールがリンクした組織の API キーで動いていれば、API の利用料のうち毎月のクレジットの分（Max 20x なら $200）は、新たに支払わずに済みます
- **Batches API でまとめて処理する**: 急がない大量の処理を、まとめて流す使い方です
- **Agent SDK や Managed Agents を試す**: 気になっていたけれど、従量課金が怖くて手を出していなかった人には、試すきっかけになります
- **Playground でプロンプトを試す**: プログラムを書く前に、Console の Playground で返答を確かめながらプロンプトを調整できます

$200 でどれくらいの処理ができるかは、使うモデルと入力・出力の量で大きく変わります。私もまだ使い始めたところなので、ここは断言できません。使ってみて分かったことは、続きの記事で書きます。

### 注意しておくこと

- **リンクしないと付かない**: プランに含まれていても、組織をリンクするまでは受け取れません
- **使い残しは消える**: 繰り越しはありません。リンクが遅れた月の分も戻りません
- **Free、Pro、Enterprise は対象外**: ヘルプページで対象に挙がっているのは Max と Team です（割引のある Team も対象です）
- **リンクした組織は自分では変えられない**: 変えるにはサポートへの連絡が必要です。試しに作った組織をそのままリンクしないように、先に使う組織を決めておきます
- **7日の条件がある**: 対象のプランに切り替えてから7日以上たっていることが条件です
- **カードは登録しなくてよい**: 受け取るだけなら、カードは要りません。使い切ったあとの扱いは、カードの有無ではなく組織の支払い方法で決まります。買ったクレジットも自動チャージもない組織では、次の付与まで API が止まります。自動チャージを有効にしている組織や、営業経由の請求書払いの組織では、使い切ったあとの分が課金されます
- **補充の日付はプランの更新日と違うことがある**: 私の場合、更新日は10月28日、次回のクレジットは10月30日でした
- **Team は入口と操作できる人が違う**: ヘルプページによると、Team は組織の設定（Organization settings）→ 請求（Billing）から、Team の Primary Owner か Owner がリンクします。私が操作したのは Max の契約なので、Team の画面はこの記事では確かめていません

### まとめ

- Claude の Max / Team プランに、毎月の API クレジットが付くようになりました。Max 20x は月 $200、Max 5x は月 $100 です
- 受け取るには、claude.ai の設定 → 請求 →「API クレジット」から、Claude Console の組織を1つリンクします
- 使えるのは Claude API・Playground・Managed Agents・Agent SDK で、Claude Code を対話で使う分には使えません
- 繰り越しがないので、まずリンクだけ済ませて、毎月 API を使う処理に回してみてください

参考: Anthropic ヘルプページ「Monthly API credits for Max and Team plans」（support.claude.com/en/articles/17154008）

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
