---
title: "認証・認可を、社内の全製品で使う1つの基盤にまとめる"
emoji: "📝"
type: "tech"
topics: ["auth", "sso", "oauth", "security"]
published: true
---

<!-- このファイルは gen-publish.mjs が生成した発行用スケルトン。翻訳(海外媒体)は Claude が en.md 経由で行う。 -->
<!-- gen-publish:src-sha256=389c540b226443e0c31dce3370c543b74a44dfea6ece00875a366e190ec88f39 -->

上原正吉（EarthLink Network Co., Ltd.）。Claude Codeを開発の主体に据え、20を超えるプロダクトを1人で同時に開発・運用しています。これは、その現場の実測記です。

<!-- TODO(Claude): この zenn 版は媒体トーン定義（/docs/platform-tone）に合わせてトーン・長さを調整する。方向性: 技術・実装重視。設計判断と数字を厚く。開発者向け。 以下は基となる本文。調整後にこのコメントを消す。 -->

![認証・認可を、社内の全製品で使う1つの基盤にまとめる](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/onion-004/hero-ogp.png)

# 認証・認可を、社内の全製品で使う1つの基盤にまとめる

## 結論

ELN IDはEarthLink Networkが法人として進める自社プロジェクトで、人間の担当者1名とAIを実行主体にしたチームで開発しています。組織管理アプリとして始めたELN IDを、複数の製品が共通で使う認証・認可の基盤（Identity Provider、IdP）へ広げました。7つの製品が誰を管理者と認めているかを調べると、想定していたServiceRoleを使う製品は0件でした。実際に使われていた`globalRole === 'ADMIN' || groups.includes('<serviceId>-admin')`を共通ルールにしています。

誰が管理者かは、1か所だけで決めます。トークンを最初に発行するときも、あとで更新するときも、同じ`resolveScimGroupNames()`を通します。画面は自分で判断せず、サーバーが出した`isAdmin`をそのまま表示します。トークンを更新すると管理者グループの情報が消える問題と、APIは通るのに管理画面が断られる問題を、この方法で直しました。

## 本文（読了 約8分）

2026年6月、私が保守していた組織管理アプリ ELN ID は、3か月かけて社内の複数製品が使うIdPへ広がりました。短期間に「OAuthクライアントを発行して」という相談が15回を超え、連携のルールを製品ごとに作るのではなく共通の仕様にする必要が出てきました。

## 認証の基盤にするとき決めた3つのこと

きっかけは受け身でした。ある製品から「サイレントSingle Sign-On（SSO、1回の認証を複数のサービスで使う仕組み）と、利用規約への同意をまとめて取りたい」という相談が来て、これを個別に対応するか、共通のルールにするかの分かれ道に立ちました。私は後者を選び、連携のルールを設計記録に書くことにしました。最初に決めたのは3つです。

1つ目は、**サイレント認証をどう実装するか**です。サイレント認証とは、利用者に入力させず、裏で「まだログイン済みか」を確かめて、済んでいれば自動でログイン状態にする仕組みです。連携先の画面を開いた瞬間に、いちいちログイン画面を出さずに済ませたい、という狙いです。当初は、画面に見えない小さな窓（hidden iframe）を置き、その中で「今ログインしているか」だけを問い合わせる定番のやり方を考えました。ところが実機では、常に「ログインしていない」と返ってきます。原因は、ELN IDがログイン状態を覚えておくためのCookieが`SameSite=Lax`という設定で発行されていることでした。この設定だと、別サイトに埋め込まれた窓にはCookieが送られません。そこで、サイレント認証は画面ごと一瞬ELN IDへ移動して確かめる方式（トップレベルリダイレクト）に限定し、埋め込み窓は使わないと決めて設計記録に残しました。

2つ目は、**利用規約への同意をどう管理するか**です。ここでいう「同意の版」とは、利用者がどの版の利用規約に同意したかを表す印です。規約が新しくなったら、利用者にもう一度同意してもらう必要があります。そのため「今の版に同意済みか」を、ELN IDと連携先で食い違いなく判断できないと困ります。連携先が独自に「版2は版1より新しいから再同意は要らない」と数字で比べ始めると、ELN IDの判断とずれてしまいます。そこで、版はELN IDが決める文字列にして、中身の大小には意味を持たせないことにしました。連携先に許すのは「文字列がそのまま一致するか」の確認だけで、数値に変換したり大小を比べたりは禁止です。同意したかどうかの情報は、ログイン時に渡すトークンの中（`id_token`の属性）には入れません。必ず内部の確認用API（`verify-token`）で取り、今の版と文字が一致するかを照らし合わせます。

3つ目は、**すでにある同意の記録を移し替えない**という判断です。連携をELN IDへ集約する前、各製品は自前で「この利用者は規約に同意済み」という記録を持っていました。これをELN ID側へコピーして引き継ぐこともできますが、私たちはやめました。代わりに、切り替えた後の最初のログインで、全員に一度だけ同意し直してもらいました。古い記録を移し替える作業には、取りこぼしや取り違えの危険と手間がかかります。それを、初回の再同意一回で避けた形です。ここまでの決めごとは6月に設計記録として確定しました。同意の仕組みとサイレント認証は、先に失敗するテストを書いてから実装するやり方（テスト駆動開発、TDD）で4段階に分けて作り、最後は41件のテストがすべて通りました。

