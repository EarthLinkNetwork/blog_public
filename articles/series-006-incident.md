---
title: "AWS Amplifyで繰り返した4つの失敗 — ビルドが通っても本番で動くとは限らない"
emoji: "🔥"
type: "tech"
topics: ["aws", "amplify", "cicd", "devops", "troubleshooting"]
published: true
---

<!-- このファイルは gen-publish.mjs が生成した発行用スケルトン。翻訳(海外媒体)は Claude が en.md 経由で行う。 -->

上原正吉（EarthLink Network Co., Ltd.）。Claude Codeを開発の主体に据え、20を超えるプロダクトを1人で同時に開発・運用しています。これは、その現場の実測記です。

<!-- TODO(Claude): この zenn 版は媒体トーン定義（/docs/platform-tone）に合わせてトーン・長さを調整する。方向性: 技術・実装重視。設計判断と数字を厚く。開発者向け。 以下は基となる本文。調整後にこのコメントを消す。 -->

# AWS Amplifyで繰り返した4つの失敗 — ビルドが通っても本番で動くとは限らない

![AWS Amplifyで繰り返した4つの失敗 — ビルドが通っても本番で動くとは限らない](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/series-006/hero-ogp.png)

2026 年の 4 月から 7 月にかけて、私は複数の Next.js プロダクトを AWS Amplify Hosting に載せて運用しました。その過程で繰り返した「Amplify 固有の失敗」を、この記事では 4 系統に整理します。env が消える、ビルドは通るのに実行時に落ちる、失敗が無音のまま進む、そしてドメイン移行で CloudFront の古い割り当てが残ってつまずく——複数のプロダクトを Amplify で運用する中で繰り返したので、再現条件と防ぎ方をまとめておきます。

先に結論を置きます（本文の読了は約 10 分）。

- **env を触る API は「追記か置換か」を先に確認する** — Amplify の `update-app --environment-variables` は渡したマップで全置換します。1 個足すつもりが、既存の env が丸ごと消えて本番の認証を全滅させました。env を直したらリビルドするまでが修正です。
- **ビルドが通っても本番で動くとは限りません** — Amplify SSR（サーバーサイドレンダリング）はブランチ環境変数を実行時 Lambda に渡さず、`NEXT_PUBLIC_*` はビルド時に焼き込まれます。だからビルドは通るのに実行時に env 不足で 500 になります。preBuild で `.env.production` に焼き込むか、実行時に env を解決する層を通します。
- **失敗を隠さない** — ACCESS_DENIED を HTTP 200＋サンプルデータで隠すと、障害が監視にもテストにも映りません。請求の 87〜89% はビルド時間だったので、節約すべきは SSR ではなく auto-build でした。secret はビルドスペックに書きません。
- **ドメイン association の削除は即ダウンで、素早く巻き戻せません** — 削除と再作成を速く回すほど、CloudFront の解放待ちで失敗が積もります。75 分待って 1 回だけ試し、失敗したらリトライしない、が正解でした。

## 失敗1: `update-app` は env を「全置換」する

最初の一発は、ある会員コミュニティサイトの本番 OAuth 認証が突然止まった事件でした。原因は、過去のセッションが環境変数を 1 個だけ足すつもりで次のコマンドを打っていたことです。

```bash
aws amplify update-app --app-id <APP_ID> \
  --environment-variables NEW_KEY=value
```

`update-app --environment-variables` は、渡したマップで**既存の環境変数マップ全体を置換**します。追記ではありません。結果、それまで設定されていた `ONION_CLIENT_ID` などがマップごと吹き飛び、認証クライアント ID が存在しない値に置き換わり、本番の認証が全滅しました。さらに厄介だったのは、値を Console で直したあと**リビルドしなかった**こと。Amplify は環境変数をビルド時に成果物へ焼き込むので、壊れた値が焼き込まれたビルドがそのまま本番で動き続け、「Console 上の値は正しいのに本番は壊れたまま」という状態になりました。

ここから私たちは「Amplify の env は Console から手動で触る、CLI の `update-app` で env を触るのは原則禁止」というルールを立て、スキル（自動チェック）に落としました。教訓は 2 つ。**env 操作 API は追記と置換のどちらか必ず確認する**。そして **env を直したら必ずリビルドするまでが修正**、です。

![Amplify の update-app に environment-variables を渡した瞬間、既存の env マップが丸ごと置き換わる様子（画面は再構成・匿名、挙動は実測）。1 個足すつもりが、ONION_CLIENT_ID などが消えて本番の認証が全滅しました](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/series-006/env-replace.png)

