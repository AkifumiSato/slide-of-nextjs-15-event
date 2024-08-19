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

| Mechanism                                                                                          | What                                      | Where  | Purpose                                  | Duration                 |
| -------------------------------------------------------------------------------------------------- | ----------------------------------------- | ------ | ---------------------------------------- | ------------------------ |
| **Request Memoization**                                                                            | 関数の戻り値                              | Server | React Component treeにおけるdataの再利用 | リクエストごと           |
| <span class="font-bold" v-mark="{ at: 1, color: 'orange', type: 'circle'}">Data Cache</span>       | APIレスポンスやデータベースアクセスの結果 | Server | ユーザーやデプロイをまたぐデータの再利用 | 永続化 (revalidate可)    |
| <span class="font-bold" v-mark="{ at: 1, color: 'orange', type: 'circle'}">Full Route Cache</span> | HTMLやRSC payload                         | Server | レンダリングコストやパフォーマンスの向上 | 永続化 (revalidate可)    |
| **Router Cache**                                                                                   | RSC Payload                               | Client | ナビゲーションごとのリクエスト削減       | ユーザーセッション・時間 |

---

# v14以前のキャッシュのデフォルト

デフォルトで積極的にキャッシュし、初見殺しな部分が見られた

- Router Cache: 有効期間がデフォルトで30s or 5mと長め
- Data Cache: `fetch()`はデフォルトでキャッシュが有効
- Full Route Cache:
  - Router Handlerの`GET`がキャッシュ可能かつデフォルトで有効
  - ページやレイアウトはデフォルトでキャッシュが有効
- etc...

---

# v15でのキャッシュのデフォルト

大きく以下3つが修正された

- Router Cache: 有効期間のデフォルトが0sに変更
- Data Cache: `fetch()`のデフォルトが`cache: "no-store"`に変更
- Router Handlerの`GET`がデフォルトでキャッシュされなくなった

<span v-mark="{ at: 1, color: 'red', type: 'underline'}" class="font-bold">キャッシュにおける初見殺しが緩和された</span>

---
layout: fact
---

## おそらくこの変更があったから<br>Next Confを待たずにv15になった...？

---
layout: section
---

# PPR

---

# Static/Dynamic Rendering

TBW

---

# PPRとは

TBW

---

# PPR incrementalオプション

PPRはページをStatic Renderingしつつ、一部Suspense境界内をDynamic Rendering

- PPRはページをStatic Renderingしつつ、一部Suspense境界内をDynamic Rendering

TBW
