## TypeScriptをどの程度使うか

初学者にとって、 `TypeScript` の **型に対する思いはかなり重厚** です。

なので、まずは、お仕事としてどの程度 TypeScript を使っていくと Nuxt.js でうまく回せるのか、というところにフォーカスして書いていきます。

とにかくスモールスタートしていきましょう！

## 型を付ける

```javascript
const message: string = 'something'
```

このように、まずは変数定義のときに型を付与していきましょう！

これだけでもコンパイル時に適切にエラーで TypeScript が教えてくれます。

## Interfaceを使う

`Interface` を使うことが、 TypeScript 醍醐味であるなーと思うぐらい便利なんですが、

特に BFF との ajax 時のレスポンスを定義することで、 API を把握するのに手助けしてくれます。

```javascript
/**
 * Login インターフェイス
 */
export interface ILoginPayload {
  username: string
  password: string
}

export interface IUser {
  loggedIn: boolean
}
```

以下のように Vuex との IF としても機能しますし、レスポンスの値がどうであるかも把握できるので、便利です。

```javascript
export const actions = {
  /**
   * login
   * @param state
   * @param commit
   * @param payload
   */
  async login(
    this: Vue,
    { state, commit }: any,
    payload: ILoginPayload
  ): Promise<IUser | void> {
    try {
      const { data, headers } = await this.$axios.post<IUser>(
        this.$C.API_ENDPOINT.LOGIN,
        {
          username: payload.username,
          password: payload.password
        }
      )
      
      // ログイン状態を更新
      commit('updateLoginStatus', data.loggedIn)
      // ユーザー情報をストアに保存
      commit('SET_USER', data)

      return data
    } catch (err) {
      throw err
    }
  }
}
```

## Enumを使う

`enum` は Java などでも使用され、サーバーサイドの方でも入りやすい印象をうけます。

また、既存の昔ながらのサーバーから html をダウンロードしてくるパターンを SSR 版にリプレースする場合、

サーバーサイドの Java にあるものを TypeScript の enum に持ってくるなど、割と移行に貢献できます。

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
