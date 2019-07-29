## 開発で気をつけるポイント

- Nuxt の `plugin` を使わずに `prototype` 拡張をどこかのファイルでやるのはやめましょう
- Cookie は使わず、なるべく localStorage を使いましょう
  - [Nuxt.jsでログインをどうやるか、そしてCookieからlocalStorageへ - DJ lemon-Sour's diary (prod.hisasann)](https://hisasann.github.io/2019/06/22/how-to-login-with-nuxt-and-cookie-to-localstorage/)
- document オブジェクト系はなるべく使わないようにしましょう
  document.getElementById(id)など
- `alert` や `confirm` は `process.client` の場合に使うように if 文で判定しましょう
- `v-bind:val` ではなく、エイリアスを使いましょう `:val`
- `v-on:event` ではなく、エイリアスを使いましょう `@event`
- コールバックパターンよりも `async/await パターン` を使いましょう
- input 系のバリデーションは `vee-validattor` を使いましょう
[vee-validateを使う - typescript-nuxtjs-boilerplate-docs](https://typescript-nuxtjs-boilerplate-docs.netlify.com/#/nuxt/vee-validate)