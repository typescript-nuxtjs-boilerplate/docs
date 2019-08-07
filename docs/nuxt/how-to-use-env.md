## Nuxt.jsにenv（環境変数）をどう渡すのか

`process.env.xxx` を `nuxt` は `nuxt コマンド` や `nuxt build コマンド` のタイミングで実際の値に書き換えられます。

詳しくは以下を御覧ください。

[API: env プロパティ - Nuxt.js](https://ja.nuxtjs.org/api/configuration-env/#process-env-)

## docker-composeの環境変数。envをNuxt.jsに渡す方法

そして、さらにややこしいのが、 `docker-compose` などのオーケストレーション系から呼ばれる場合に、

`docker-compose.env` や `AWS ECS の環境変数` を `Nuxt.js` 側に渡す必要があります。

これをどう実現するかは以下の記事をご覧ください。

少し長いですが、全貌が理解できるかと思います。

[docker-composeの環境変数.envをNuxt.jsに渡す方法 - DJ lemon-Sour's diary (prod.hisasann)](https://hisasann.github.io/2019/03/05/how-to-pass-docker-compose-env-to-nuxt/)

## 環境変数を扱うファイルたち

* src/interfaces/app/IEnv.d.ts
* nuxt.config.ts
* docker-compose.env
* src/common/env/env.local.ts
* src/common/env/index.ts

## ローカル開発時の環境変数の流れ

`nuxt.config.ts` ➡️️ `src/common/env/env.local.ts` ➡️ `src/common/env/index.ts`

## オーケストレーション時の環境変数の流れ（docker-composeなど）

`docker-compose.env` ➡️️ `nuxt.config.ts` ➡️ `src/common/env/index.ts`
