---
layout: section
transition: slide-left
---

# Model-View-ViewModel (MVVM) Pattern

---

## Model-View-ViewModel (MVVM) Pattern

<div class="pt-4" />

<Transform :scale="0.8">
  <img src="./.images/mvvm-vertical.png" />
</Transform>

---
layout: two-cols-header
---

## Rewriting the previous example using ViewModel

<div class="pt-2" />

<div class="grid grid-cols-2 gap-4">

<div>
```vue
<template>
  <div>
    <p>Count: {{ counter.count }}</p>
    <button @click="counter.increment">Count Up</button>
  </div>
</template>

<script setup lang="ts">
import { reactive } from 'vue'

const vm = reactive(new CounterViewModel())
</script>
```
</div>

<div>
<h4>View</h4>

- Define UI in the `template` tag
- Use properties provided by the ViewModel
- Use functions provided by the ViewModel

</div>

<div>
```ts
class CounterViewModel {
  public count: number = 0
  
  increment(): void {
    this.count++
  }
}
```
</div>

<div>

<h4>ViewModel</h4>

- Define properties
- Define functions

<small>
*Note: If you don't remove dependency on Vue's `reactive` function, it's not completely UI-independent.*
</small>

</div>

</div>
