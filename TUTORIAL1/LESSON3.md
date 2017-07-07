# LESSON 3 Referencing Securities

今回は銘柄を設定する方法を解説します。
Quantopianではティッカーシンボルを文字列で指定する方法ではなく、銘柄のインスタンスを作成する必要があります。このインスタンスには銘柄名やSIDの他、取引所の情報等、さまざまな情報が含まれています。

## sid()
`sid()` 関数の引数にSIDを渡すことで対象となる銘柄のインスタンスを作成できます。
下記の例ではアップル社のインスタンスを作成しています。

```python
def initialize(context):
    print(sid(24))
```

後述するsymbolを用いればティッカーシンボルを指定することができますが、`sid()` を使う利点として、ディッカーシンボルは変更される可能性があることに対して、SIDは変更されないことです。SIDは銘柄名を連想できないですが、下記のように `context` オブジェクトに設定することで識別がしやすくなります。

```python
def initialize(context):
    context.aapl = sid(24)
    print(context.aapl)
```

## symbol()
引数にティッカーシンボルを指定することで、銘柄のインスタンスを作成します。
下記の例ではアップル社のインスタンスを作成しています。

```python
def initialize(context):
    print(symbol('AAPL'))
```

ティッカーシンボルが変更された銘柄については、異なる名前が出力されることがあります。

```python
def initialize(context):
    print(symbol('UA'))
```

```
1970-01-01 09:00  PRINT Equity(27822 [UAA])
End of logs.
```

`set_symbol_lookup_date()` 関数を用いて古い名前を参照することもできますが、この方法は推奨されません。

```python
def initialize(context):
    set_symbol_lookup_date('2017-01-01')
    print(symbol('UAA'))
```

[LESSON 1,2](./LESSON1_2.md) <--> [LESSON 4](./LESSON4.md)
