# Tiny, but extremely powerful 


Provided code allows to call every binance api method and is easy ti integrate.
Also, it will (highly likely) support most methods that may appear in future.
It was tested by many users ([Bablofil website](https://bablofil.ru) subscribers) and proved it's functionality.
Below you can find all the source and examples.


```
import ssl
import time
import json
import urllib
import hmac, hashlib
import requests

from urllib.parse import urlparse, urlencode
from urllib.request import Request, urlopen

class Binance():

    methods = {
            # public methods
            'ping':             {'url':'api/v1/ping', 'method': 'GET', 'private': False},
            'time':             {'url':'api/v1/time', 'method': 'GET', 'private': False},
            'exchangeInfo':     {'url':'api/v1/exchangeInfo', 'method': 'GET', 'private': False},
            'depth':            {'url': 'api/v1/depth', 'method': 'GET', 'private': False},
            'trades':           {'url': 'api/v1/trades', 'method': 'GET', 'private': False},
            'historicalTrades': {'url': 'api/v1/historicalTrades', 'method': 'GET', 'private': False},
            'aggTrades':        {'url': 'api/v1/aggTrades', 'method': 'GET', 'private': False},
            'klines':           {'url': 'api/v1/klines', 'method': 'GET', 'private': False},
            'ticker24hr':       {'url': 'api/v1/ticker/24hr', 'method': 'GET', 'private': False},
            'tickerPrice':      {'url': 'api/v3/ticker/price', 'method': 'GET', 'private': False},
            'tickerBookTicker': {'url': 'api/v3/ticker/bookTicker', 'method': 'GET', 'private': False},
            # private methods
            'createOrder':      {'url': 'api/v3/order', 'method': 'POST', 'private': True},
            'testOrder':        {'url': 'api/v3/order/test', 'method': 'POST', 'private': True},
            'orderInfo':        {'url': 'api/v3/order', 'method': 'GET', 'private': True},
            'cancelOrder':      {'url': 'api/v3/order', 'method': 'DELETE', 'private': True},
            'openOrders':       {'url': 'api/v3/openOrders', 'method': 'GET', 'private': True},
            'allOrders':        {'url': 'api/v3/allOrders', 'method': 'GET', 'private': True},
            'account':          {'url': 'api/v3/account', 'method': 'GET', 'private': True},
            'myTrades':         {'url': 'api/v3/myTrades', 'method': 'GET', 'private': True},
            # wapi
            'depositAddress':   {'url': '/wapi/v3/depositAddress.html', 'method':'GET', 'private':True},
            'withdraw':   {'url': '/wapi/v3/withdraw.html', 'method':'POST', 'private':True},
            'depositHistory': {'url': '/wapi/v3/depositHistory.html', 'method':'GET', 'private':True},
            'withdrawHistory': {'url': '/wapi/v3/withdrawHistory.html', 'method':'GET', 'private':True},
            'withdrawFee': {'url': '/wapi/v3/withdrawFee.html', 'method':'GET', 'private':True},
            'accountStatus': {'url': '/wapi/v3/accountStatus.html', 'method':'GET', 'private':True},
            'systemStatus': {'url': '/wapi/v3/systemStatus.html', 'method':'GET', 'private':True},
    }
    
    def __init__(self, API_KEY, API_SECRET):
        self.API_KEY = API_KEY
        self.API_SECRET = bytearray(API_SECRET, encoding='utf-8')
        self.shift_seconds = 0

    def __getattr__(self, name):
        def wrapper(*args, **kwargs):
            kwargs.update(command=name)
            return self.call_api(**kwargs)
        return wrapper

    def set_shift_seconds(self, seconds):
        self.shift_seconds = seconds
        
    def call_api(self, **kwargs):

        command = kwargs.pop('command')
        api_url = 'https://api.binance.com/' + self.methods[command]['url']

        payload = kwargs
        headers = {}
        
        payload_str = urllib.parse.urlencode(payload)
        if self.methods[command]['private']:
            payload.update({'timestamp': int(time.time() + self.shift_seconds - 1) * 1000})
            payload_str = urllib.parse.urlencode(payload).encode('utf-8')
            sign = hmac.new(
                key=self.API_SECRET,
                msg=payload_str,
                digestmod=hashlib.sha256
            ).hexdigest()

            payload_str = payload_str.decode("utf-8") + "&signature="+str(sign) 
            headers = {"X-MBX-APIKEY": self.API_KEY}

        if self.methods[command]['method'] == 'GET':
            api_url += '?' + payload_str

        response = requests.request(method=self.methods[command]['method'], url=api_url, data="" if self.methods[command]['method'] == 'GET' else payload_str, headers=headers)

            
        if 'code' in response.text:
            print(response.text)
        return response.json()
```

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


If you happen to have some time-related issues, like "Timestamp for this request was 1000ms ahead of the server's time."  or "Timestamp for this request is outside of the recvWindow", you should synchronize your local time with a ntp server or use "set_shift_seconds" method to adjust difference between your local time and Binance's;


Here's an example:


```
local_time = int(time.time())
server_time = int(limits['serverTime'])//1000


shift_seconds = server_time-local_time
bot.set_shift_seconds(shift_seconds)
``` 
