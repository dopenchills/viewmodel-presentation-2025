# Vue.jsのみの実装

```vue
<template>
  <div>{{ count }}</div>
  <div>
    <button @click="increment">Increment</button>
    <button @click="decrement">Decrement</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}

function decrement() {
  count.value--
}
</script>
```

---
layout: two-cols-header
---

# 問題点

::left::

```vue {12-20}
<template>
  <div>{{ count }}</div>
  <div>
    <button @click="increment">Increment</button>
    <button @click="decrement">Decrement</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}

function decrement() {
  count.value--
}
</script>
```

::right::

- `script setup`内の再利用性
- `script setup`内のテスタビリティ
- UIライブラリへの不要な依存


---
layout: two-cols-header
---

# ViewModelを利用した場合

::left::

```vue
<template>
  <div>{{ vm.count }}</div>
  <div>
    <button @click="vm.increment">Increment</button>
    <button @click="vm.decrement">Decrement</button>
  </div>
</template>

<script setup lang="ts">
const vm = reactive(new CounterViewModel())
</script>
```

```ts
class CounterViewModel {
  count = 0

  increment() { this.count += 1 }
  decrement() { this.count -= 1 }
}
```

::right::

- ViewModelは再利用可能
- ViewModelは簡単に単体テスト可能
- ViewModelはUIライブラリ非依存
- ViewModelを使えば状態管理ライブラリ不要
- HTML/CSS/UIライブラリの知識不要
- 純粋なプログラミングで要件を満たしてくことに集中できる

※ Vue.jsの`reactive`関数を排除することで、よりUIライブラリ非依存にできる