## 認可を4つの部品に分けて考える

連携が増えると、次に効いてくるのは「誰が管理者か」の判断です。ここが曖昧なまま各製品に散らばると、必ず事故ります。そこで認可を4つの部品に分けました。

- `globalRole`（`'ADMIN' | 'USER'`、DynamoDBのユーザーレコードに保存、既定は`'USER'`）は、全体管理者を示します。
- `ServiceRole`は、サービス単位のロールを示します。
- `ServiceTeam`は、サービス内のチーム所属を示します。
- `SCIMグループ`は、社外のIDシステムとユーザーやグループを自動でそろえる仕組み（SCIM、System for Cross-domain Identity Management）で作るグループです。

実際に、7つの社内製品が誰を管理者と認めているかを一つずつ調べました。設計上は、ServiceRoleを製品ごとの管理者の判断に使う想定でした。しかし、実際の作りは違いました。

```
admin判定の実地監査（7プロダクト）:
- product-A : globalRole === 'ADMIN' のみ
- product-B : globalRole + 独自RBAC
- product-C : JWT groups クレーム（<service>-admin）を主、globalRoleは後方互換fallback
- product-D : JWT groups クレーム（<service>-admin）
- product-E : globalRole OR ServiceTeam の staffRole
- product-F : ロールチェックなし（有効セッション＝admin扱い / 重大な認可欠陥）
- product-G : そもそもSSO未接続（ハードコードのuser/pass）
→ ServiceRole を admin判定に使うプロダクト: ゼロ
```

設計記録で理想を書いていたのに、現場は誰もServiceRoleを使っておらず、先に動いていた2つの製品は、それぞれ別々に「`<serviceId>-admin`というSCIMグループのメンバーかどうか」で管理者を見分ける形に落ち着いていました。私は理想を捨て、**現実に収束していたパターンを標準に昇格**させました。新しい標準形はこれです。

```ts
// 全製品共通の管理者チェックの標準形（監査で分かった実態に合わせた版）
const isAdmin =
  globalRole === 'ADMIN' ||
  groups.includes(`${serviceId}-admin`)
```

`globalRole === 'ADMIN'`は、緊急用の全体管理者に限定しました。日常のサービス単位の管理者は、`<serviceId>-admin`のSCIMグループで付与します。

ここでは、管理者を締め出さないための**デプロイの順番**も決めました。管理者チェックのコードを先に変えてしまうと、対応するSCIMグループやメンバーがまだ無い間は、すべての管理者がログインできなくなります。そこで、次の順番を必須にしました。

1. グループを作る
2. メンバーを割り当てる
3. 管理者チェックのコードを変える
4. デプロイして動作を確かめる

## なぜ直しても別の場所で壊れ続けたのか

このモデルへ移る途中で、認可のバグが立て続けに出ました。どれも「同じ判断を2か所で作っていたせいでズレる」という同じ形でした。

1つ目は、**トークンを更新すると管理グループの情報が消えてしまう**不具合です。ログインすると、利用者には身分証にあたるトークンが渡されます。これは時間が経つと失効するので、更新用トークン（`refresh_token`）を使って、ログインし直さずに新しいトークンを受け取ります。このトークンには、その人が入っている管理グループの一覧（`groups`）が入っていて、これで「管理者かどうか」を見分けます。ところが、最初にトークンを発行するとき（`authorization_code`）はグループを調べて`groups`を組み立てるのに、更新する処理（`issueTokensForRefresh()`）には同じ調べ物がなく、空（`undefined`）を渡していました。そのため、最初のログイン直後は管理者でも、トークンを更新した瞬間に`groups`が空になり、グループで与えていた管理権限が消えます。更新のときだけ起きるので、最初のログインを確かめる普通のテストでは見つかりませんでした。

この問題は、社内のセキュリティレビューでも同じ形で指摘されていました。「更新の経路だけグループを取ってこないので、更新後にグループの情報が消える」という指摘です。原文（英語）はこうです。

```
issueTokensForRefresh() (line 586) passes `undefined` as 5th arg to
generateAccessToken — SCIM groups are never fetched in the refresh path
→ any user with group-based access loses JWT groups claim after first
  token refresh
```

2つ目は**フロントエンドとバックエンドで見ている場所が違う**不具合です。バックエンド側の管理者チェック（`withAdminAuth`）は管理者グループのメンバーを認めるように直したのに、フロントエンド側（`AdminGuard`）は全体管理者かどうか（`globalRole`）しか見ていませんでした。その結果、グループで管理者になった人はAPIが通っても、画面は「Access Denied」になります。同じ判断を二か所で別々に作ったことで、この食い違いが起きました。