## 失敗2: ビルドは通るのに、実行時に落ちる

次の系統は、もっと発見が遅れる厄介なやつです。**Amplify の SSR では、ブランチ環境変数が実行時 Lambda に伝搬しない**。

具体的には 2 段構えの罠でした。管理画面を Amplify に載せたら NextAuth の API ルートが全部 500。調べると、(1) `NEXT_PUBLIC_*` は `next build` の時点で静的にインライン化されるため、ビルド時に未設定だと空文字がバンドルに焼き付く。(2) さらに、アプリ／ブランチレベルの環境変数は**ビルドコンテナには入るが、SSR を動かす Lambda ランタイムには転送されない**。ビルドは環境変数が見えるので普通に通り、実行時になって初めて「値が無い」で落ちる。診断用に作った `/api/_debug-env` 自体も「Next.js の API ルートは `_` 始まり不可」で 404 になりました。

回避策は、まだ変数が見える preBuild フェーズで `.env.production` を生成して焼き込むことです。

```yaml
# amplify.yml (preBuild で env を .env.production に焼き込む)
frontend:
  phases:
    preBuild:
      commands:
        - |
          cat > .env.production <<EOF
          NEXT_PUBLIC_API_BASE=${NEXT_PUBLIC_API_BASE}
          NEXTAUTH_URL=${NEXTAUTH_URL}
          EOF
        - npm ci
```

同じ罠は、あとから型安全 env ライブラリ（`createEnv` がモジュールロード時に `process.env` を検証するタイプ）を入れたときにも再発しました。ローカルと CI のビルドは通るのに、本番 Lambda で `Invalid environment variables` が投げられて 500。「ビルドが通ること」が逆に障害を隠していたわけです。対応は 3 つの PR を即 revert → インシデント記録 → `resolveRuntimeEnv` 方式（実行時に安全に env を解決する層）で再設計 → staging から段階再適用、という 4 段階でした。以後これを「build-once + runtime injection」として恒久ルールにしました。**Amplify SSR では「ビルドが通る」ことと「ランタイムで動く」ことは一致しません**。

## 失敗3: 失敗が無音で進む

3 つ目は「壊れているのにアラートが鳴らない」系統です。3 つ紹介します。

1 つ目。管理画面の全リストが空白になった障害。SSR の compute role（`computeRoleArn`）が null で DynamoDB へのアクセスが全滅していたのに、API が **ACCESS_DENIED のとき HTTP 200 + サンプルデータを黙って返す**設計だったため、監視にもテストにも障害が映りませんでした。しかも本番 E2E（エンドツーエンドの通し検証）は認証バイパスが無効化された経路を一度も通っておらず、「認証後のデータ取得」が丸ごと検証の穴でした。ここから fail-loud（エラーは 200 で隠さず落とす）・staging promotion・認証済み E2E の 3 点セットを制定しました。

2 つ目。ある月の Amplify 請求を Cost Explorer で分解したら、**費用の 87〜89% が BuildDuration（ビルド時間）**で、SSR ランタイムはわずか月 $3〜7 でした。「SSR を軽くする」という当初の節約計画は的外れで、正しくは「auto-build を止めて成果物だけデプロイする」でした。無音のコスト漏れも同じ穴です。

![Amplify 請求を Cost Explorer で分解した内訳（画面は再構成・匿名、数値は実測）。費用の 87〜89% はビルド時間で、SSR ランタイムは月 $3〜7 しかない。節約すべきは SSR ではなく auto-build の方でした](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/series-006/cost-breakdown.png)

3 つ目。`amplify.yml` がビルド時に `SES_ACCESS_KEY_ID/SECRET` を `.env.production` に**平文で書き込み**、それが `.next/` にコピーされてデプロイ成果物にもビルドログにも残っていました。secret がそのまま漏れる経路です。幸い攻撃者のアクションは `CreateUser` 1 回（AccessDenied でブロック）のみで日次コストのスパイクも無く実害ゼロでしたが、**ビルドスペックに secret を書くとログが漏えい面になる**という教訓を残しました。

## 失敗4: ドメイン移行と、解放されない CloudFront

最後はインフラ移行系です。複数の Amplify アプリを 1 つの hub に集約する過程で、ドメイン association の付け替えを何度もやりました。ここで学んだのは **Amplify の domain association 削除は即ダウンタイムで、素早く巻き戻せない**ということ。

