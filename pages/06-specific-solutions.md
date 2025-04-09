---
layout: section
transition: slide-left
---

# 実践
# 例: カウンターアプリ

---

<!-- Vue.jsのReactivity Fundamentalsでも使われている例 -->

## シンプルなカウンターアプリの例

<div class="py-4"></div>

要件

- カウントの初期状態は0
- カウントアップボタンを押すと、カウントを1増やす

---
layout: two-cols-header
---


## Vue.jsでの実装

::left::


```vue {*|11-15|*}
<template>
  <div>
    <p>カウント: {{ count }}</p>
    <button @click="increment">カウントアップ</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>
```

::right::

<div class="pl-4">

核となるロジックが切り出せていないので...

- ❌ コードベース全体がVueのバージョンアップグレード・EOLに影響を受ける
- ❌　テストにVue Test Utilsが必要

</div>


---
layout: two-cols-header
---

## 核となるロジックを切り出す

::left::

<div class="pt-4">

```vue {*|11}
<template>
  <div>
    <p>カウント: {{ counter.count }}</p>
    <button @click="counter.increment">カウントアップ</button>
  </div>
</template>

<script setup lang="ts">
import { reactive } from 'vue'

const counter = reactive(new Counter())
</script>
```

</div>

<div class="pt-2">

```ts
class Counter {
  public count: number = 0
  
  increment(): void {
    this.count++
  }
}
```

</div>

::right::

<div class="pl-4">

核となるロジックが切り出せたので

- ⭕️ UIフレームワークから核となるロジックを守れた
- ⭕️ 単体テスト可能


</div>

---

## カウンターアプリをさらに複雑にする: バリデーション

---

## カウンターアプリをさらに複雑にする: Dependency Injection

---

## さらにDIを活用しよう

---

## 単純なプログラミングの問題となる