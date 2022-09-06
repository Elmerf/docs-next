---
badges:
  - breaking
---

# Penyatuan Slot <MigrationBadges :badges="$frontmatter.badges" />

## Gambaran Umum

Perubahan ini menyatukan slot-slot normal dan scoped di 3.x.

Berikut adalah gambaran sekilas apa yang berubah:

- `this.$slots` sekarang mengekspos slot-slot sebagai fungsi
- **BREAKING**: `this.$scopedSlots` telah dihapuskan

Untuk informasi lebih lanjut, silahkan baca!

## 2.x Sintaksis

Ketika menggunakan fungsi render, contoh, `h`, 2.x sebelumnya mendefinisikan properti data `slot` di node-node konten.

```js
// 2.x Sintaksis
h(LayoutComponent, [
  h('div', { slot: 'header' }, this.header),
  h('div', { slot: 'content' }, this.content)
])
```

Tambahan, ketika mereferensikan slot-slot scoped, dapat direferensikan menggunakan sintaksis berikut:

```js
// 2.x Sintaksis
this.$scopedSlots.header
```

## 3.x Sintaksis

Pada 3.x, slot-slot didefinisikan sebagai anak dari node orang tua sebagai objek:

```js
// 3.x Sintaksis
h(LayoutComponent, {}, {
  header: () => h('div', this.header),
  content: () => h('div', this.content)
})
```

Dan ketika Anda butuh mereferensikan slot-slot scoped secara terprogram, mereka sekarang tergabung dalam opsi `$slots`.

```js
// 2.x Sintaksis
this.$scopedSlots.header

// 3.x Sintaksis
this.$slots.header()
```

## Strategi Migrasi

Perubahan skala besar telah dikirimkan di 2.6. Sebagai hasilnya, migrasi dapat terjadi dengan satu langkah:

1. Mengubah semua `this.$scopedSlots` bentuk dengan `this.$slots` di 3.x.
2. Mengubah semua bentuk dari `this.$slots.mySlot` dengan `this.$slots.mySlot()`

[Migration build flag: `INSTANCE_SCOPED_SLOTS`](migration-build.html#compat-configuration)
