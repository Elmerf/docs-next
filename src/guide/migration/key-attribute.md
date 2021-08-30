---
badges:
  - breaking
---

# Atribut `key` <MigrationBadges :badges="$frontmatter.badges" />

## Gambaran Umum

- **BARU:** `key` sekarang tidak lagi diperlukan pada cabang `v-if`/`v-else`/`v-else-if`, karena Vue sekarang secara otomatis menghasilkan `key` unik.
 - **BREAKING:** Jika Anda secara manual memberikan `key`, sehingga tiap cabang harus memiliki `key` yang unik. Anda tidak lagi dengan sengaja menggunakan `key` yang sama untuk memaksa penggunaan ulang cabang.
- **BREAKING:** `<template v-for>` `key` harus diletakkan pada tag `<template>` (daripada pada tag anak).

## Latar Belakang

Atribut `key` spesial digunakan sebagai petunjuk oleh algoritma Vue's virtual DOM untuk tetap memperhatikan identitas dari node's. Dengan cara itu, Vue mengetahui kapan dapat menggunakan ulang dan memperbaiki node-node yang ada dan kapan membutuhkan untuk menata ulang atau membuat ulang node. Untuk informasi lebih lanjut, baca bagian berikut:

- [Menampilkan Daftar: Menjaga State](/guide/list.html#maintaining-state)
- [Referensi API: Atribut `key` Spesial](/api/special-attributes.html#key)

## Pada Cabang Kondisional

Pada Vue 2.x, direkomendasikan untuk memakai `key`s pada cabang `v-if`/`v-else`/`v-else-if`.

```html
<!-- Vue 2.x -->
<div v-if="condition" key="yes">Yes</div>
<div v-else key="no">No</div>
```

Contoh diatas tetap bekerja di Vue 3.x. Namun, Kami tidak lagi merekomendasikan untuk memakai atribut `key` pada cabang `v-if`/`v-else`/`v-else-if`, karena `key` unik telah dibuat secara otomatis pada cabang kondisional jika Anda tidak memberikan atribut `key`.

```html
<!-- Vue 3.x -->
<div v-if="condition">Yes</div>
<div v-else>No</div>
```

Perubahan yang berbeda adalah Jika Anda Secara manual memberikan `key`, pada setiap cabang harus memiliki sebuah `key` unik. Pada banyak kasus, Anda dapat menghapus `key` tersebut.

```html
<!-- Vue 2.x -->
<div v-if="condition" key="a">Yes</div>
<div v-else key="a">No</div>

<!-- Vue 3.x (solusi direkomendasikan: hilangkan keys) -->
<div v-if="condition">Yes</div>
<div v-else>No</div>

<!-- Vue 3.x (solusi alternatif: yakinkan bahwa keys harus selalu unik) -->
<div v-if="condition" key="a">Yes</div>
<div v-else key="b">No</div>
```

## Dengan `<template v-for>`

Pada Vue 2.x, sebuah tag `<template>` tidak dapat memilki sebuah `key`. Sehingga, Anda dapat meletakkan `key` pada setiap anaknya.

```html
<!-- Vue 2.x -->
<template v-for="item in list">
  <div :key="'heading-' + item.id">...</div>
  <span :key="'content-' + item.id">...</span>
</template>
```

Pada Vue 3.x, `key` tersebut dapat diletakkan pada tag `<template>`.

```html
<!-- Vue 3.x -->
<template v-for="item in list" :key="item.id">
  <div>...</div>
  <span>...</span>
</template>
```

Dengan mirip, ketika menggunakan `<template v-for>` dengan anaknya yang menggunakan `v-if`, `key` harus dinaikkan ke dalam tag `<template>`.

```html
<!-- Vue 2.x -->
<template v-for="item in list">
  <div v-if="item.isVisible" :key="item.id">...</div>
  <span v-else :key="item.id">...</span>
</template>

<!-- Vue 3.x -->
<template v-for="item in list" :key="item.id">
  <div v-if="item.isVisible">...</div>
  <span v-else>...</span>
</template>
```
