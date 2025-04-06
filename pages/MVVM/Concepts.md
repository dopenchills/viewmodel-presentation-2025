# MVVMパターンの導入

- UIライブラリにはUIのレンダリングだけさせる
- 状態管理ライブラリは使わず、単純なTypeScriptのクラスに置き換える

---
layout: two-cols-header
class: text-sm
---

# MVVMパターン

![MVVM Pattern](./.images/mvvm-pattern.png)

::left::
<div class="pr-4">

## View

見た目やレイアウトのロジック。

ReactやVueを使ってUIレンダリングを行う。

ViewModelに依存する。

</div>

::right::
<div class="pl-4">

## ViewModel

Viewをコントロールするロジック。

Viewに変更を通知したり、Viewからユーザーの入力を受け取ったりする。

純粋なTypeScriptのクラスとして実装可能。

Viewに依存しない。

</div>

---

# 重要な点

UIに関するロジックと、それ以外のロジックを分離すること。

| ロジックの種類 | レイヤー | 実装方法 |
|-------------|------------|---------|
| UIに関するロジック | View | UIライブラリ (React, Vue, etc.) |
| それ以外のロジック | ViewModel | 純粋なTypeScript |

