---
layout: section
---

## "Vue.jsのみの実装" vs "Vue.js + ViewModelの実装"

---

# Vue.jsのみの実装

カウンターアプリを実装すると以下のようになる:

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

# Vue.jsのみの実装の問題点

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

- `script setup`内の再利用性が低い
- `script setup`内のテスタビリティが低い
- UIライブラリへ不必要に依存している


---
layout: two-cols-header
zoom: 0.9
---

# ViewModelを利用した場合

::left::

👇 Viewの実装

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

👇 ViewModelの実装

```ts
class CounterViewModel {
  count = 0

  increment() { this.count += 1 }
  decrement() { this.count -= 1 }
}
```

::right::

ViewModel導入によるメリット

- 再利用可能
- 単体テスト可能
- UIライブラリ非依存
- 状態管理ライブラリが不要
- HTML/CSS/UIライブラリの知識が不要
- 純粋なプログラミングで要件を満たしてくことに集中できる

※ Vue.jsの`reactive`関数を排除することで、よりUIライブラリ非依存にできる
