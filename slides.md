---
# Documentation: https://sli.dev/
# You can also start simply with 'default'
theme: apple-basic
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: MVVM

class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# open graph
# seoMeta:
#  ogImage: https://cover.sli.dev
---

# ViewModelがあなたを救う方法

UIライブラリに依存しない生活

※ UIライブラリ非依存はいい考え方。タイトルもそれっぽくできそう
※ さっきは「MVVMでよいという提案」としたが、もう一回考えたい

---
src: ./pages/Introduction/Frontend Development.md
---

---
src: ./pages/UI Library Migrations/Introduction.md
---

---
src: ./pages/UI Library Migrations/AngularJS.md
---

---
src: ./pages/UI Library Migrations/Recoil.md
---

---

# ではどうすればいいのか

フロントエンドエンジニアは外部要因によって何年も移行作業をする必要が出てくる可能性がある。

全てを.vueファイルで記述していると、Vue.jsがEOLしたときに会社のフロントエンド資産を全て移行する必要が出てくる。

## MVVMパターンの導入

- UIライブラリにはUIのレンダリングだけさせる
- 状態管理ライブラリは使わず、単純なTypeScriptのクラスに置き換える

---
layout: two-cols-header
---

# MVVMパターン

![MVVM Pattern](./pages/MVVM/.images/mvvm-pattern.png)

::left::
<div class="pr-4">

## View

見た目やレイアウトのロジック。

モダンなフロントエンド開発では、ReactやVueのコンポーネントを使ってUIレンダリングを行うことが多い。

</div>

::right::
<div class="pl-4">

## ViewModel

Viewをコントロールするロジック。

Viewに変更を通知したり、Viewからユーザーの入力を受け取ったりする。

純粋なTypeScriptのクラスとして実装可能。

</div>

## 重要な点

フロントエンド上で、UIに関するロジック(View)と、それ以外のロジック(ViewModel)に分離すること。

ViewModelはTypeScriptのクラスとして記述する。

---