![認可の判断を1か所にまとめた関係図（本文のコードを再構成）](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/onion-004/authz-consolidation.png)

原因は、**誰が管理者かを、いくつもの場所でばらばらに決めていたこと**です。直したあとは1か所で決めます。トークンを扱うコードでは、発行時も更新時も共通で使う`resolveScimGroupNames(userId)`という関数を新しく作りました。`authorization_code`（発行）も`refresh_token`（更新）も、同じ関数を呼びます。

```ts
// sso/token/route.ts — 発行も更新も同じ resolver を通る
async function resolveScimGroupNames(userId: string): Promise<string[]> {
  const { ScimGroupMemberEntity, ScimGroupEntity } =
    await import('@/infrastructure/dynamodb/entities')
  const membershipResult =
    await ScimGroupMemberEntity.query.byUser({ userId }).go()
  // ...メンバーシップからグループ名を解決...
}

// authorization_code grant 側
const groups = await resolveScimGroupNames(userId)          // line ~309
const accessToken = generateAccessToken(userId, clientId, scopes, globalRole, groups, ttl)

// refresh grant 側（issueTokensForRefresh 内）
const groups = await resolveScimGroupNames(userId)          // line ~588
const accessToken = generateAccessToken(/* ... */ groups /* ... */)
```

フロント側も、サーバーが計算した`isAdmin`をAPIの返事に載せ、画面はそれを表示するだけにしました。管理者チェックはサーバー内の`userIsInScimGroup(...)`へ集約し、確認できなければ拒否する方針へ寄せています。

```ts
// withAdminAuth.ts — globalRole が ADMIN でなくても管理者グループなら許可
if (user.globalRole !== 'ADMIN') {
  const isOnionAdminGroupMember =
    await userIsInScimGroup(userId, ADMIN_GROUP)
  if (!isOnionAdminGroupMember) {
    return /* 403 */
  }
}
```

## その後と、残っている課題

管理者を割り当てるグループ（SCIMグループ）を、管理画面から作ったりメンバーを足したりできるようにしました。この画面を本番に出す前のセキュリティレビューで、重大な問題が2件・高リスクの問題が2件見つかり、出す前にすべて直しました。いちばん危なかったのは、メンバーを追加するときに「相手がそのグループと同じ組織の人か」を確かめておらず、`org-B`の利用者を`org-A`のグループに入れられてしまう穴です。別々の組織のデータが混ざる事故につながります。追加しようとする相手の所属組織（`targetUser.organizations`）に、対象の組織が含まれるかを確かめて塞ぎました。この管理者グループの仕組みは、先に動いていた2つの製品に続いて他の製品にも広げました。

一方で監査で見つかった「ロールチェックの無いプロダクト」「そもそもSSO未接続のプロダクト」は、この記事の時点では別タスクとして残っています。全体を覆う理想の認可モデルを書くことと、既存プロダクトを一つずつそこへ寄せることは、まったく別の労力なのだと痛感しました。

教訓を4つ残します。

- **連携のルールは設計記録に残します**。「iframe禁止」「versionは中身に意味を持たせない文字列」「移行しない」といった約束を実装前に決めると、連携先との食い違いを防げます。
- **実際に使われている権限の形を標準にします**。調べて誰もServiceRoleを使っていないと分かったため、SCIMグループを標準に変えました。
- **誰が管理者かは1か所で決めて、みんなに渡します**。画面、サーバー、トークンの発行時・更新時でばらばらに作ると答えがずれます。1つの処理にまとめ、確認できなければ断ります。
- **権限変更ではデプロイ順序も決めます**。グループとメンバーを先に作ってからコードを変えます。逆順では管理者全員が操作できなくなります。

---

この ELN ID（社内全製品の認証・認可をまとめる基盤）についての記事は、開発の経緯・どんな機能があるか・どう実装したかを、順次シリーズとして公開していきます。
興味のある方は、ぜひ「いいね」と記事の購読をお願いいたします。

自社プロダクトの一覧は https://www.eln.ne.jp/products にまとめています。

## 筆者について

上原正吉。EarthLink Network Co., Ltd. でAI開発をしています。2025年からClaude Codeを開発の主体に据え、今は20を超えるプロダクトを1人で同時に開発・運用しています。この連載では、その現場で実際に起きたこと（うまくいったことも、失敗も）を、数字と一緒に書いていきます。

また、AIで業務や開発を組み替えたい会社・チーム向けに、AI活用のコンサルティングも受け付けています。ご相談は [www.eln.ne.jp](https://www.eln.ne.jp) からどうぞ。

---

**EarthLink Network** は、会社の全業務を AI で回すために、必要になったものを自社で作っています。いま作っているプロダクトの一覧と概要は、こちらにまとめています。

→ [EarthLink Network が自社でつくっている18のプロダクト](https://zenn.dev/chooser/articles/in-house-products)

会社と各プロダクトの詳細は、公式サイト [www.eln.ne.jp](https://www.eln.ne.jp) をご覧ください。
