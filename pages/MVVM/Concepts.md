# MVVMパターンの導入

- UIライブラリにはUIのレンダリングだけさせる
- 状態管理ライブラリは使わず、単純なTypeScriptのクラスに置き換える

---
layout: two-cols-header
---

# MVVMパターン

![MVVM Pattern](./.images/mvvm-pattern.png)

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
