---
badges:
  - breaking
---

# API Fungsi Render <MigrationBadges :badges="$frontmatter.badges" />

## Gambaran Umum

Perubahan ini tidak berdampak pada pengguna `<template>`.

Berikut adalah ringkasan sekilas apa yang telah berubah:

- `h` sekarang terimpor secara global daripada dilewatkan ke fungsi render sebagai argumen
- argumen fungsi render diubah menjadi lebih konsisten antara komponen-komponen stateful dan functional
- VNodes sekarang mempunyai struktur flat props

Untuk informasi lebih lanjut, baca seterusnya!

## Argumen Fungsi Render

### 2.x Sintaksis

Pada 2.x, fungsi `render` akan secara otomatis  menerima fungsi `h` (yang merupakan sebuah alias konvensional untuk `createElement`) sebagai sebuah argumen:

```js
// Contoh Vue 2 Fungsi Render
export default {
  render(h) {
    return h('div')
  }
}
```

### 3.x Sintaksis

Pada 3.x, `h` sekarang secara global diimpor daripada secara otomatis dilewatkan sebagai sebuah argumen.

```js
// Contoh Vue 3 Fungsi Render
import { h } from 'vue'

export default {
  render() {
    return h('div')
  }
}
```

## Perubahaan Signature Fungsi Render

### 2.x Sintaksis

Pada 2.x, fungsi `render` secara otomatis menerima argumen seperti `h`.

```js
// Contoh Vue 2 Fungsi Render
export default {
  render(h) {
    return h('div')
  }
}
```

### 3.x Sintaksis

Pada 3.x, dikarenakan fungsi `render` tidak lagi menerima argumen apapun, Fungsi ini akan secara primer digunakan di dalam fungsi `setup()`. Hal ini akan memberikan keuntungan dalam mendapatkan akses ke keadaan reaktif dan fungsi-fungsi dideklarasikan di scope, sama baiknya argumen dilewatkan ke `setup()`.

```js
import { h, reactive } from 'vue'

export default {
  setup(props, { slots, attrs, emit }) {
    const state = reactive({
      count: 0
    })

    function increment() {
      state.count++
    }

    // return fungsi render
    return () =>
      h(
        'div',
        {
          onClick: increment
        },
        state.count
      )
  }
}
```

Untuk informasi mengenai bagaimana `setup()` bekerja, baca [Paduan Composition API](/guide/composition-api-introduction.html).

## Format VNode Props

### 2.x Sintaksis

Pada 2.x, `domProps` berisikan sebuah daftar bersarang di dalam VNode props:

```js
// 2.x
{
  staticClass: 'button',
  class: {'is-outlined': isOutlined },
  staticStyle: { color: '#34495E' },
  style: { backgroundColor: buttonColor },
  attrs: { id: 'submit' },
  domProps: { innerHTML: '' },
  on: { click: submitForm },
  key: 'submit-button'
}
```

### 3.x Sintaksis

Pada 3.x, semua struktru VNode props telah diratakan. Menggunakan contoh di atas, berikut ini bagaimana sintaksis tampak.

```js
// 3.x Sintaksis
{
  class: ['button', { 'is-outlined': isOutlined }],
  style: [{ color: '#34495E' }, { backgroundColor: buttonColor }],
  id: 'submit',
  innerHTML: '',
  onClick: submitForm,
  key: 'submit-button'
}
```

## Komponen Teregistrasi

### 2.x Sintaksis

Pada 2.x, ketika sebuah komponen telah teregistrasi, fungsi render akan bekerja dengan baik ketika melewatkan nama komponen sebagai sebuah string ke argumen pertama:

```js
// 2.x
Vue.component('button-counter', {
  data() {
    return {
      count: 0
    }
  }
  template: `
    <button @click="count++">
      Clicked {{ count }} times.
    </button>
  `
})

export default {
  render(h) {
    return h('button-counter')
  }
}
```

### 3.x Sintaksis

Pada 3.x, dengan VNodes menjadi bebas konteks, kita tidak lagi dapat menggunakan sebuah ID string untuk secara implisit mencari komponen-komponen yang teregistrasi. Dengan demikian, kita memerlukan sebuah method `resolveComponent` yang diimpor:

```js
// 3.x
import { h, resolveComponent } from 'vue'

export default {
  setup() {
    const ButtonCounter = resolveComponent('button-counter')
    return () => h(ButtonCounter)
  }
}
```

Untuk informasi lebih lanjut, lihat [Api Fungsi Render mengubah RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0008-render-function-api-change.md#context-free-vnodes).

## Strategi Migrasi

[Migration build flag: `RENDER_FUNCTION`](migration-build.html#compat-configuration)

### Pencipta Library

`h` menjadi secara global diimpor berarti library apa saja yang berisi komponen-komponen Vue akan memasukkan `import { h } from 'vue'` di suatu tempat. Hasilnya, hal ini membuat sedikit pengaturan tambahan dikarenakan memerlukan pencipta library untuk secara benar mengkonfigurasikan eksternalisasi dari Vue di build setup mereka:

- Vue tidak boleh dibundel ke dalam library
- Untuk module builds, import harus dibiarkan sendiri dan ditangani oleh bundler pengguna akhir
- Untuk UMD / browser builds, harus mencoba global Vue.h dahulu dan fallback memerlukan calls

## Langkah Berikutnya

Lihat [Panduan Fungsi Render](/guide/render-function) untuk dokumentasi secara lebih detail!
