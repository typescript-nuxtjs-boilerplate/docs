## npm scriptsの解説

## `yarn run dev` alias `yarn run local`

開発中はこちらを使います。

nuxt のデフォルトポートは `3000` ですが、あえて `4000` 番ポートで起動させています。

これは、他のアーキテクチャや、このドキュメントのビルドのポートなどとバッティングしてしまうため変更しています。

## 


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
  "analyze": "cross-env NODE_ENV=production BUILD_ENV=local nuxt build --analyze"
}
```




