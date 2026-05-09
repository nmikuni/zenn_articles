---
name: zenn-article
description: Zenn CLI を使ってこのリポジトリの記事 (articles/*.md) を作成・編集・プレビュー・公開する手順。記事を新規作成したい、フロントマターを直したい、プレビューを立ち上げたい、下書きを公開したい、予約公開したい、Zenn 独自の Markdown 記法 (`:::message`, `:::details`, ファイル名つきコードブロック, diff, 埋め込みなど) を使いたい、デプロイ失敗 (slug 衝突など) を直したい、といった依頼で使う。本 (books) には触れない。
---

# Zenn 記事の作成・編集ガイド

このリポジトリは Zenn と GitHub 連携されています (`main` ブランチが同期対象)。`articles/*.md` を push すると Zenn 側に自動デプロイされます。

参考: <https://zenn.dev/zenn/articles/zenn-cli-guide>, <https://zenn.dev/zenn/articles/markdown-guide>

## 1. 新規記事の作成

```bash
npx zenn new:article --slug <slug> --title "<title>" --type tech --emoji 🧪
```

- `--type` は `tech` (技術記事) か `idea` (アイデア)
- `--emoji` は **1 文字のみ**。複合絵文字 (ZWJ など) は不可
- `--slug` の制約: `a-z0-9` + `-` + `_` の **12〜50 字**
- slug は **Zenn サイト全体で一意**。衝突するとデプロイが「Slug は既に使用されています」で中断する。`test-` 等の汎用語は衝突しやすいので、ユーザー識別子 (例: `test-mick-...`) を混ぜると安全

引数を省略すると自動でランダム slug の draft が生成される。

## 2. フロントマター

```yaml
---
title: "記事タイトル"
emoji: "🧪"            # 1 文字
type: "tech"           # tech | idea
topics: ["zenn", "test"]  # 最大 5 個
published: false       # true で公開
published_at: "2026-05-09 10:00"  # 任意。JST。未来指定で予約公開
---
```

- `published: false` のまま push すれば下書きとして同期される (ダッシュボードの「下書き」で確認可)
- 既存記事を移行するときは `published_at` に過去日時を入れて公開日を維持
- `publication_name` を指定すると Publication に紐づけられる

## 3. ローカルプレビュー

```bash
npx zenn preview
```

`http://localhost:8000` で表示。ファイル監視つきなので保存で自動反映。終了は Ctrl+C。

## 4. Zenn 独自の Markdown 記法

### メッセージ / 警告
```
:::message
補足メッセージ
:::

:::message alert
注意・警告
:::
```

### アコーディオン
```
:::details タイトル
折りたたまれる本文
:::
```
ネストするときは外側のコロンを増やす (`::::details ... ::::`)。

### コードブロック
ファイル名つき / diff:
````
```ts:src/foo.ts
const x = 1;
```

```diff ts
- const x = 1;
+ const x = 2;
```
````

### 埋め込み
URL **だけの行**にすると自動でカード/埋め込みになる:
- リンクカード (任意の URL)
- X (旧 Twitter) のポスト URL
- YouTube, GitHub (行範囲指定可), CodePen, SpeakerDeck, Figma など

### その他
- 脚注: 本文中に `[^1]`、別行に `[^1]: 注釈`
- 数式: ブロック `$$ ... $$`、インライン `$ ... $` (KaTeX)
- 図: ` ```mermaid ` でフローチャート等

## 5. 公開・更新・削除フロー

| 操作 | 手順 |
|------|------|
| 公開 | `published: true` にして commit & push |
| 下書き同期 | `published: false` のまま push |
| 予約公開 | `published_at` に未来の JST 日時を指定 |
| 更新 | 同じ slug のファイルを編集して push |
| slug 変更 | 別記事扱いになる (旧 slug の記事はダッシュボードに残る) |
| 削除 | **Zenn ダッシュボードから削除**。ファイル削除だけでは消えない |

デプロイは <https://zenn.dev/dashboard/deploys> で確認できる。失敗時はメッセージを読んで該当ファイルを修正 → 再 push。

## 6. 画像

参考: <https://zenn.dev/zenn/articles/deploy-github-images>

GitHub リポジトリ内に画像を置く方式 (推奨):

- 配置場所: リポジトリ直下の `/images/`。サブディレクトリは自由 (`images/<slug>/foo.png` など記事ごとに分けると整理しやすい)
- 対応形式: `.png` / `.jpg` / `.jpeg` / `.gif` / `.webp`
- ファイルサイズ: **3MB 以内**
- 参照は **絶対パスで `/images/` から**:
  - OK: `![](/images/foo.png)`, `![](/images/my-article/foo.png)`
  - NG: `![](../images/foo.png)`, `![](//images/foo.png)`
- push すると自動で Zenn 側に同期される。**ファイルを削除すると Zenn 側でも消える** ので、参照中の画像を消さないこと
- `npx zenn preview` (CLI 0.1.93 以降) でローカル表示を確認できる

代替手段:
- Zenn の画像アップロードページから上げて発行された URL を貼る
- Gyazo など外部サービスの URL を貼る

## 7. よくあるハマりどころ

- **slug 衝突**: 一度公開した slug は再利用できない。衝突したらファイルをリネームして再 push
- **絵文字 1 文字制約**: `🧑‍💻` のような ZWJ 結合絵文字は弾かれることがある
- **topics は 5 個まで**: 6 個以上を入れるとエラー
- **デプロイ反映に時間差**: キャッシュのため、push 直後に反映されないことがある
- **`.claude/` を git に含めない**: ローカル設定は `.gitignore` で除外済み

## 8. CLI のアップデート

```bash
npm install zenn-cli@latest
```

プレビュー時に更新通知が出たら実行。
