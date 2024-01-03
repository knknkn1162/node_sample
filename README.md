## ch1

+ 外部ライブラリの作成方法
  + 利用側では`require('./test')`とする
```js
// test.js
const module = { exports: {}}
let exports = module.exports

# requireで公開にするには以下のように書く
module.exports = {
  add: ...,
  sub: ...
}
```

+ `use strict`をつける

## ch2
+ 関数は以下のような形。
  + callbackは非同期実行にする
```js
func(arg1, arg2, callback : (err, res) => {...})
```

+ 非同期実行化のコード
  + `setTimeout(() => func(arg), 0)`: 遅い
  + `process.nextTick(() => func(arg))`: 最も早い/nodejsのみ
  + `queueMicrotask(() => func(arg))`: 2番目に早い/ブラウザでも動く
  + `Promise.resolve().then(() => func(arg))`: メソッドチェーンでかける/関数の規則が守られている必要がある
    + pending -> (fulfilled or rejected)の状態遷移
    + fulfilled, rejectedと確定している場合: Promise.resolve, Promise.rejectを使う
    + Promiseの配列を引数に取る: Promise.all(), Promise.race(), Promise.allSettled(), Promise.any()
    + util.promisify(func)で上記の規約を満たした非同期関数を返す

+ ジェネレータ: 同期処理のように書ける
  + yield
    + go-stopできる
    + next(arg)で`arg = (yield res)`となり、再開する
    + throw()で処理が異常終了する

```js
simple: function*(num) {
  // 2. 1を返す
  // 4. 2回目のnextが呼ばれるまで待機 <- これのおかげで非同期関数の値が返ってくるまで待つような処理(promise.then)を入れることであたかも同期的な振る舞いの処理にできる
  const value = yield 1;
  // 6. value = 2が入る
  console.log("ans: ", value);
  // 8. 関数終わり
};
> m = require('./test');
// 1. generatorを得る
> c = m.simple();
Object [Generator] {}
// 2. 1回目のnext実行
> c.next();
// 3. value=1になる
{ value: 1, done: false }
// 5. 2回目のnext実行
> c.next(2);
// 7. 出力
ans:  2
// 9. done=trueになる
{ value: undefined, done: true }
```