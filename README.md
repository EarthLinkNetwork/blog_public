# blog_public — EarthLinkNetwork 技術ブログ 公開リポジトリ

**このリポジトリは公開経路 (Zenn 連携・自社サイト配信) 専用の生成物置き場です。**

- 記事の**正本ではありません**。正本と管理は private の母艦リポジトリ (`~/dev/blog`) にあります。
- ここに入るのは、母艦で `workflow.stage = approved` になった記事だけを exporter が整形して吐き出した**公開確定分**です。
- **手編集しないでください。** 修正は母艦側の `content/articles/<ID>.md` に対して行い、再エクスポートします (ADR-0001 §13 / ADR-0002)。

## 構成

```
blog_public/
├── articles/   # Zenn 連携対象の公開記事 (Markdown)。approved のみ
├── images/     # 公開画像 (マスク・トリミング済み)
└── README.md
```

## 公開してはいけないもの (`.gitignore` で二重防御)

母艦の内部資産 — 発掘メモ・claim 台帳・管理アプリ (`tools/`)・記事の下書き (`content/`)・公開リスクラベル付き台帳 (`ARTICLE-CATALOG.md`) ・未加工スクリーンショット・秘密情報 — は**このリポジトリに入れません**。混入防止のため `.gitignore` で denylist しています。

## Zenn 連携

Zenn は本リポジトリの `articles/*.md` を公開対象として読みます。したがって `articles/` に入れてよいのは、著者 (上原正義) が公開承認した記事のみです。

## 運用フロー

1. 母艦 (`~/dev/blog`) で記事を執筆・レビュー → `approved` にする
2. exporter が approved 記事を Zenn 形式へ整形し、本リポの `articles/` へ出力
3. `git push` で Zenn / 自社サイトへ反映

著者・公開責任: 上原正義 (EarthLinkNetwork)。発掘・草稿・レビューには Claude Code と Codex を使用し、内容確認と公開判断は著者が行っています。