あるカナリア移行で「旧 association 削除 → 即 hub に作成 → DNS 切替」の手順を踏んだところ、CloudFront の alias 解放が遅れて hub 側の association が FAILED。DNS を旧ターゲットに戻しても、旧 association はもう削除済みなので復旧せず、約 6 分ダウン。alias が解放される（約 6 分）のを待ってから FAILED を削除・再作成してようやく戻りました。

さらに別ドメインの移行では、作成が 5 回連続で失敗しました。削除→再作成を繰り返すたびに、alias を掴んだまま消えない CloudFront distribution（解放待ちで残る、いわゆるゾンビ）が積もり、次の試行を即 FAILED にする。削除と再作成を速く回すほど失敗が増える悪循環です。対策は逆転の発想でした。

```text
# アンチチャーン手順（擬似コード）
1. DNS を新ターゲットへ向け替える
2. 全ゾンビの alias ロックが解放されるまで 75 分「何もしない」
3. association 作成を「1 回だけ」試す
4. 失敗したら深い障害としてリトライせず即 exit（スパイラルに戻さない）
```

「リトライしないこと」が正解、という珍しいケースでした。

![ドメイン移行の失敗と、その対策手順（画面は再構成・匿名、数値は実測）。alias を掴んだまま残る distribution が積もり 5 回連続で FAILED。解放を 75 分待ってから作成を 1 回だけ試し、失敗したらリトライしない、が正解でした](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/series-006/domain-antichurn.png)

これより前、pnpm monorepo の Next.js SSR を Amplify に載せる初期にも、「node_modules をどこにコピーするか」だけで 2 日間に fix コミットを 14 連発した記録があります。standalone 出力・コピー先変更・symlink 解決と迷走した末、「新しい Amplify は SSR をネイティブ処理するので standalone 不要」に到達しました。

## なぜ Amplify では build 成功と runtime 成功が一致しないのか

失敗 1〜3 の根っこは同じで、Amplify SSR の実行モデルにあります。`next build` は**ビルドコンテナ**で走り、ここには env が見える。生成された SSR は**別の Lambda**として実行され、ここにはブランチ env が自動では渡らない。加えて `NEXT_PUBLIC_*` はビルド時にコード中へ文字列として焼き込まれ、実行時の `process.env` とは別物になる。つまり env が「効く瞬間」が、静的インライン（ビルド時）・成果物への焼き込み（ビルド時）・Lambda の実行環境（実行時）の 3 箇所に分裂している。この分裂を知らずに「env を設定した＝どこでも使える」と思い込むと、ビルドは通るのに本番で落ちる。だから env を直したらリビルドが要り、runtime で必要な値は preBuild で焼き込むか runtime injection 層で解決する必要がある——すべてこの実行モデルの帰結です。

## 他の環境でも使える教訓

- env を触る API は「追記か置換か」を必ず確認する。Amplify の `update-app --environment-variables` は全置換。env を直したらリビルドまでが修正。
- Amplify SSR は「ビルドが通る」と「実行時に動く」が別。runtime で要る値は preBuild で `.env.production` に焼き込むか runtime 解決層を通す。load-time validation を入れる前に必ず staging で実行時を踏む。
- 失敗を隠さない。ACCESS_DENIED を 200＋サンプルで隠さず落とす（fail-loud）。コストは Cost Explorer で分解し、secret はビルドスペックに書かない。
- ドメイン association の削除は即ダウン。alias 解放を待ってから DNS を切り替え、失敗が続いたら「待って 1 回だけ」に切り替えてリトライを止める。

## 筆者について

上原正吉。EarthLink Network Co., Ltd. でAI開発をしています。2025年からClaude Codeを開発の主体に据え、今は20を超えるプロダクトを1人で同時に開発・運用しています。この連載では、その現場で実際に起きたこと（うまくいったことも、失敗も）を、数字と一緒に書いていきます。

また、AIで業務や開発を組み替えたい会社・チーム向けに、AI活用のコンサルティングも受け付けています。ご相談は [www.eln.ne.jp](https://www.eln.ne.jp) からどうぞ。

---

**EarthLink Network** は、会社の全業務を AI で回すために、必要になったものを自社で作っています。いま作っているプロダクトの一覧と概要は、こちらにまとめています。

→ [EarthLink Network が自社でつくっている18のプロダクト](https://zenn.dev/chooser/articles/in-house-products)

会社と各プロダクトの詳細は、公式サイト [www.eln.ne.jp](https://www.eln.ne.jp) をご覧ください。

