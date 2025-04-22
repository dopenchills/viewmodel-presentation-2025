---
layout: section
transition: slide-left
---

# UI Independence of ViewModel

---
layout: two-cols-header
---

## WebUI is not the only View

::left::

Anything with the following features can be considered a View (UI):

- Can read input from the user
- Can present output to the user
- Can execute software

A ViewModel can be reused in these environments:

- WebUI
- CLI
- Voice interaction system
- Robot
- Anything with the above features

::right::

<img src="./.images/anything-is-ui.png" />

---

## Demo: ViewModel Reusability

<div class="pt-8" />

### ViewModel Reuse

- WebUI (Vue.js)
- Voice interaction system (OpenAI's Realtime API)

[GitHub repository](https://github.com/dopenchills/note-viewmodel-demo-202502)

<div class="pt-4" />

### Features

- Authentication
- Note creation & listing

<div class="pt-4" />

*Note: The demo requires an OpenAI API key. If you are demoing on a work PC, please confirm in advance if you are allowed to use an OpenAI API key.*
