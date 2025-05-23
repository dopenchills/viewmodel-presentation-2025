---
layout: section
transition: slide-left
---

# 実践
# 例: カウンターアプリ


<!-- 
  Vue.jsのReactivity Fundamentalsでも使われている例
  
  Vueを使うこと自体が間違っているかのように聞こえるかもしれない。
  「ここで登場する例は単純化しているし、Vueを使って保守可能なものを目指すという考えもある」
  「あくまでもポイントはUIフレームワークや状態管理ライブラリへの過度な依存をなくすこと」
  というような補足を入れてもいいかもしれない
-->

---


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
- ❌ テストにVue Test Utilsが必要

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

- ⭕️ UIフレームワーク非依存
- ⭕️ 移植可能
- ⭕️ 単体テスト可能

</div>

---
layout: two-cols-header
---

## Reactへの移植の例

<div class="pt-4"></div>

核となるロジックをReactでも再利用できる

<div class="text-xs">

Reactの場合、再レンダリングの仕組みは必要 / 
このような実装差異をRxで回避する方法はAppendixに記載

</div>

::left::

<p class="font-bold text-center">核となるロジック</p>


<!-- TODO: 本当に動くか検証 -->

```ts
import { makeAutoObservable } from "mobx";

class Counter {
  public count: number = 0

  constructor(){
    // 👇 リアクティブな再レンダリングのためにMobXを利用
    makeAutoObservable(this);
  }

  increment(): void {
    this.count++
  }
}
```

::right::

<div class="pl-4">

<p class="font-bold text-center">Reactで実装したUI</p>

```tsx
import { observer } from "mobx-react-lite";

const counter = new Counter();

export const SimpleCounter = observer(() => (
  <div>
    <p>カウント: {counter.count}</p>
    <button onClick={() => counter.increment()}>
      カウントアップ
    </button>
  </div>
));
```

</div>

---

## テストの比較

<div class="pt-4"></div>

<div class="grid grid-cols-2 gap-8">

<div>

<h4>❌ Vue.jsのみで実装した場合</h4>

<div class="pt-4"></div>


```ts {*|5|7|1|*}
import { mount } from '@vue/test-utils'
import SimpleCounter from './SimpleCounter.vue'

it('should increment when clicking button', async () => {
  const wrapper = mount(SimpleCounter)
  
  await wrapper.find('button').trigger('click')
  
  expect(wrapper.text()).toContain('カウント: 1')
})
```

</div>

<div>

<h4>⭕️ 核となるロジックを切り出した場合</h4>

<div class="pt-4"></div>

```ts
import { Counter } from './counter'

it('should increment', () => {
  const counter = new Counter()
  
  counter.increment()
  
  expect(counter.count).toBe(1)
})
```

</div>

<div>

<ul>
<li>❌ コンポーネントのマウントが必要</li>
<li>❌ ボタンの数など、UI起因で壊れる</li>
<li>❌ UIフレームワークに依存している</li>
</ul>

</div>

<div>

<ul>
<li>⭕️ 単体テストができる</li>
<li>⭕️ UI起因で壊れない</li>
<li>⭕️ UIフレームワーク非依存</li>
</ul>

</div>

</div>


---
layout: section
transition: slide-left
---

# バリデーション

---

## カウンターアプリをさらに複雑にする: バリデーション

<div class="py-4"></div>

要件

- カウントの最大値は100
- 最大値を超えてカウントアップできない
- カウントアップできないことが一目で分かるようにする

---
layout: two-cols-header
---

## Vue.jsでのバリデーション実装

::left::

```vue {*|12-22|*}{ maxHeight:'350px' }
<template>
  <div>
    <p>カウント: {{ count }}</p>
    <button @click="increment" :disabled="!canIncrement">
      カウントアップ
    </button>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

const countMax = 100
const count = ref(0)

const canIncrement = computed(() => count.value + 1 <= countMax)

function increment() {
  if (canIncrement.value) {
    count.value++
  }
}
</script>
```

::right::

<div class="pl-4">

バリデーションロジックがUIライブラリに依存してしまっているので...

- ❌ バリデーションロジックを移植できない
- ❌ テストにVue Test Utilsが必要になる

</div>

---
layout: two-cols-header
---

## バリデーションロジックを切り出す

::left::

<div class="pt-2">

