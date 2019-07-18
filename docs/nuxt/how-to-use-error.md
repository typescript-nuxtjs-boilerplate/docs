## Nuxt.jsでAPIエラーをどう扱うか

[ビュー - Nuxt.js](https://ja.nuxtjs.org/guide/views/#%E3%82%A8%E3%83%A9%E3%83%BC%E3%83%9A%E3%83%BC%E3%82%B8)

Nuxt.js は `axios で ajax をすることが主流` になりますが、その場合のエラーハンドリングはなかなか悩ましいものです。

とくに、共通的な処理を実装するのか、そのまま使うのかなど、考え出すと定石を見つけるのは難しいです。

ここでは、 **ベーシック** に Nuxt.js の機能を使う方法を焦点を当てて解説しています。

## SSR時のerror関数の有用性

SSR の場合で axios の http エラーが発生した場合は、

ブラウザからアクセスする URL のレスポンスが `error` 関数に渡す `statusCode` になります。

つまり、 `layout/error.vue` が画面には表示されますが、 URL のレスポンスは 401 や 500 など指定したものになります。

CSR の場合は、 error 関数でただエラー画面が表示されます。

## asyncDataでのエラーハンドリング

```javascript
public async asyncData({ $axios, app, error }: any): Promise<void> {
  // try-catch パターン
  try {
    await $axios.$get(`http://localhost:5000/status/${app.$C.HTTP_STATUS.OK}`)
  } catch (err) {
    error({
      statusCode: err.response.status,
      message: err.message
    })
  }
}
```

## fetchでのエラーハンドリング

```javascript
public async fetch({ app, store, error }: any): Promise<void> {
  // try-catch パターン
  try {
    await store.dispatch('http-status/fetchHttpStatus', app.$C.HTTP_STATUS.OK)
  } catch (err) {
    error({
      statusCode: err.response.status,
      message: err.message
    })
  }
}
```

## asyncData、fetch以外でのエラーハンドリング

```javascript
public async onClick() {
  // await-catch パターン
  const response = await this.$axios
    .get(
      `http://localhost:5000/status/${this.$C.HTTP_STATUS.SERVICE_UNAVAILABLE}`
    )
    .catch(err => {
      return err.response
    })

  if (response.status !== this.$C.HTTP_STATUS.OK) {
    this.$nuxt.error({
      statusCode: response.status,
      message: response.data
    })
    // ここの return が重要で、これがないとこれ以降の処理が継続してしまう
    return
  }

  console.log('onClick success')
}
```

`this.$nuxt.error` で TypeScript のエラーが出る場合は、 `src/@types/nuxt.d.ts` を作成して、以下を記載するとエラーが消えます。

```javascript
import { ErrorParams } from '@nuxt/vue-app'

declare module 'vue/types/vue' {
  interface NuxtApp {
    error(params: ErrorParams): void
  }
}
```

## axiosの共通処理でのエラーハンドリング

```javascript
import { AxiosError, AxiosRequestConfig } from 'axios'

export default ({ $axios, app, req, error }): void => {
  /**
   * $axios.onResponseError
   */
  $axios.onResponseError((response: AxiosError): void => {
    console.log('$axios.onResponseError')
    // 通信エラー
    if (!response.response) {
      return
    }

    const { status, data } = response.response

    // 401
    if (status === app.$C.HTTP_STATUS.UNAUTHORIZED) {
      const message = app.i18n.t('error.api.status401')
      error({ statusCode: status, message })
    }
  })
}
```

## 参考記事

[Nuxt.js と axios のエラーハンドリング周りの話 - Slide - mya-ake.com](https://mya-ake.com/slides/nuxt-axios-error-handling)

[Nuxt.js で共通のエラーページに遷移させる](https://blog.naoki85.me/posts/68.html)

[Nuxt 404ページへの遷移方法と、メッセージの設定 | Web and Me](http://log.tkyisgk.com/2019/01/14/136/)
