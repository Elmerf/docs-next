# _Rendering List_

<VideoLesson href="https://vueschool.io/lessons/list-rendering-in-vue-3?friend=vuejs" title="Pelajari cara rendering list di Vue School">Pelajari cara melakukan _rendering_ suatu _list_ di Vue School secara gratis</VideoLesson>

## Memetakan sebuah Array menjadi element menggunakan `v-for`

Kita dapat menggunakan direktif `v-for` untuk melakukan _rendering list_ item berdasarkan array. Direktif `v-for` memerlukan sintaks khusus dalam bentuk `item in items`, yang dimana `items` adalah sumber data array dan `item` adalah sebuah **alias** untuk setiap elemen array yang di iterasi:

```html
<ul id="array-rendering">
  <li v-for="item in items">{{ item.message }}</li>
</ul>
```

```js
Vue.createApp({
  data() {
    return {
      items: [{ message: 'Foo' }, { message: 'Bar' }],
    }
  },
}).mount('#array-rendering')
```

Hasil:

<common-codepen-snippet title="v-for with Array" slug="VwLGbwa" tab="js,result" :preview="false" />

Didalam blok `v-for` kita memiliki akses penuh ke properti induk. `v-for` juga mendukung opsional agumen kedua untuk mendapatkan indeks item saat ini.

```html
<ul id="array-with-index">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

```js
Vue.createApp({
  data() {
    return {
      parentMessage: 'Parent',
      items: [{ message: 'Foo' }, { message: 'Bar' }],
    }
  },
}).mount('#array-with-index')
```

Hasil:

<common-codepen-snippet title="v-for with Array and index" slug="wvaEdBP" tab="js,result" :preview="false" />

Anda juga bisa menggunakan `of` sebagai pembatas pengganti `in`, yang membuat sintaks lebih mirip dengan sintaks Javascript untuk iterator:

```html
<div v-for="item of items"></div>
```

## `v-for` dengan menggunakan Objek

Anda juga dapat menggunakan direktif `v-for` untuk melakukan iterasi terhadap properti dari objek.

```html
<ul id="v-for-object" class="demo">
  <li v-for="value in myObject">{{ value }}</li>
</ul>
```

```js
Vue.createApp({
  data() {
    return {
      myObject: {
        title: 'How to do lists in Vue',
        author: 'Jane Doe',
        publishedAt: '2016-04-10',
      },
    }
  },
}).mount('#v-for-object')
```

Hasil:

<common-codepen-snippet title="v-for with Object" slug="NWqLjqy" tab="js,result" :preview="false" />

Anda juga bisa memberikan argument kedua untuk properti nama (alias _key_)

```html
<li v-for="(value, name) in myObject">{{ name }}: {{ value }}</li>
```

<common-codepen-snippet title="v-for with Object and key" slug="poJOPjx" tab="js,result" :preview="false" />

Dan satu lagi untuk indeks:

```html
<li v-for="(value, name, index) in myObject">
  {{ index }}. {{ name }}: {{ value }}
</li>
```

<common-codepen-snippet title="v-for with Object key and index" slug="abOaWdo" tab="js,result" :preview="false" />

:::catatan
Ketika melakukan iterasi terhadap suatu objek, urutannya didasarkan pada urutan enumerasi `Object.keys()`, yang tidak dijamin akan konsisten di seluruh implementasi mesin Javascript.
:::

## Pemeliharaan State

Ketika Vue melakukan pembaruan terhadap _list_ dari elemen yang dirender melalui `v-for`, secara bawaan Vue menggunakan strategi "in-place patch". Jika urutan data telah berubah, alih-alih memindahkan elemen DOM agar sesuai dengan urutan, Vue akan menambal setiap elemen pada tempatnya dan memastikan data tersebut sesuai dengan apa yang harus dirender pada indeks tersebut.

Mode ini cukup efisien, tetapi **hanya dapat digunakan jika output _list render_ anda tidak bergantung pada _state_ komponen turunan atau _state_ DOM sementara**

Untuk memberikan petunjuk kepada Vue agak dapat melacap setiap node, menggunakannya kembali dan menyusun ulang element yang ada, anda perlu menambahkan atribut `key` unik untuk setiap item:

```html
<div v-for="item in items" :key="item.id">
  <!-- content -->
