---
badges:
  - breaking
---

# Atribut Templat Inline <MigrationBadges :badges="$frontmatter.badges" />

## Gambaran Umum

Dukungan untuk [fitur inline-template](https://vuejs.org/v2/guide/components-edge-cases.html#Inline-Templates) telah dihapuskan.

## 2.x Sintaksis

Di 2.x, Vue menyediakan atribut `inline-template` pada komponen anak/turunan. Komponen turunan ini menggunakan konten didalamnya sebagai templat komponen daripada memperlakukannya sebagai konten terdistribusi.

```html
<my-component inline-template>
  <div>
    <p>These are compiled as the component's own template.</p>
    <p>Not parent's transclusion content.</p>
  </div>
</my-component>
```

## 3.x Sintaksis

Fitur ini tidak lagi didukung

## Strategi dalam Migrasi

Banyak penggunaan `inline-template` diasumsikan menggunakan sebuah no-build-tool, dimana semua templat ditulis langsung ke dalam halaman HTML.

[Migration build flag: `COMPILER_INLINE_TEMPLATE`](migration-build.html#compat-configuration)

### Pilihan #1: menggunakan tag `<script>`

Cara paling mudah adalah dalam kasus ini adalah menggunakan `<script>` dengan tipe alternatif:

```html
<script type="text/html" id="my-comp-template">
  <div>{{ hello }}</div>
</script>
```

Dan dalam komponen, target templat tersebut menggunakan sebuah selector:

```js
const MyComp = {
  template: '#my-comp-template'
  // ...
}
```

Opsi ini tidak memerlukan suatu build setup, bekerja di semua peramban, bukan subjek dari suatu in-DOM HTML parsing caveats (contoh Anda dapat menggunakan camelCase pada penamaan prop), dan memberikan sorotan sintaksis yang benar pada kebanyakan IDEs. Pada kerangka kerja sisi-peladen tradisional, template ini dapat dipisah menjadi partisi templat peladen (termasuk templat HTML utama) agar mempermudah dalam pemeliharaan.

### Pilihan #2: Default Slot

Komponen yang sebelumnya menggunakan `inline-template` juga dapat direfaktor menggunakan default slot - Yang mana membuat jangkauan data menjadi lebih jelas sementara menjaga kemudahanan dalam menulis konten anak secara inline:

```html
<!-- 2.x Syntax -->
<my-comp inline-template :msg="parentMsg">
  {{ msg }} {{ childState }}
</my-comp>

<!-- Default Slot Version -->
<my-comp v-slot="{ childState }">
  {{ parentMsg }} {{ childState }}
</my-comp>
```

Anak tersebut, daripada menyediakan templat kosong, sekarang seharusnya menampilkan default slot\*:

```html
<!--
  in child template, render default slot while passing
  in necessary private state of child.
-->
<template>
  <slot :childState="childState" />
</template>
```

> - Catatan: di 3.x, slots juga dapat ditampilkan sebagai root bersamaan dengan dukungan native [fragments](/guide/migration/fragments)!