```vue {*|4-9|16}{maxHeight: '150px'}
<template>
  <div>
    <p>カウント: {{ counter.count }}</p>
    <button
      @click="counter.increment"
      :disabled="!counter.canIncrement"
    >
      カウントアップ
    </button>
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
  public readonly countMax: number = 100
  
  get canIncrement(): boolean {
    return this.count + 1 <= this.countMax
  }

  increment(): void {
    if (this.canIncrement) {
      this.count++
    }
  }
}
```

</div>

::right::

<div class="pl-4">

バリデーションロジックを切り出せたので

- ⭕️ UIフレームワーク非依存
- ⭕️ 移植可能
- ⭕️ 単体テスト可能

</div>

---
layout: section
transition: slide-left
---

# Dependency Injection

---

## カウンターアプリをさらに複雑にする: Dependency Injection

<div class="py-4"></div>

要件

- アプリを再起動しても、同じカウントを表示したい (カウントを永続化したい)
  
※ バリデーションの要件はここでは考えない

---
layout: two-cols-header
---

## Vue.jsでの実装

::left::

```vue {*|13-29}{maxHeight:'400px'}
<template>
  <div>
    <p>カウント: {{ count }}</p>
    <button @click="increment">
      カウントアップ
    </button>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

const count = ref(0)

onMounted(async () => {
  try {
    const response = await fetch('/api/counter')
    const data = await response.json()
    count.value = data.count
  } catch (e) { /* エラーハンドリング省略 */ }
})

async function increment() {
  count.value++
  await fetch('/api/counter', {
    method: 'POST',
    body: JSON.stringify({ count: count.value })
  })
}
</script>
```

::right::

<div class="pl-4">

- ❌ 核となるロジックを移植できない
- ❌ テストが難しい (APIコールのモックが必要)

</div>

---
layout: two-cols-header
---

## DIを使って永続化ロジックを分離する

::left::

<div class="pt-2">

```ts
interface ICounterService {
  get(): Promise<number>
  update(count: number): Promise<void>
}
```

</div>

<div class="pt-2">

```ts
class Counter {
  public count: number = 0
  
  constructor(
    private readonly counterService: ICounterService
  ) {}
  
  async increment(): Promise<void> {
    this.count++
    await this.counterService.update(this.count)
  }

  async load(): Promise<void> {
    this.count = await this.counterService.get()
  }
}
```

</div>

::right::


<div class="pl-4">

DIのメリットを享受できる

- ⭕️ Counterクラスはカウント処理に集中
- ⭕️ 永続化の方法は自由

今まで通り...

- ⭕️ UIフレームワーク非依存
- ⭕️ 移植可能
- ⭕️ 単体テスト可能

</div>

---

## 実装例

```vue {*|13-19}
<template>
  <div>
    <p>カウント: {{ counter.count }}</p>
    <button @click="counter.increment">
      カウントアップ
    </button>
  </div>
</template>

<script setup lang="ts">
import { reactive, onMounted } from 'vue'

// 👇 Serviceの実装は自由
const counterService = new LocalStorageCounterService()
const counter = reactive(new Counter(counterService))

onMounted(async () => {
  await counter.load()
})
</script>
```

---
layout: two-cols-header
---

## Tips: DIライブラリを活用する

::left::

```vue {*|13-16}
<template>
  <div>
    <p>カウント: {{ counter.count }}</p>
    <button @click="counter.increment">
      カウントアップ
    </button>
  </div>
</template>

<script setup lang="ts">
import { reactive, onMounted } from 'vue'

// 👇 diContainerが自動で依存を解決してくれる
const counter = reactive(
  new diContainer.get<ICounter>(TYPES.Counter)
)

onMounted(async () => {
  await counter.load()
})
</script>
```

::right::

<div class="pl-4">

⭕️ 複雑な依存の自動解決が可能に

</div>


---

## これで純粋にプログラミングと向き合える

<div class="py-4"></div>

核となるロジックを純粋なTypeScriptとして切り出せたので...

**純粋にプログラミングに向き合えるようになる:**

- 「ここはDIを使おう」
- 「ここは関数型の考えを使おう」
- 「ここはイベント駆動で考えよう」
- 「ここはステートマシーンで管理しよう」
- etc.

**要件をプログラミングで満たしていくことに集中すればいい**
