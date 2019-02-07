## Python 3+ interface for binance

examples:


### check connection 

```
from binance_api import Binance
bot = Binance(
    API_KEY='D7...Ejj',
    API_SECRET='gwQ...u3A'
)
print(bot.ping())
```

### get depth

```
from binance_api import Binance
bot = Binance(
    API_KEY='D7...Ejj',
    API_SECRET='gwQ...u3A'
)
print('depth', bot.depth(
    symbol='BNBBTC',
    limit=5
))
```

### Place order

```
from binance_api import Binance
bot = Binance(
    API_KEY='D7...Ejj',
    API_SECRET='gwQ...u3A'
)
# Buy 0.1 LTC with BTC
print('createOrder', bot.createOrder(
    symbol='LTCBTC',
    recvWindow=5000,
    side='BUY',
    type='LIMIT',
    timeInForce='GTC',
    quantity=0.1,
    price=0.1
))
```

### Get order info

```
from binance_api import Binance
bot = Binance(
    API_KEY='D7...Ejj',
    API_SECRET='gwQ...u3A'
)
print('orderInfo', bot.orderInfo(
    orderId=123123,
    symbol='LTCBTC',
))
```

### Get account info

```
from binance_api import Binance
bot = Binance(
    API_KEY='D7...Ejj',
    API_SECRET='gwQ...u3A'
)

print('account', bot.account())
```

ETC..

### More info:


You just take methods names and params from from [official api](https://github.com/binance-exchange/binance-official-api-docs/blob/master/rest-api.md) and use it with the provided interface.


Every response will contain json as described in official docs, or an Exception will be thrown.


You can get more examples in Russian [here](https://bablofil.ru/binance-api/)
