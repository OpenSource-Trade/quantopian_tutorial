# LESSON 5 The data Object

`data` オブジェクトは株価の情報を取得するさまざまなメソッド[^1]が用意されています。
直近または過去の株価や出来高の情報を取得できます。
`data` オブジェクトは下記の関数で使用できます。

* handle_data()
* before_trading_start()
* schedule_function() [^2]

## data.current()

`current()` メソッドでは直近の情報を取得できます。
下記のコードではAPPLの直近の株価を取得しています。

```python
data.current(sid(24), 'price')
```
引数は 'price' の他に下記の指定ができます。

引数|説明
---|---
price|現在値
open|始値
high|高値
low|安値
close|終値
volume|出来高

下記のように複数銘柄を指定することもできます。
この場合、戻り値はpandasのSeries型が返ります。

```python
data.current([sid(24), sid(8554)], 'price')
```

下記のように複数銘柄に対して複数の戻り値がある場合はpandasのDataFrame型が返ります。

```python
data.current([sid(24), sid(8554)], ['low', 'high'])
```

## data.can_trade()
`can_trade()` メソッドは対象の銘柄が取引所で取引可能であるかを返します。取引可能であれば `True` を返します。自分でこの機能を実装しようとすると結構大変なので、地味だけど便利な機能ですね。

## data.history()
[LESSON 6](./LESSON6.md) で解説します。

[LESSON 4](./LESSON4.md) <--> [LESSON 6](./LESSON6.md)

---
[^1]: 原文では 'functions' と記載されていますが、Pythonユーザにとってわかりやすい表現にしています。
[^2]: : LESSON 7で解説します。
