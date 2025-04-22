---
layout: section
transition: slide-left
---

# Practice
# Example: Counter App

---

## Simple Counter App Example

<div class="py-4"></div>

Requirements:

- Initial count is 0
- Pressing the count up button increases the count by 1

---
layout: two-cols-header
---

## Implementation in Vue.js

::left::

```vue {*|11-15|*}
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="increment">Count Up</button>
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

The core logic is not separated, so...

- ❌ The whole codebase is affected by Vue version upgrades/EOL
- ❌ Vue Test Utils is required for testing

</div>

---
layout: two-cols-header
---

## Extracting the core logic

::left::

<div class="pt-4">

```vue {*|11}
<template>
  <div>
    <p>Count: {{ counter.count }}</p>
    <button @click="counter.increment">Count Up</button>
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

Now that the core logic is separated:

- ✅ UI framework independent
- ✅ Portable
- ✅ Unit testable

</div>

---
layout: two-cols-header
---

## Example of migration to React

<div class="pt-4"></div>

The core logic can be reused in React as well.

<div class="text-xs">

In React, you need a mechanism for re-rendering. See the Appendix for how to avoid such implementation differences with reactive programming.

</div>

::left::

<p class="font-bold text-center">Core logic</p>

```ts
import { makeAutoObservable } from "mobx";

class Counter {
  public count: number = 0

  constructor(){
    // 👇 Use MobX for reactive rendering
    makeAutoObservable(this);
  }

  increment(): void {
    this.count++
  }
}
```

::right::

<div class="pl-4">

<p class="font-bold text-center">UI implemented in React</p>

```tsx
import { observer } from "mobx-react-lite";

const counter = new Counter();

export const SimpleCounter = observer(() => (
  <div>
    <p>Count: {counter.count}</p>
    <button onClick={() => counter.increment()}>
      Count Up
    </button>
  </div>
));
```

</div>

---

## Test comparison

<div class="pt-4"></div>

<div class="grid grid-cols-2 gap-8">

<div>

<h4>❌ Vue.js only implementation</h4>

<div class="pt-4"></div>

```ts {*|5|7|1|*}
import { mount } from '@vue/test-utils'
import SimpleCounter from './SimpleCounter.vue'

it('should increment when clicking button', async () => {
  const wrapper = mount(SimpleCounter)
  
  await wrapper.find('button').trigger('click')
  
  expect(wrapper.text()).toContain('Count: 1')
})
```

</div>

<div>

<h4>✅ Core logic extracted</h4>

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
<li>❌ Needs component mounting</li>
<li>❌ Can break due to UI triggers</li>
<li>❌ Depends on UI framework</li>
</ul>
</div>

<div>
<ul>
<li>✅ Unit testable</li>
<li>✅ Not broken by UI triggers</li>
<li>✅ UI framework independent</li>
</ul>
</div>

</div>

---
layout: section
transition: slide-left
---

# Validation

---

## More complex counter app: Validation

<div class="py-4"></div>

Requirements:

- Max count is 100
- Cannot increment beyond max
- Should be obvious when increment is not possible

---
layout: two-cols-header
---

## Validation logic in Vue.js

::left::

```vue {*|12-22|*}{ maxHeight:'350px' }
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="increment" :disabled="!canIncrement">
      Count Up
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

Validation logic is tied to the UI library...

- ❌ Cannot migrate validation logic
- ❌ Vue Test Utils required for testing

</div>

---
layout: two-cols-header
---

## Extracting validation logic

::left::

<div class="pt-2">

```vue {*|4-9|16}{maxHeight: '150px'}
<template>
  <div>
    <p>Count: {{ counter.count }}</p>
    <button
      @click="counter.increment"
      :disabled="!counter.canIncrement"
    >
      Count Up
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

Now validation logic is separated:

- ✅ UI framework independent
- ✅ Portable
- ✅ Unit testable

</div>

---
layout: section
transition: slide-left
---

# Dependency Injection

---

## More complex counter app: Dependency Injection

<div class="py-4"></div>

Requirements:

- After restarting the app, the same count should be shown (persist count)

---
layout: two-cols-header
---

## Implementation in Vue.js

::left::

```vue {*|13-29}{maxHeight:'400px'}
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="increment">
      Count Up
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
  } catch (e) { /* error handling omitted */ }
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

- ❌ Cannot migrate core logic
- ❌ Testing is hard (need to mock API calls)

</div>

---
layout: two-cols-header
---

## Using DI to separate persistence logic

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

Benefits of DI:

- ✅ Counter class focuses on count logic
- ✅ Persistence method is flexible

As before...

- ✅ UI framework independent
- ✅ Portable
- ✅ Unit testable

</div>

---

## Example implementation

```vue {*|13-19}
<template>
  <div>
    <p>Count: {{ counter.count }}</p>
    <button @click="counter.increment">
      Count Up
    </button>
  </div>
</template>

<script setup lang="ts">
import { reactive, onMounted } from 'vue'

// Service implementation is up to you
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

## Tips: Use DI libraries

::left::

```vue {*|13-16}
<template>
  <div>
    <p>Count: {{ counter.count }}</p>
    <button @click="counter.increment">
      Count Up
    </button>
  </div>
</template>

<script setup lang="ts">
import { reactive, onMounted } from 'vue'

// diContainer automatically resolves dependencies
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

✅ Can automatically resolve complex dependencies

</div>

---

## Now you can focus on pure programming

<div class="py-4"></div>

By extracting the core logic as pure TypeScript...

**You can focus on pure programming:**

- "Let's use DI here"
- "Let's use functional programming here"
- "Let's use event-driven here"
- "Let's use state machines here"
- etc.

**Just focus on fulfilling requirements with programming**
