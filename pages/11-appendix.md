---
layout: section
transition: slide-left
---

# Appendix

---
layout: section
---

# ViewとViewModelの境目

---
layout: two-cols-header
---

## どこまでがViewで、どこまでがViewModelか

**「ViewはHow, ViewModelはWhatを記述する」と考えると分かりやすい**。

例えばViewModelはi18nのキーを扱うことができるが、そのキーをどのように翻訳するかは扱うべきではない。

::left::

```ts {*|10-11|*}
class ProfileViewModel {
  errorKey: I18nKey = ""

  // 中略

  getUser(){
    try {
      const user = this.userService.getUser()
    } catch (error) {
      // 👇 ViewModelでは"何を伝えたいか"を扱う
      this.errorKey = "Errors.Api.Unexpected"
    }
  }
}
```

::right::

<div class="pl-4">
```vue {*|3-6,14|*}
<template>
  <div>
    <!-- 👇 Viewでは"どう見せるか"を扱う -->
    <div v-if="vm.errorKey" class="error-message">
      {{ $t(vm.errorKey) }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { useI18n } from 'vue-i18n'
import { reactive } from 'vue'

const { t } = useI18n()
const vm = reactive(new ProfileViewModel())
</script>
```
</div>


---
layout: section
---

# ViewModelの書き方の
# アイディア

---
layout: two-cols-header
zoom: 0.8
---

## `isBusy` プロパティ

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

<div class="pl-4">

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

</div>

---
layout: two-cols-header
zoom: 0.8
---

## `EventAggregator`の利用

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

<div class="pl-4">

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

</div>

---
layout: two-cols-header
zoom: 0.8
---

## `reactive`関数の排除

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

<div class="pl-4">

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
</div>

---

## 関連資料

- [Model-View-ViewModel (MVVM) | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/architecture/maui/mvvm)
- [Screaming Architecture](https://blog.cleancoder.com/uncle-bob/2011/09/30/Screaming-Architecture.html)
