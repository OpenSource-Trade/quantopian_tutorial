# LESSON 4 Ordering Securities

`order_target_percent()` 関数を用いて銘柄をロングしたりショートしたりします。
第一引数には [LESSON 3](http://qiita.com/driller/items/b7eb097ea094ee92e023) で作成した銘柄のインタンス、第二引数には全資産を1とした場合(以降ポートフォリオ)の比率を指定します。

下記の例ではAAPLをポートフォリオの50%分ロングしています。

```python
order_target_percent(sid(24), 0.50)
```

上記を実行する以前にポジションがなければ、0.5の資金がロングされますが、この時点で他のポジションが含まれている場合は投入される金額が異なることに注意してください。

ショートする場合は第二引数に負の値を指定します。

```python
order_target_percent(sid(24), -0.50)
```

下記のコードではAAPLをポートフォリオの60%分ロングし、SPYをポートフォリオの40%分ショートしています。
コードは [こちら](https://www.quantopian.com/tutorials/getting-started#lesson4) からクローンできます。
`data.can_trade()` については LESSON 5で解説します。

```python
def initialize(context):
    context.aapl = sid(24)
    context.spy = sid(8554)

def handle_data(context, data):
    # Note: data.can_trade() is explained in the next lesson
    if data.can_trade(context.aapl):
        order_target_percent(context.aapl, 0.60)
    if data.can_trade(context.spy):
        order_target_percent(context.spy, -0.40)
```
[LESSON 3](./LESSON3.md) <--> [LESSON 5](LESSON5.md)
