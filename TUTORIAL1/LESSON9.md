# LESSON 9 Slippage and Commission

## Slippage
### set_slippage()
スリッページを設定する場合には `set_slippage()` メソッドを用います。これも定義なしで存在しています。`initialize()` 関数内で定義する必要があります。
引数に後述するスリッページのモデルを渡します。

```python
set_slippage(slippage.VolumeShareSlippage(volume_limit=0.025, price_impact=0.1))
```

### Slippage Models
スリッページのモデルは `FixedSlippage` と `VolumeShareSlippage` の他、カスタマイズしたモデルを作成できます。
モデルは `slippage` モジュールに属していますが、このモジュールは予めインポートされています。

#### FixedSlippage
bid/askのスプレッドを固定で指定します。流動性の低い銘柄には向いていません。

#### VolumeShareSlippage
キーワード引数 `volume_limit` に出来高に対する注文数の割合を設定します。デフォルトは0.025です。例えば1分の出来高が1000である銘柄に60株を注文した場合、25株、25株、10株と注文が分割されて執行されます。

キーワード引数 `price_impact` にマーケットインパクトとなる定数を設定します。デフォルトは0.1です。出来高と注文数の比率の2乗にこの定数を掛けた値がマーケットインパクトとなります。出来高が1000である銘柄に25株を注文した場合、マーケットインパクトは下記のようになります。

```
0.1 * (25 / 1000) ** 2 = 0.00625％
```

また、約定できなかった注文は当日の引けでキャンセルされます。FixedSlippageモデルと比較して、かなり実用的なモデルと言えそうです。

## Commission
手数料は `set_commission()` メソッドを用います。

```python
set_commission(commission.PerShare(cost=0.0075, min_trade_cost=1))
```

### Commission Models
スリッページのモデルは `PerShare` と `PerTrade` があります
モデルは `commission` モジュールに属していますが、このモジュールは予めインポートされています。

#### PerTrade
1注文当たりの手数料を設定します。

#### PerShare
キーワード引数 `cost` に1株当たりの手数料を設定します。デフォルトは0.0075ドルです。キーワード引数 `min_trade_cost` に1注文当たりの最低手数料を設定します。デフォルトは1ドルです。

[LESSON 8](./LESSON8.md) <--> [LESSON 10](./LESSON10.md)
