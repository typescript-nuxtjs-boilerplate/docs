## asyncDataとfetchの使い分け

`Nuxt.js` を使うにあたって、最も重要なライフサイクルメソッドと言えるのが、 `asyncData` と `fetch` です。

**SSR（サーバーサイド）** と **CSR（他のルートへ移動したとき）** に呼び出され、 `非同期処理に対応` します。

なので、 SEO や head タグの中身を意識した画面で、 ajax するなら、このどちらかのメソッドでするとよいでしょう。

2つのメソッドの違いについては、以下の記事を読むとよく理解できるのですが、

[Nuxt.jsのasyncDataとfetchは何が違うのか - Qiita](https://qiita.com/Tsuyoshi84/items/2e47b7f5e7fb8c0c3c66)

シンプルに言うなら **Vuex** を使うか使わないかです。

`asyncData` は使わないほう、 `fetch` は使うほうです。

かならずしもどっちがいいとかという話ではないのですが、 Vuex 側に ajax のコードを入れて store 管理させていく場合は、 fetch 縛りしてもよいかもしれません。

また、その画面固有の ajax と言える場合で、ややこしくならない範囲であれば asyncData で ajax するのもありかもしれません。

下のほうに Nuxt.js のライフサイクルをわかりやすくイラストにしてくれた方の画像を貼っておきます。

参考としてみるとよいでしょう。

## asyncDataメソッド

> サーバーサイドでデータを取得し、それをレンダリングしたいことがあるでしょう。 Nuxt.js はコンポーネントを初期化する前に非同期の処理を行えるようにするために asyncData メソッドを追加しています。

[非同期なデータ - Nuxt.js](https://ja.nuxtjs.org/guide/async-data)

```vue
<template>
<h1>{{ title }}</h1>
</template>

<script>
export default {
  async asyncData ({ params }) {
    let { data } = await axios.get(`https://my-api/posts/${params.id}`)
    return { title: data.title }
  }
}
</script>
```

## fetchメソッド

> fetch メソッドは、ページがレンダリングされる前に、データをストアに入れるために使われます。コンポーネントのデータをセットしないという点を除いては asyncData メソッドとよく似ています。

[API: fetch メソッド - Nuxt.js](https://ja.nuxtjs.org/api/pages-fetch/)

```vue
<template>
<h1>Stars: {{ $store.state.stars }}</h1>
</template>

<script>
export default {
  async fetch ({ store, params }) {
    let { data } = await axios.get('http://my-api/stars')
    store.commit('setStars', data)
  }
}
</script>
```

## Nuxt.js のライフサイクルについて

### Nuxt.js modules, serverMiddleware and plugins

こちらは、 asyncData と fetch にはあまり関係ないですが、見ておくとよいでしょう。

![https://pbs.twimg.com/media/DeqXp7PUwAAvMIk.jpg:large](https://pbs.twimg.com/media/DeqXp7PUwAAvMIk.jpg:large)

<blockquote class="twitter-tweet" data-cards="hidden" data-lang="ja"><p lang="en" dir="ltr">Understanding modules, serverMiddleware and plugins in Nuxt Js Configuration <a href="https://twitter.com/nuxt_js?ref_src=twsrc%5Etfw">@nuxt_js</a> <a href="https://twitter.com/vuejs?ref_src=twsrc%5Etfw">@vuejs</a> ✍️ <a href="https://t.co/QSubHBkECP">pic.twitter.com/QSubHBkECP</a></p>&mdash; Krutie Patel (@KrutiePatel) <a href="https://twitter.com/KrutiePatel/status/1002781159997100033?ref_src=twsrc%5Etfw">2018年6月2日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

### Nuxt.js Lifecycle Hooks...

![https://pbs.twimg.com/media/DeDKSyuUwAARbq-.jpg:large](https://pbs.twimg.com/media/DeDKSyuUwAARbq-.jpg:large)

<blockquote class="twitter-tweet" data-cards="hidden" data-lang="ja"><p lang="en" dir="ltr">Understanding Nuxt.js Lifecycle Hooks... <a href="https://twitter.com/nuxt_js?ref_src=twsrc%5Etfw">@nuxt_js</a> <a href="https://twitter.com/vuejs?ref_src=twsrc%5Etfw">@vuejs</a> ...had ton of fun creating this diagram. ✍️ <a href="https://t.co/nDu1dXRfEF">pic.twitter.com/nDu1dXRfEF</a></p>&mdash; Krutie Patel (@KrutiePatel) <a href="https://twitter.com/KrutiePatel/status/1000022559184764930?ref_src=twsrc%5Etfw">2018年5月25日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
