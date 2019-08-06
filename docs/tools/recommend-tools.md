## オススメライブラリ一覧

### Lodash

[Lodash Documentation](https://lodash.com/docs/4.17.11)

言わずとしれたユーティリティライブラリ。

`_.forEach` や `_.map` 、 `_.isEmpty` など、配列操作やオブジェクト操作はすべてを **lodash** にまかせてもいいかと思います。

それぐらい優秀なライブラリなので、是非、やりこいことが見つかったら [Lodash Documentation](https://lodash.com/docs/4.17.11) で検索してみてください。

道が開かれます。

### Moment

[Moment.js | Home](https://momentjs.com/)

時刻系で現時点で一番オススメなのが **moment** です。

ある時間が A 時間と B 時間の between かどうかなど、ややこしい比較や加算などはすべて moment にまかせてよいでしょう。

以下のようなことをしようとすると結構面倒なので、ほんと重宝しています。

```javascript
const isBetweenTargetDate = moment(new Date()).isBetween('2019-07-08 10:00:00', '2019-07-10 10:00:00');
```
