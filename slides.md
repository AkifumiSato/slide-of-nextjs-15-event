---
theme: default
# https://unsplash.com/ja/%E5%86%99%E7%9C%9F/%E9%BB%92%E3%81%84%E8%83%8C%E6%99%AF%E3%81%A8%E8%B5%A4%E3%81%84%E5%85%89%E3%81%AE%E6%8E%A5%E5%86%99-9XL5mIpZLCU
background: /background.png
highlighter: shiki
lineNumbers: false
title: Next.js v15.0.0-rc.0
info: |
  ## Next.js v15.0.0-rc.0

  キャッシュのデフォルト挙動改善とPPR
class: text-center
drawings:
  persist: false
transition: slide-left
mdc: true
---

# Next.js v15.0.0-rc.0

キャッシュのデフォルト挙動改善とPPR

---

# Next.js v15.0.0-rc.0での更新内容

現在v15はReact19のリリース待ちの状態

- React19サポート
- <span v-mark="{ at: 1, color: 'orange', type: 'underline'}">キャッシュのデフォルトを一部変更</span>
- <span v-mark="{ at: 1, color: 'orange', type: 'underline'}">PPRのincrementalオプションが追加</span>
- `next/after`の追加
- create next appのデザイン更新、オプション追加
- [and more...](https://nextjs.org/blog/next-15-rc#create-next-app-updates)

---
layout: section
---

# Cache

---

# App Routerのキャッシュ

4層のキャッシュが存在する

| Mechanism                                                                                                           | What                        | Where  | Purpose                                  | Duration                 |
| ------------------------------------------------------------------------------------------------------------------- | --------------------------- | ------ | ---------------------------------------- | ------------------------ |
| **Request Memoization**                                                                                             | 関数の戻り値                | Server | React Component treeにおけるdataの再利用 | リクエストごと           |
| <span v-mark="{ at: 1, color: 'orange', type: 'circle'}"><strong>Data Cache</strong></span>                         | `fetch()`やDBアクセスの結果 | Server | ユーザーやデプロイをまたぐデータの再利用 | 永続化 (revalidate可)    |
| <span class="font-bold" v-mark="{ at: 1, color: 'orange', type: 'circle'}"><strong>Full Route Cache</strong></span> | HTMLやRSC payload           | Server | レンダリングコストやパフォーマンスの向上 | 永続化 (revalidate可)    |
| <span v-mark="{ at: 1, color: 'orange', type: 'circle'}"><strong>Router Cache</strong></span>                       | RSC Payload                 | Client | ナビゲーションごとのリクエスト削減       | ユーザーセッション・時間 |

---

# v14以前のキャッシュのデフォルト

デフォルトで積極的にキャッシュ

- Router Cache: 有効期間がデフォルトで30s or 5mと長め
- Data Cache: `fetch()`はデフォルトでキャッシュが有効
- Full Route Cache:
  - Router Handlerの`GET`がキャッシュ可能かつデフォルトで有効
  - ページやレイアウトはデフォルトでキャッシュが有効
- etc...

<br>

### 筆者の意見

<span v-mark="{ at: 1, color: 'red', type: 'underline'}" class="font-bold">キャッシュにおける**初見殺し**な部分があった</span>

---

# v15でのキャッシュのデフォルト

大きく以下3つが修正された

- Router Cache: 有効期間のデフォルトが0sに変更
- Data Cache: `fetch()`のデフォルトが`cache: "no-store"`に変更
- Router Handlerの`GET`がデフォルトでキャッシュされなくなった

<br>

### 筆者の意見

<span v-mark="{ at: 1, color: 'red', type: 'underline'}" class="font-bold">キャッシュにおける初見殺しが緩和された</span>

---

# 「なぜもっと早く変更しなかったのか？」

[Jimmy Lai氏のツイート](https://x.com/feedthejim/status/1792973728512426304)を要約

- デフォルトのキャッシュ挙動の変更は、2023/11頃にはすでに議論が始まっていた
  - 問題はどうやってパフォーマンスを損ねずに実現するか
  - Next.jsの哲学としてデフォルトで高いパフォーマンスが実現できることは非常に重要
- PPRがデフォルトになれば全て解決する
  - PPRは現在experimentalだが、今後安定したらデフォルトになる予定
- PPRの設計や実装が進み、Next.jsとしての方針が固まった
  - デフォルトのキャッシュ挙動を変更しても良いと判断できるようになった

---
layout: section
---

# PPR

---

# Static/Dynamic Rendering

従来におけるSSR/SSG/ISG相当と考えるとわかりやすい

- Static Rendering: ページを事前に生成、revalidate可能
- Dynamic Rendering: ページをリクエスト時に生成

---
transition: fade
---

# PPRとは

「可能な限りStaticに、部分的にDynamicに」を可能にするレンダリングモデル

![PPR](/ppr.png)

---
transition: fade
---

# PPRとは

「可能な限りStaticに、部分的にDynamicに」を可能にするレンダリングモデル

- PPR: Partial Pre-Rendering(部分的な事前レンダリング)
- Routeは基本Static Rendering、一部Suspense境界内をDynamic Renderingにすることが可能
- パフォーマンスとシンプルな設計が両立
- 15.0.0時点ではexperimental、実装進行中
  - (Next.js側の実装がとても大変)

<br>

### 筆者の意見

考える粒度がページ単位からSuspense境界単位になり、<span v-mark="{ at: 1, color: 'red', type: 'underline'}" class="font-bold">よりReactらしい設計</span>

---

# PPRとは

PPRにおける注意点

- CDNと相性が悪い
- HTTP Status Codeに依存した監視などができない
  - (実はPPRに限った話ではないが、より影響が顕著)
- Next.js側の実装が大変なためまだ不安定

---

# PPRの参考資料

より詳細な内容については筆者の過去記事を参照

https://zenn.dev/akfm/articles/nextjs-partial-pre-rendering

<div class="flex justify-center pt-5">
  <img width="400" src="/zenn-ppr-article.png" alt="PPRの記事">
</div>

---

# PPRの有効化

v14系ではexperimentalフラグを有効にすると、全ページでPPRが有効になった

```ts
// next.config.ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  experimental: {
    ppr: true, // v14系ではboolean
  },
};

export default nextConfig;
```

---
transition: fade
---

# PPR incrementalオプション

v15.0.0-rc.0から`incremental`オプションが追加された

```ts
// next.config.ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  experimental: {
    ppr: "incremental",
  },
};

export default nextConfig;
```

---

# PPR incrementalオプション

Route単位で`experimental_ppr`が`true`の時のみPPRが有効化

```tsx
// app/page.tsxなど
export const experimental_ppr = true;
```

---
layout: section
---

# まとめ

---

# v15の変更まとめ

筆者が注目してる点は以下2つ

- キャッシュのデフォルト設定が変更され、初見殺しが緩和された
- PPRの実装が進み、段階的導入が可能となった

App Routerがより<span v-mark="{ at: 1, color: 'red', type: 'underline'}" class="font-bold">初学者に優しく・シンプルな設計になると期待</span>できそう
