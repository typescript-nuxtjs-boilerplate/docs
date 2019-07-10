## Nuxt.js で page （画面）を追加する方法

ここでは一画面を追加するための、 **はじめに** を解説しています。

Nuxt.js のボイラープレートの全容は、 

[typescript-nuxtjs-boilerplate/typescript-nuxtjs-boilerplate: Nuxt.js with TypeScript and Run with docker and docker-compose](https://github.com/typescript-nuxtjs-boilerplate/typescript-nuxtjs-boilerplate)

こちらのリポジトリをご覧ください。

今回は仮のページ、 `お知らせ一覧` を開発するとして page の追加の仕方を記載していきます。

## page を追加する

お知らせ一覧の page を追加します。

たとえばこんな page を想定します。 `/src/pages/notice/list.vue`

この場合、ブラウザからは `http://domain/notice/list` でアクセスできます。

## Page - /src/pages/notice/list.vue

`Vue.js` は `.vue` という拡張子のファイルに

* HTML（Pug）
* JavaScript（TypeScript）
* CSS（Sass）

を記載していきます。

この感覚は、少し慣れが必要ですが、一つのファイルに画面を構成するための要素がまとまっているので見やすいと思います。

サンプルでは、 `NoticeListComponent` というコンポーネントがある想定で書いてます。

また、これぐらいの量であれば、実際には Component を分ける必要はないかもしれないですが、サンプルとして書いています。

```vue
<template lang="pug">
article
  NoticeListComponent(
    :noticeListData="noticeListDataComputed"
  )
</template>

<script lang="ts">
import { Component, Vue } from 'nuxt-property-decorator'
import {
  INoticeListPayload,
  INotice
} from '@/interfaces/api/Notice/INoticeList'
import { cancelToken } from '@/utilities/'
import NoticeListComponent from '@/components/pages/notice/noticeListComponent.vue'

@Component({
  components: {
    NoticeListComponent
  },
  // たとえばログイン必須画面だとした場合の middleware
  middleware: 'authenticated',
  layout: 'notDefaultLayout'
})
export default class NoticeListPage extends Vue {
  /** リクエストキャンセル用のシンボル */
  public symbols = {
    fetchNoticeList: cancelToken.create(Symbol('fetchNoticeList'))
  }

  /** computed */
  // getter にすると自動的に computed になる
  public get noticeListDataComputed(): INotice[] {
    return this.$store.state.notice.noticeList
  }

  /** Nuxt ライフサイクル */
  public async asyncData() {
    await console.log('asyncData')
  }

  /** Nuxt ライフサイクル */
  public async fetch({ store }) {
    const payload: INoticeListPayload = {}
    await store.dispatch('notice/fetchNoticeList', {
      data: payload
    })
  }

  /** Vue ライフサイクル */
  // インスタンスの初期化が済んで props や computed にアクセスできる
  // DOM にはアクセスできない
  public created() {}

  /** Vue ライフサイクル */
  // created + DOMにアクセスできる
  public mounted() {
  }

  /** Vue ライフサイクル */
  public beforeDestroy(): void {
    // リクエストをキャンセル
    cancelToken.cancelAll(Object.values(this.symbols), this.$t(
      'error.api.cancel'
    ) as string)
  }

  /** head */
  public head() {
    return {
      // vue-i18n で直値を管理している場合
      title: this.$t('text.notice.noticeList.title')
    }
  }
}
</script>

<style lang="scss" scoped></style>
```

## Interface - /src/interfaces/api/Notice/INoticeList.d.ts

`BFF` と通信をしたときのレスポンス（json）をあらかじめ定義しておきます。

ここで、 swagger や、何かサーバーサイドの方とやりとりをし、定義を書いていきます。

```javascript
/**
 * お知らせ一覧インターフェイス
 */
export interface INoticeListPayload {}

export interface INotice {
  noticeDiv: number
  title: string
}

export interface INoticeList {
  noticeList: INotice[]
}
```

## Vuex - /src/store/notice.ts

`Vuex` は `React+Redux` を使ったことある方にはピンとくると思いますが、サイト全体から参照できる値を保持できる Store になります。

`Ajax` を `Page 内でやるのか` 、 `Vuex 内でやるのか` は、悩ましいところですが、一旦は Vuex 経由で Ajax し、値は Vuex に保存するのが良いかと思います。

```javascript
import Vue from 'vue'
import { AxiosRequestConfig } from 'axios'
import {
  INoticeListPayload,
  INoticeList,
  INotice
} from '@/interfaces/api/Notice/INoticeList'
import { cancelToken } from '@/utilities/'
import { AxiosAction } from '@/interfaces/app/vuex'

/**
 * store 用インターフェイス
 */
export interface IState {
  /**
   * お知らせ一覧
   */
  noticeList: INotice[]
  /**
   * ローディング
   */
  busy: {
    noticeList: boolean
  }
}

/**
 * state
 */
export const state = (): IState => ({
  noticeList: [],
  busy: {
    noticeList: false,
  }
})

/**
 * getters
 */
export const getters = {
  getNoticeList(state: IState): INotice[] | null {
    return state.noticeList
  },
}

/**
 * mutations
 */
export const mutations = {
  /**
   * お知らせ一覧を保存する
   */
  saveNoticeList(state: IState, noticeList: INotice[]): void {
    state.noticeList = noticeList
  },
  /**
   * 処理中ステータスを更新する
   */
  updateBusyStatus(
    state: IState,
    payload: [keyof IState['busy'], boolean]
  ): void {
    const [key, status] = payload

    Vue.set(state.busy, key, status)
  }
}

/**
 * actions
 */
export const actions = {
  /**
   * お知らせ一覧を取得する
   * @param state
   * @param commit
   * @param payload
   */
  async fetchNoticeList(
    this: Vue,
    { state, commit }: any,
    payload: AxiosAction<INoticeListPayload> = {} as any
  ): Promise<void> {
    const { key = Symbol('fetchNoticeList'), data } = payload

    commit('updateBusyStatus', ['noticeList', true])

    try {
      const { noticeList }: INoticeList = await this.$axios.$post<INoticeList>(
        this.$C.API_ENDPOINT.NOTICE_LIST,
        {},
        {
          cancelToken: cancelToken.getToken(key)
        } as AxiosRequestConfig
      )

      // お知らせ一覧情報をストアに保存
      commit('saveNoticeList', noticeList || [])
    } catch (err) {
      throw err
    } finally {
      commit('updateBusyStatus', ['noticeList', false])
    }
  }
}
```

### axios

axios は Ajax をするためのモジュールですが、少し使い方がややこしいです。

[nuxt-community/axios-module: Secure and easy axios integration with Nuxt.js](https://github.com/nuxt-community/axios-module)

Nuxt.js 用の拡張された `this.$axios.$post` は以下のようにデータが直接返ってきてしまいます。

```javascript
const { noticeList }: INoticeList = await this.$axios.$post<INoticeList>()
```

これはこれで便利なんですが、レスポンスヘッダーなどレスポンスに関するものにアクセスしたい場合は、拡張されていない版を使う必要があります。

それがこちらです。

```javascript
const { data, headers } = await this.$axios.post<INoticeList>()
```

ただ、 `post` を使うのか `$post` を使うのかの違いですが、まったく挙動が違うので、注意が必要です。

参考記事：

[Nuxtモジュールのaxiosでレスポンスヘッダが取得できない - Qiita](https://qiita.com/mr-hisa-child/items/bfce5efe82fb11db700b)

## Enum - /src/common/constants/notice.ts

ここでは、 DB に登録されている `内部コード` をフロントエンド側で出すときに、 constants として管理することについて記載します。

BFF 側からラベル付きで返ってくる場合もあると思いますが、 0 や 1 などの内部コードが返ってきた場合は、フロントエンド側でラベル管理します。

```javascript
/** お知らせ区分 */
export enum NOTICE_DIV {
  ALL_USERS = 0,
  ALL_MEMBERS = 1
}

/** お知らせ区分とラベルの紐づけ */
export const NOTICE_DIV_LABEL = {
  [NOTICE_DIV.ALL_USERS]: '全ユーザー',
  [NOTICE_DIV.ALL_MEMBERS]: '全会員'
}
```
