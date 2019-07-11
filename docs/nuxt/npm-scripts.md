## npm scriptsの解説

## yarn run dev `or` yarn run local

local で開発するにはこちらを使います。

nuxt のデフォルトポートは `3000` ですが、あえて `4000` 番ポートで起動させています。

これは、他のアーキテクチャや、このドキュメントのビルドのポートなどとバッティングしてしまうため変更しています。

また、環境変数を2つ設定していますが、少し理由があります。
 
`NODE_ENV=development` は本当は必要ありません。

nuxt コマンドは、デフォルトで `NODE_ENV=development` がつくので、不必要なのですが、あえて忘れないように明示的にしています。

nuxt コマンド内部では、この環境変数は `development` に書き換えるため、たとえば、 `NODE_ENV=fuga` としても nuxt 側には到達しません。 

`BUILD_ENV=local` 

## yarn run build `or` yarn run build:local

`--no-lock` を付いていますが、 Nuxt.js のエラーが出たためつけています。

[✖ Nuxt Fatal Error Error: Lock is already released - Qiita](https://qiita.com/noracorn92/items/8ffb48e5ee3b601c8718)

## yarn run build:static

`--spa` をつけているので、 `spa モード` でビルドします。

`typescript-nuxtjs-boilerplate` では、 netlify にデプロイしていますが、 nuxt サーバーは使えないので、こちらの static なビルドを使っています。

## yarn run build:docker

`Dockerfile` 経由で呼ばれる docker 向けのビルドになります。

## yarn run lintfix

`eslint` と `prettier` を `--fix` で実行します。

定期的に実行しておくと、 commit のときに lint エラーがたくさんでないためやっておくと良いでしょう。

## yarn run clean:cache

`nuxt.config.ts` では内部的に **webpack** を呼んでいますが、 webpack の設定で

`hardSource: true`

というのがありますが、内部的にプログラムをキャッシュしてビルドを高速にするプロパティです。

[mzgoddard/hard-source-webpack-plugin](https://github.com/mzgoddard/hard-source-webpack-plugin)

これを true にするしないで速度が別物になりますが、たまにキャッシュがうまくされずエラーが出てしまう場合があります。

そんなときはこちらを実行してください。

## package.json の全体

```json
"scripts": {
  "local": "npm run dev",
  "dev": "cross-env NODE_ENV=development BUILD_ENV=local nuxt",
  "build": "nuxt build --no-lock",
  "build:static": "nuxt build --spa --no-lock",
  "build:local": "cross-env NODE_ENV=production BUILD_ENV=local envName=local nuxt build --no-lock",
  "build:docker": "cross-env NODE_ENV=production nuxt build --no-lock",
  "start": "nuxt start",
  "lint": "eslint --ext .js,.ts,.vue --ignore-path .gitignore .",
  "lintfix": "eslint --fix --ext .js,.ts,.vue --ignore-path .gitignore .",
  "precommit": "npm run lint",
  "test": "jest",
  "clean:cache": "rimraf node_modules/.cache/hard-source",
  "storybook": "start-storybook -p 9001 -c .storybook",
  "analyze": "cross-env NODE_ENV=production BUILD_ENV=local nuxt build --analyze",
  "docker": "docker-compose -f docker-compose.yml up --build",
  "docker:clean": "docker-compose -f docker-compose.yml build --no-cache && docker-compose -f docker-compose.yml up",
  "container-prune": "docker container prune",
  "image-prune": "docker image prune",
  "system-prune": "docker system prune",
  "stats": "docker container stats"
}
```
