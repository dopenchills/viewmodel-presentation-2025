---
layout: section
---

## 純粋なプログラミングで問題を解決する例

---
layout: two-cols-header
zoom: 0.8
---

# Dependency Injection

Dependency Injectionを利用して疎結合なプログラミングが可能。

::left::

👇 ViewModelの実装

```ts
@injectable()
class ProfileViewModel implements IProfileViewModel {
  public name: string = ''
  public age: number | undefined = undefined

  constructor(
    @inject(TYPES.UserService)
    private readonly userService
  ){}

  async load() {
    const user = await this.userService.getUser()
    this.name = user.name
    this.age = user.age
  }
}
```

※ InversifyJSでDIを行っている


::right::

👇 Viewの実装

```vue
<template>
  <div>
    <h2>プロフィール</h2>
    <div>
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
