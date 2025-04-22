---
layout: section
transition: slide-left
---

# Appendix

---
layout: section
---

# Boundary between View and ViewModel

---
layout: two-cols-header
---

## Where does View end and ViewModel begin?

**It's easy to understand if you think "View describes How, ViewModel describes What".**

For example, a ViewModel can handle i18n keys, but it shouldn't handle how those keys are translated.

::left::

```ts {*|10-11|*}
class ProfileViewModel {
  errorKey: I18nKey = ""

  // ...

  getUser(){
    try {
      const user = this.userService.getUser()
    } catch (error) {
      // 👇 ViewModel handles "what to convey"
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
    <!-- 👇 View handles "how to display" -->
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

# Keep Views Small and Simple

---
layout: two-cols-header
---

## When in doubt, put it in the ViewModel

Write logic in the ViewModel for portability and testability. This also keeps the View simple.

For example, you don't need to create many property-specific components just for validation.

::left::

```vue {*|3-4,8|3-11}
<template>
  <form>
    <!-- 👇 Use different components for validation -->
    <first-name
      :model-value="firstName"
      @update:model-value="firstName = $event"
    />
    <last-name
      :model-value="lastName"
      @update:model-value="lastName = $event"
    />
  </form>
</template>

<script setup lang="ts">
const firstName = ref<string>('')
const lastName = ref<string>('')
</script>
```

::right::

<div class="pl-4">

```vue {*|3-4,9|3-13}
<template>
  <form>
    <!-- 👇 Same component is fine -->
    <text-input 
      :model-value="vm.firstName"
      @update:model-value="vm.setFirstName($event)"
      error-messages="vm.errorMessages.get('firstName')"
    />
    <text-input 
      :model-value="vm.lastName"
      @update:model-value="vm.setLastName($event)"
      error-messages="vm.errorMessages.get('lastName')"
    />
  </form>
</template>

<script setup lang="ts">
const vm = reactive(new PersonalInfoViewModel())
</script>
```

</div>

---
layout: section
---

# Ideas for Writing ViewModels

---
layout: two-cols-header
zoom: 0.8
---

## `isBusy` Property

A property to indicate whether it's okay to access ViewModel values.

When the ViewModel is communicating with a server or doing heavy processing, the View can know about it.

Also useful to avoid `undefined`-typed properties.

::left::

```ts {*|12-20|3-5|*}
@injectable()
class ProfileViewModel implements IProfileViewModel {
  isBusy: boolean = false
  name: string = ''
  age: number = 0  // 👈 No need for `undefined` type

  constructor(
    @inject(TYPES.UserService)
    private readonly userService
  ){}

  async load() {
    this.isBusy = true  // 👈 Initializing

    const user = await this.userService.getUser()
    this.name = user.name
    this.age = user.age

    this.isBusy = false  // 👈 Initialization complete
  }
}
```

::right::

<div class="pl-4">

```vue {*|18-20|4-7|*}
<template>
  <div>
    <h2>Profile</h2>
    <div v-if="!vm.isBusy">
      <p>Name: {{ vm.name }}</p>
      <p>Age: {{ vm.age }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
// import statements omitted

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

## Using `EventAggregator`

You can loosely couple ViewModels by communicating via Pub/Sub.

For example, it's easier to maintain if error handling is separated into another ViewModel.

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

## Eliminating the `reactive` Function

You can eliminate the dependency on the `reactive` function by using RxJS's `BehaviorSubject`.

Communication from ViewModel to View becomes UI-library independent.

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

## References

- [Model-View-ViewModel (MVVM) | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/architecture/maui/mvvm)
- [Screaming Architecture | The Clean Code Blog](https://blog.cleancoder.com/uncle-bob/2011/09/30/Screaming-Architecture.html)
