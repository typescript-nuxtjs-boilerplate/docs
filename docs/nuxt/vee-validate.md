## VeeValidateの使い方

## コンポーネントで使う場合

`$validator` を Inject します。

```javascript
import { Validator } from 'vee-validator';

export default class extends Vue {
  @Inject public $validator: Validator;
}
```

## バケツリレーする系の中間コンポーネントで使う場合

**↑といっしょ！**

## pageコンポーネントで使う場合

`$_veeValidate` でスコープを作成する

```javascript
@Component({
  $_veeValidate: {
    validator: 'new'
  }
})
export default class extends Vue {}
```

## 何やってるの？

VeeValidate は `$_veeValidate.validate` で新しいスコープを作成することができ、

そのスコープ以下にあるバリデーション可能な箇所を参照できるようになるようです。

参照可能にするコンポーネントでは `$validator` を `@Inject` すれば VeeValidate を利用できます。

## VeeValidateのルール一覧

だいたいの validate が揃っているはずなので、ここから選んでください。

[Validation Rules | VeeValidate](https://baianat.github.io/vee-validate/guide/rules.html)

また、日本語に関する validate は弱いので、自作する必要があります。
