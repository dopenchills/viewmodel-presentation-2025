---
layout: two-cols-header
transition: slide-left
---

## まとめ


フロントエンドが辛い理由は、UIフレームワークや状態管理ライブラリへの必要以上の依存

解決策は核となるロジックと周辺のロジックを切り分けること。

移植性やテスタビリティが向上し、純粋にプログラミングに向き合えるようになる。

核となるロジックと周辺ロジックの切り分け方のアイディア:

- MVVMパターン
  - 核となるロジックをViewModelに置く
  - 周辺のロジックはViewに置く
- DIの利用
  - 周辺のロジックを切り分け、核となるロジックに注入する

---
layout: section
---

# 質疑応答

<div></div>

※ 時間に余裕があれば関連トピック & Appendixに触れたいです