</div>
```

[Kami merekomendasikan](/style-guide/#keyed-v-for-essential) anda untuk menambahkan atribut `key` dengan `v-for` bila memungkinkan, kecuali jika konten DOM yang diulang sangat sederhana, atau anda sengaja menggunakan perilaku bawaan untuk meningkatkan kinerja.

Karena ini adalah mekanisme yang umum bagi Vue untuk mengidentifikasi tiap node, `key` juga memiliki kegunaan lain yang tidak secara khusus terkait dengan `v-for`, seperti yang akan kita lihat nanti dalam panduan ini.

:::catatan
Jangan gunakan nilai non-primitif seperti objek dan array sebagai `key` dari `v-for`. Gunakan string atau nilai numerik sebagai gantinya.
:::.

Untuk detail mengenai penggunaan atribut `key`, silahkan lihat [Dokumentasi API `key`](../api/special-attributes.html#key)

## Deteksi perubahan pada Array

### Method Mutation

Vue membungkus sebuah array yang diamati menggunakan method mutasi, sehingga mereka juga akan memicu pembaruan tampilan tiap terjadi perubahan pada data. Method-method yang dibungkus adalah:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

Anda dapat mencobanya dengan membuka konsol dan bermain dengan contoh array `items` berikut dengan cara memanggil metode mutasinya. Sebagai contoh: `example1.items.push({ message: 'Baz' })`.

### Mengganti sebuah Array

Method _mutation_, seperti namanya, method ini dapat mengubah bentuk array yang dipanggil. Sebagai perbandingan, ada juga method non-mutasi, seperti `filter()`, `concat()` dan `slice()`, yang tidak mengubah bentuk asli array tetapi **selalu mengembalikan array baru**. Saat bekerja dengan method non-mutasi, anda bisa mengganti array lama dengan array yang baru:

```js
example1.items = example1.items.filter((item) => item.message.match(/Foo/))
```

Anda mungkin berpikir ini akan menyebabkan Vue membuang DOM yang ada dan me*render* ulang seluruh isi _list_ - untungnya, bukan itu masalahnya. Vue mengimplementasikan beberapa heuristik cerdas untuk memaksimalkan penggunaan ulang DOM, jadi mengganti array dengan array lain yang berisi objek yang tumpang tindih adalah operasi yang sangat efisien.

## Menampilkan hasil yang disaring / diurutkan

Terkadang kita ingin menampilkan versi array yang disaring atau diurutkan tanpa melakukan mutasi atau mengatur ualng data asli. Dalam hal ini, anda dapat membuat properti _computed_ yang mengembalikan array yang disaring atau diurutkan.

Sebagai contoh:

```html
<li v-for="n in evenNumbers" :key="n">{{ n }}</li>
```

```js
data() {
  return {
    numbers: [ 1, 2, 3, 4, 5 ]
  }
},
computed: {
  evenNumbers() {
    return this.numbers.filter(number => number % 2 === 0)
  }
}
```

Ketika dalam situasi dimana properti _computed_ tidak bisa digunakan (seperti didalam perulangan v-for bersarang), anda dapat menggunakan metode berikut:

```html
<ul v-for="numbers in sets">
  <li v-for="n in even(numbers)" :key="n">{{ n }}</li>
</ul>
```

```js
data() {
  return {
    sets: [[ 1, 2, 3, 4, 5 ], [6, 7, 8, 9, 10]]
  }
},
methods: {
  even(numbers) {
    return numbers.filter(number => number % 2 === 0)
  }
}
```

## `v-for` dengan rentang

`v-for` juga dapat mengambil integer. Dalam kasus ini, templat akan diulang sebanyak integer yang dimasukkan.

```html
<div id="range" class="demo">
  <span v-for="n in 10" :key="n">{{ n }} </span>
