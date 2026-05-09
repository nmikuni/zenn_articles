---
title: "テスト記事"
emoji: "🧪"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["zenn", "test"]
published: false
---

## はじめに

これは Zenn と GitHub の同期テスト用の draft 記事です。`published: false` のまま push すると、Zenn ダッシュボードの「下書き」一覧で確認できます。

## 動作確認項目

- [ ] GitHub への push で Zenn 側に反映されるか
- [ ] プレビュー (`npx zenn preview`) で正しく表示されるか
- [ ] `published: true` に変更すると公開されるか

```ts
const hello = (name: string) => `Hello, ${name}!`;
console.log(hello("Zenn"));
```
