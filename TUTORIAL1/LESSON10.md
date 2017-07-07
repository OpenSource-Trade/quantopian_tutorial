# LESSON 10 Managing Orders
Quantopianのスリッページモデルを使用していると、出した注文が必ずしもすべて約定するとは限りません。未約定の注文は約定またはキャンセルされるまで残るため、このような状態を考慮する必要があります。

`order_target_percent()` は未約定の注文を考慮しません。当日の引けで注文はキャンセルされますが、1日の間に未約定の注文が残ったまま追加の注文を出すと、元の資金をオーバして取引してしまいます(オーバオーダ)。

オーバオーダを回避するためには `get_open_orders()` 関数を用いて未約定の注文を確認します。引数に銘柄のインスタンスを渡すことで、対象銘柄の未約定注文を辞書型で返します。

下記の例では未約定の注文が存在しない状態で、且つ取引可能である場合にXTLをロングしています。コードは [こちら](https://www.quantopian.com/tutorials/getting-started#lesson10) からcloneできます。

```python
def initialize(context):
    # Relatively illiquid stock.
    context.xtl = sid(40768)

def handle_data(context, data):
    # Get all open orders.
    open_orders = get_open_orders()

    if context.xtl not in open_orders and data.can_trade(context.xtl):
        order_target_percent(context.xtl, 1.0)
```

# LESSON 11 Putting It All Together


[LESSON 9](./LESSON9.md) <--> [LESSON 11](./LESSON11.md)