</div>
```

Hasil:

<common-codepen-snippet title="v-for with a range" slug="NWqLjNY" tab="html,result" />

## `v-for` on a `<template>`

Mirip dengan template `v-if`, Anda juga dapat menggunakan `<template>` tag dengan ` v-for` untuk membuat blok beberapa elemen. Sebagai contoh:

```html
<ul>
  <template v-for="item in items" :key="item.msg">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## `v-for` dengan menggunakan `v-if`

:::catatan
Perhatikan juga bahwa anda tidak disarankan menggunakan `v-if` dan `v-for` secara bersamaan. Lihat [Panduan](../style-guide/#avoid-v-if-with-v-for-essential) untuk detainya.
:::

Ketika mereka ada di node yang sama, `v-if` memiliki prioritas yang lebih tinggi daripada` v-for`. Itu berarti kondisi `v-if` tidak akan memiliki akses ke variabel dari ruang lingkup` v-for`:

```html
<!-- Ini akan melempar error karena properti "TODO" tidak didefinisikan pada instance -->

<li v-for="todo in todos" v-if="!todo.isComplete">{{ todo.name }}</li>
```

Hal ini dapat kita perbaiki dengan memindahkan `v-for` ke templat dengan menggunakan tag `<Template>`:

```html
<template v-for="todo in todos" :key="todo.name">
  <li v-if="!todo.isComplete">{{ todo.name }}</li>
</template>
```

## `v-for` dengan Komponen

> Pada bagian ini kami mengasumsikan anda memiliki pengetahuan tentang [komponen](component-basics.md). Jangan ragu untuk melewatkannya dan kembali lagi nanti.

Anda dapat langsung menggunakan `v-for` pada komponen khusus, seperti pada elemen normal:

```html
<my-component v-for="item in items" :key="item.id"></my-component>
```

Namun, ini tidak akan secara otomatis meneruskan data ke komponen, karena komponen telah terisolasi lingkup mereka sendiri. Untuk meneruskan data iterasi ke dalam komponen, kita juga harus menggunakan props:

```html
<my-component
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
></my-component>
```

Alasan kenapa Vue tidak secara otomatis meneruskan `item` ke dalam komponen adalah karena itu membuat komponen erat digabungkan dengan sebagaimana `v-for` berfungsi. Menjadikannya eksplisit mengenai darimana datanya berasal membuat komponen dapat digunakan kembali dalam situasi lain.

Berikut adalah contoh lengkap pembuatan _todo list_ sederhana:

```html
<div id="todo-list-example">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Add a todo</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="E.g. Feed the cat"
    />
    <button>Add</button>
  </form>
  <ul>
    <todo-item
      v-for="(todo, index) in todos"
      :key="todo.id"
      :title="todo.title"
      @remove="todos.splice(index, 1)"
    ></todo-item>
  </ul>
</div>
```

```js
const app = Vue.createApp({
  data() {
    return {
      newTodoText: '',
      todos: [
        {
          id: 1,
          title: 'Do the dishes',
        },
        {
          id: 2,
          title: 'Take out the trash',
        },
        {
          id: 3,
          title: 'Mow the lawn',
        },
      ],
      nextTodoId: 4,
    }
  },
  methods: {
    addNewTodo() {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText,
      })
      this.newTodoText = ''
    },
  },
})

app.component('todo-item', {
  template: `
    <li>
      {{ title }}
      <button @click="$emit('remove')">Remove</button>
    </li>
  `,
  props: ['title'],
  emits: ['remove'],
})

app.mount('#todo-list-example')
```

<common-codepen-snippet title="v-for with components" slug="abOaWpz" tab="js,result" :preview="false" />
