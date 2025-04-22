---
layout: section
transition: slide-left
---

# Why is it tough?

---

# Because we depend too much on UI frameworks

<div class="py-4"></div>

UI frameworks and state management libraries have a huge impact everywhere.

- ❌ Best practices change with every trend
- ❌ Major version upgrades bring lots of breaking changes
- ❌ When a library reaches EOL, you have to consider migration
- ❌ Special tools locked to the framework are needed for testing
- ❌ **Ad-hoc fixes become the norm, and general methodologies don't accumulate**

---
layout: section
---

# Real-world tragedies caused by dependencies

---

## Example: AngularJS EOL

<div class="py-4"></div>

A frontend framework developed by Google. Started in 2010, became popular, then reached EOL. The successor Angular is almost incompatible, requiring full rewrite from scratch.

---

## Migration effort required

<div class="py-4"></div>

| Company/Project | Migration Period | Destination | Notes | Reference |
|-----------------|-----------------|-------------|-------|-----------|
| Holistics | 4 months | Vue.js | | [Blog](https://www.holistics.io/blog/why-and-how-we-migrated-from-angularjs-to-vuejs/) |
| ANDPAD | 8 months | NuxtJS | | [Blog](https://tech.andpad.co.jp/entry/2021/03/11/170000) |
| Nulab TypeTalk | 62 person-months | Angular | | [Blog](https://nulab.com/ja/blog/typetalk/things-we-did-on-angular-migration/) |
| Personal Project | 2.5 years | Angular | 100k LOC, 1700 files | [Medium](https://medium.com/@rochat.aurelia/from-angularjs-to-angular-a-2-years-journey-5afcebe050a8) |
| ButterCMS | 3 years | React | | [Blog](https://buttercms.com/blog/migrating-from-angularjs-to-react/) |

---

## Example: Recoil archived

<div class="py-4"></div>

Meta's experimental state management library. In Jan 2025, became unmaintained due to Meta's restructuring. State management libraries tend to be used throughout the codebase, so rewrites are often needed in many places.

---

## Was that rewrite really necessary?

<div class="py-4"></div>

- Was it because the whole project depended on the UI framework?
- Do we really need state management libraries?
- Don't want to be at the mercy of external dependencies anymore!

<div class="py-4"></div>

<div class="text-2xl"> If we organize our dependencies, we can solve this! </div>
