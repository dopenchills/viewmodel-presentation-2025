---
layout: section
---

## ViewModel実装のTips

---
layout: two-cols-header
zoom: 0.8
---

# Tips: `isBusy` プロパティ

ViewModelの値にアクセスしてもいいかを示すプロパティ

ViewModelがサーバーと通信していたり、重たい処理をしている際に、Viewがそれを知ることができる。

`undefined`型のプロパティを避ける際にも使用できる。

::left::

```ts {*|12-20|3-5|*}
@injectable()
class ProfileViewModel implements IProfileViewModel {
  isBusy: boolean = false
  name: string = ''
  age: number = 0  // 👈 `undefined`型を使わなくていい

  constructor(
    @inject(TYPES.UserService)
    private readonly userService
  ){}

  async load() {
    this.isBusy = true  // 👈 初期化中

    const user = await this.userService.getUser()
    this.name = user.name
    this.age = user.age

    this.isBusy = false  // 👈 初期化完了
  }
}
```

::right::

```vue {*|18-20|4-7|*}
<template>
  <div>
    <h2>プロフィール</h2>
    <div v-if="!vm.isBusy">
      <p>名前: {{ vm.name }}</p>
      <p>年齢: {{ vm.age }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
// インポート文は省略

const vm = reactive(
  diContainer.get<IProfileViewModel>(TYPES.ProfileViewModel)
)

onMounted(async () => {
  await vm.load()
})
</script>
```

---
layout: two-cols-header
zoom: 0.8
---

# Tips: `EventAggregator`の利用

ViewModel間の通信をPub/Subベースで行うとViewModel同士を疎結合にできる。

以下は、エラー処理は別のViewModelなどに分離した方が保守しやすいというアイディア

::left::

```ts {*|13-17|*}
@injectable()
class ViewModelThatThrowsError {
   
  constructor(
    @inject(TYPES.EventAggregator)
    ea: IEventAggregator
  ){}

  run() {
    try {
      logicThatThrows()
    } catch (error) {
      this.ea.publish(
        new ErrorThrownEvent(error)
      )
    }
  }
}
```

::right::

```ts {*|15-18|*}
@injectable()
class ViewModelThatLogsError {
  subscriptions: ISubscription[] = []

  constructor(
    @inject(TYPES.EventAggregator)
    ea: IEventAggregator,

    @inject(TYPES.TelemetryClient)
    tc: ITelemetryClient
  ){}

  async load() {
    this.subscriptions.push(
      const subscription = this.ea.subscribe(ErrorThrownEvent, (event) => {
        console.log(event.error.message)
        tc.trackException(event.error)
      })
    )
  }

  async unload() {
    this.subscriptions.forEach((s) => s.unsubscribe())
  }
}
```


---
layout: two-cols-header
zoom: 0.8
---

# Tips: `reactive`関数の排除

RxJSの`BehaviorSubject`を利用して、`reactive`関数を排除できる。

ViewModel → Viewの通信がUIライブラリ非依存になる。

::left::

```ts {*|3|*}
@injectable()
class CounterViewModel {
  count = new BehaviorSubject<number>(0)

  increment() { this.count.next(this.count + 1) }
  decrement() { this.count.next(this.count - 1) }
}
```

::right::

```vue {*|13-23|*}
<template>
  <div>{{ vm.count }}</div>
  <div>
    <button @click="vm.increment">Increment</button>
    <button @click="vm.decrement">Decrement</button>
  </div>
</template>

<script setup lang="ts">
const vm = new CounterViewModel()

const count = ref<number>(0)
let subscription

onMounted(() => {
  subscription = vm.count.subscribe(value => {
    this.count.value = value
  })
})

onUnmounted(() => {
  subscription.unsubscribe()
})
</script>
```
