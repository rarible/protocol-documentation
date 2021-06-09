# Order Discovery

## Search orders

{% hint style="info" %}
In order to get latest information about API, please follow [OpenAPI doc](https://api-staging.rarible.com/protocol/ethereum/order/indexer/v0.1/swagger/webjars/swagger-ui/index.html?configUrl=/protocol/ethereum/order/indexer/v0.1/swagger/v3/api-docs/swagger-config#/order-controller/searchOrders)
{% endhint %}

https://api-staging.rarible.com/protocol/ethereum/order/indexer/v0.1/orders/search

Query parameters:

* size - how many items you want to get
* continuation - send this parameter to fetch next portion of data \(you can find continuation value in the server response\)

Body: it's a json with filter.

```typescript
type Address = string // hex string with address
type TokenId = string // hex string or string with bigint
type BaseOrderFilter = { origin: Address } // origin - is the address of an agent who submitted the order
type OrderFilter = BaseOrderFilter & ( 
    { "@type": "sell" } // all orders for selling NFTs
  | { "@type": "sell_by_maker", maker: Address } // orders for selling NFTs (which are sold by selected user - maker)
  | { "@type": "sell_by_item", token: Address, tokenId: TokenId } // orders for selling selected NFT 
  | { "@type": "sell_by_collection", collection: Address } // what NFTs are sold from specific collection
  | { "@type": "bid_by_item", token: Address, tokenId: TokenId } // bids for specific NFT
  | { "@type": "bid_by_maker", maker } // bids by maker - you can find your own bids for example
)
```

Here is an example with curl:

```text
curl \
  --header "Content-Type: application/json" \
  --request POST \
  --data '{ "@type": "sell" }' \
  https://api-staging.rarible.com/protocol/ethereum/order/indexer/v0.1/orders/search
```

The other option is to find the order using a get request with the order hash \(Which is returned when the order is created\).

{% api-method method="get" host="http://api-staging.rarible.com/protocol/ethereum/order/indexer/v1/orders/" path=":hash" %}
{% api-method-summary %}
Get Order
{% endapi-method-summary %}

{% api-method-description %}
Searching for an order via the orders API
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="hash" type="string" required=true %}
Order hash
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "maker": "string",
  "make": {
    "type": {
      "token": "string",
      "tokenId": 0
    },
    "value": 0
  },
  "take": {
    "type": {
      "token": "string",
      "tokenId": 0
    },
    "value": 0
  },
  "fill": 0,
  "stock": 0,
  "canceled": true,
  "salt": {},
  "data": {
    "token": "string",
    "tokenId": 0
  },
  "signature": {},
  "createdAt": "2021-03-17T15:06:01.283Z",
  "lastUpdateAt": "2021-03-17T15:06:01.283Z",
  "hash": {}
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

Below is an example call to getting the orders via hash

```javascript
var axios = require('axios');
var data = '';

var config = {
  method: 'get',
  url: 'https://api-staging.rarible.com/protocol/ethereum/order/indexer/v1/orders/0x438f5a4f9809285c722cfe5991f89b603d2d605a4e739831b81d0f622bdf1f37',
  data: data
};

axios(config).then(function (response) {
  console.log(JSON.stringify(response.data));
}).catch(function (error) {
  console.log(error);
});
```

