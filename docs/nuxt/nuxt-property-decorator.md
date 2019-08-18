## Nuxtが推奨しているデコレータパターン

Nuxt で page（.vue）ファイルを作る場合、 `export default` する class をオブジェクト形式で書くか、

`vue-property-decorator` 形式で書くか、はひじょうに迷うところではありますが、

    コンポーネントのために vue-property-decorator を利用することを強くお薦めします。

via [TypeScript サポート - Nuxt.js](https://ja.nuxtjs.org/guide/typescript/#%E3%82%B3%E3%83%B3%E3%83%9D%E3%83%BC%E3%83%8D%E3%83%B3%E3%83%88)

と書いてあるとおり、 Nuxt 側がデコレータパターンで書くことをオススメしているので、こちらの記法でこのボイラープレートも書いています。

個人的には、 `Vue.js` すら久々に触っている筆者としては、はじめは慣れずに苦労しました。

ただ、手に馴染んでくると、すっきりした見た目になるので、今ではこちらを採用しています。

[kaorun343/vue-property-decorator: Vue.js and Property Decorator](https://github.com/kaorun343/vue-property-decorator)

## nuxt-property-decorator

[nuxt-community/nuxt-property-decorator: Property decorators for Nuxt (base on vue-property-decorator)](https://github.com/nuxt-community/nuxt-property-decorator)

`vue-property-decorator` をさらに Nuxt 版に拡張しているので、機能の追加にはラグがありますが、

Nuxt を使う場合はこちらを使うのが良いかと思います。

## hookできるものが盛りだくさん

これだけあると、慣れるまで時間が掛かると思いますが、実際に使うのは数種類です。

### Nuxt hooks
* asyncData
* fetch
* head
* key
* layout
* loading
* middleware
* scrollToTop
* transition
* validate
* watchQuery

[asyncDataとfetchの使い分け - typescript-nuxtjs-boilerplate-docs](https://typescript-nuxtjs-boilerplate-docs.netlify.com/#/nuxt/asyncdata-or-fetch)

### Vue-class Hooks
* data
* beforeCreate
* created
* beforeMount
* mounted
* beforeDestroy
* destroyed
* beforeUpdate
* updated
* activated
* deactivated
* render
* errorCaptured
* serverPrefetch

以下の記事をまずは一読することをオススメします。

[はじめてのvue-property-decorator - Qiita](https://qiita.com/simochee/items/e5b77af4aa36bd0f32e5)
