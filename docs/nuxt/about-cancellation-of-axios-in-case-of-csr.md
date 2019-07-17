## CSRした場合のaxiosのキャンセルについて

asyncData と fetch 内で `axios` を使って ajax する場合、仮に、その画面に遷移してきた瞬間に **history.back()** した場合、

ajax のキャンセルをしたくなります。

asyncData と fetch は `ajax が完了するまで画面遷移が完了しないため` ブラウザの URL は変わりません。

このパターンは、 A → B → C と遷移しようとして、 B から C に遷移する瞬間に **history.back()** をして、 A に戻るという感じになります。

## axios のキャンセルのサンプル

```javascript
const CancelToken = axios.CancelToken;
const source = CancelToken.source();

axios.get('/user/12345', {
  cancelToken: source.token
}).catch(function (thrown) {
  if (axios.isCancel(thrown)) {
    console.log('Request canceled', thrown.message);
  } else {
    // handle error
  }
});

axios.post('/user/12345', {
  name: 'new name'
}, {
  cancelToken: source.token
})

// cancel the request (the message parameter is optional)
source.cancel('Operation canceled by the user.');
```

[axios/axios: Promise based HTTP client for the browser and node.js](https://github.com/axios/axios#cancellation)

こちらが axios のキャンセルするときの書き方になります。

## キャンセルはできるのか？

そもそも `asyncData` で ajax し、 `beforeDestroy` で source を使ってキャンセルしたいのですが、 this が使えない且つ、

ルーティングすらできていないので、 Vue インスタンスが生成されておらず `beforeDestroy すらも fire されない` です。

[ナビゲーションガード | Vue Router](https://router.vuejs.org/ja/guide/advanced/navigation-guards.html#%E3%83%AB%E3%83%BC%E3%83%88%E5%8D%98%E4%BD%8D%E3%82%AC%E3%83%BC%E3%83%89)

## キャンセルを意識しないajaxをする

Vuex（store） で、 API 呼び出しをすることが多いと思いますが、

CSR 時に asyncData で ajax が実行され、それがキャンセルされたとしても、

画面が変な状態にならないようにするのが、まずは大事なのかなと思っています。

たとえば、 ajax 完了時に `window.alert` を出すようにしておくと、 ajax がキャンセルされなかったときに弊害が出てしまいます。

これは、 Vuex 側で `window.alert` を出すのではなく、 dispatch を使っているコンポーネント側で書くのも一つの方法です。

```javascript
<script>
export default {
  async fetch ({ store, params }) {
    let { data } = await axios.get('http://my-api/stars')
    store.commit('setStars', data)
    
    // UI に関係することは Vuex 側でやらずにコンポーネント側でやる
    if (process.client) {
      window.alert('something')
    }
  }
}
</script>
```

## ルーティング時でキャンセルする

おそらく落とし所として、この方法があるんですが、実装がややこしくまだ検証が進んでおりません。

今後、検証次第ここに記載していきます。
