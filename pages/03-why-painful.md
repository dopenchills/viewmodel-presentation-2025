---
layout: section
transition: slide-left
---

# なぜ辛いのか

---

# UIフレームワークに強く依存しているから

<div class="py-4"></div>

UIフレームワークや状態管理ライブラリから、あらゆる面で大きな影響を受けてしまう

- ❌ トレンドが変わるたびにベストプラクティスが変わる
- ❌ Majorバージョンを勝手に上げて、大量のBreaking Changesを引き起こす
- ❌ ライブラリがEOLしたらマイグレーションを考える必要がある
- ❌ テストにはフレームワークにロックインした特殊なツールが必要になる
- ❌ **場当たり的な対応をするのが当たり前になり、普遍的な方法論が積み上がらない**

---
layout: section
---

# 依存が起こした悲劇の実例

---

## 実例: AngularJSのEOL

<div class="py-4"></div>

Googleが開発したフロントエンドフレームワーク。

2010年に開発が開始され、人気になったのちにEOLした。

後継のAngularとはほぼ互換性がなく、フルスクラッチレベルの書き直しが必要となった。


---

## 必要となった工数

<div class="py-4"></div>

| 会社/プロジェクト | 移行期間 | 移行先 | 備考 | 参照リンク |
|-----------------|---------|-------|------|----------|
| Holistics | 4ヶ月 | Vue.js | | [ブログ記事](https://www.holistics.io/blog/why-and-how-we-migrated-from-angularjs-to-vuejs/) |
| ANDPAD | 8ヶ月 | NuxtJS | | [ブログ記事](https://tech.andpad.co.jp/entry/2021/03/11/170000) |
| NulabのTypeTalk | 62人月 | Angular | | [ブログ記事](https://nulab.com/ja/blog/typetalk/things-we-did-on-angular-migration/) |
| 個人プロジェクト | 2.5年 | Angular | 10万行、1700ファイル | [Medium記事](https://medium.com/@rochat.aurelia/from-angularjs-to-angular-a-2-years-journey-5afcebe050a8) |
| ButterCMS | 3年 | React | | [ブログ記事](https://buttercms.com/blog/migrating-from-angularjs-to-react/) |


---

## 実例: Recoilのアーカイブ

<div class="py-4"></div>

Metaの実験的な状態管理ライブラリ。

2025年1月、Metaの人員整理の影響でメンテナンス不能になった。

状態管理ライブラリはコードベース全体で依存しがちなため、多くの箇所で書き直しが見込まれる。

---

## その書き直しは本当に必要だったのか？

<div class="py-4"></div>

- プロジェクト全体がUIフレームワークに依存してしまっていたからでは？
- 状態管理ライブラリってそもそも必要？

<div class="py-4"></div>

<div class="text-2xl">→ 依存を整理すれば解決するはず！</div>
