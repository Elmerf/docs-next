---
title: Transition Group Root Element
badges:
  - breaking
---

# {{ $frontmatter.title }} <MigrationBadges :badges="$frontmatter.badges" />

## Gambaran Umum

`<transition-group>` tidak lagi merender sebuah element root secara default, tetapi masih dapat membuatnya menggunakan prop `tag`.

## 2.x Sintaksis

Pada Vue 2, `<transition-group>`, seperti komponen-komponen kustom lainnya, membutuhkan sebuah element root, dimana secara default adalah sebuah `<span>` tetapi dikostumisasi menggunakan prop `tag`.

```html
<transition-group tag="ul">
  <li v-for="item in items" :key="item">
    {{ item }}
  </li>
</transition-group>
```

## 3.x Sintaksis

Pada Vue 3, Kami mempunyai [fragment support](/guide/migration/fragments.html), sehingga komponen-komponen tidak lagi _membutuhkan_ sebuah node root. Sehingga, `<transition-group>` tidak lagi merendernya secara default.

- Jika Anda telah mempunyai prop `tag` terdefinisikan pada code Vue 2 Anda, seperti contoh diatas, semuanya akan bekerja normal seperti sebelumnya
- Jika ANda belum mendefenisikan _dan_ gaya Anda dan perilaku lainnya mengandalkan kepada adanya element root`<span>` untuk bekerja, Sederhananya, tambahkan `tag="span"` di `<transition-group>`:

```html
<transition-group tag="span">
  <!-- -->
</transition-group>
```

## Migration Strategy

[Migration build flag: `TRANSITION_GROUP_ROOT`](migration-build.html#compat-configuration)

## Lihat Juga

- [Some transition classes got a rename](/guide/migration/transition.html)
- [`<Transition>` as a root can no longer be toggled from the outside](/guide/migration/transition-as-root.html)
