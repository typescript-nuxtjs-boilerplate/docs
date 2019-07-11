## asyncDataとfetch内でのthisについて

> 警告: fetch メソッド内の this を通してコンポーネントのインスタンスにアクセスすることは できません。それはコンポーネントがインスタンス化される前に呼び出されるためです。

[API: fetch メソッド - Nuxt.js](https://ja.nuxtjs.org/api/pages-fetch/)

## thisが期待とおりに動かないパターン

以下のコードは一見すると this.foo により、画面に "foo" と文字が表示されそうですが、ブラウザには、

`TypeError: Cannot read property 'foo' of undefined`

と表示されてしまいます。

```vue
<template>
<h1>{{ foo }}</h1>
</template>

<script lang="ts">
import { Component, Vue } from 'nuxt-property-decorator'

@Component
export default class Sample extends Vue {
  private foo: string = 'foo'
  
  asyncData() {
    return {
      foo: this.foo
    }
  }
}
</script>
```

asyncData と fetch が特殊なのであって、 `created` や `mounted` では問題なく **this** にアクセスできます。

つまり、 Vue.js のライフサイクル上のメソッドは問題ないのですが、 Nuxt.js のライフサイクルメソッドでは this アクセスできないという感じになります。

## asyncDataとfetch内でthisが使いたくなるとき

TODO: あとで書く
