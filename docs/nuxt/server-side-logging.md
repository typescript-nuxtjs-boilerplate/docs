## サーバーサイドのログ処理

サーバーサイドというのはつまり `SSR` 時ですが、サーバーのリソースにダイレクトにログを保存するのは、スケールアウトを意識していない環境かもしれませんが、

サクッと `AWS EC2` 一台で作るときには、必要になってくると思います。

## Nuxt.js に導入するには？

今回は拙作の logger を使っております。

[lemon-sour/node-log-rotate: Just a very simple logging module for your node.js application](https://github.com/lemon-sour/node-log-rotate)

plugin として `$log` を inject するパターンを紹介しておきます。

### plugin/node-log-rotate.ts

```javascript
import { Context } from '@nuxt/vue-app'
import { setup, log } from 'node-log-rotate'
import { pjName } from '@/common/constants/'

// logger のセットアップ
setup({
  appName: pjName
})

export default (context: Context, inject: any): void => {
  // https://github.com/nuxt-community/axios-module/blob/dev/lib/plugin.js#L200
  context.$log = log
  inject('log', log)
}
```

### plugin/axios.ts

```javascript
export default ({ $axios, $log, app, req, error }): void => {
  /**
   * $axios.onResponse
   */
  $axios.onResponse((response): void => {
    const token = response.headers[app.$C.ACCESS_TOKEN_NAME]
    const { status, statusText, config, data } = response
    // SSR 時だけ log を実行する
    if (process.server) {
      $log(`${config.url}:${status}`)
    }
  })

  /**
   * $axios.onResponseError
   */
  $axios.onResponseError((response: AxiosError): void => {
    console.log('$axios.onResponseError')
    // 通信エラー
    if (!response.response) {
      return
    }

    const { status, statusText, config } = response.response

    // SSR 時だけ log を実行する
    if (process.server) {
      $log(`${config.url}:${status}`)
    }
  })
}
```

### nuxt.confit.ts

`fs` などの node.js 組み込みモジュールを使う場合は、 webpack のバンドルを意識する必要があります。

以下のようにすることで、 fs を browserify されないようにできます。

```json
build: {
  // https://github.com/nuxt-community/dotenv-module/issues/11
  config.node = {
    fs: 'empty'
  }
}
```

plugin は配列に入れる順番を意識しないと plugin から plugin を呼ぶことはできません。

たとえば、 axios.ts から node-log-rotate.ts の plugin を呼ぶには以下のようにしてください。

```json
plugins: [
  { src: '@/plugins/libraries/node-log-rotate.ts', mode: 'server' },
  '@/plugins/libraries/axios.ts'
]
```

## サンプルのプルリク

[Feature/add server side logger by hisasann · Pull Request #3 · typescript-nuxtjs-boilerplate/typescript-nuxtjs-boilerplate](https://github.com/typescript-nuxtjs-boilerplate/typescript-nuxtjs-boilerplate/pull/3)
