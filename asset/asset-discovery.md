# Asset Discovery

## [Search Items](https://api-staging.rarible.com/protocol/ethereum/nft/indexer/v0.1/swagger/webjars/swagger-ui/index.html?configUrl=/protocol/ethereum/nft/indexer/v0.1/swagger/v3/api-docs/swagger-config#/item-controller/searchItems)

{% hint style="info" %}
In order to get latest information about API, please follow [OpenAPI doc](https://api-reference.rarible.com/#tag/item-controller) for item-controller part
{% endhint %}

There are some controllers to query items:

* [get all items](https://api-reference.rarible.com/#operation/getAllItems)
* [get items by owner](https://api-reference.rarible.com/#operation/getItemsByOwner)
* [get items by collection](https://api-reference.rarible.com/#operation/getItemsByCollection)
* other controllers in [item-controller](https://api-reference.rarible.com/#tag/item-controller) section of the [api-reference](https://api-reference.rarible.com/)

These controllers have common parameters:

* size - how many items you want to get
* continuation - send this parameter to fetch next portion of data \(you can find continuation value in the server response\)
* also they have different query parameters for each. Please, see [api-reference](https://api-reference.rarible.com/)

{% api-method method="get" host="http://api.rarible.com/protocol/ethereum/nft/indexer/v1/items/" path=":itemId/meta" %}
{% api-method-summary %}
Item Metadata
{% endapi-method-summary %}

{% api-method-description %}
Displays all item Metadata \(Stored on IPFS\)
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="itemId" type="string" required=true %}
The itemId is built by using collectionAddress:tokenId - An example is 0x60f80121c31a0d46b5279700f9df786054aa5ee5:21
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "name": "string",
  "description": "string",
  "image": "string",
  "external_url": "string",
  "animation_url": "string",
  "attributes": {}
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="get" host="http://api.rarible.com/protocol/ethereum/nft/indexer/v1/items/" path=":itemId" %}
{% api-method-summary %}
Item Data
{% endapi-method-summary %}

{% api-method-description %}
Displays Item data such as Royalties, Unlockable content, etc.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="itemId" required=true type="string" %}
The itemId is built by using collectionAddress:tokenId - An example is 0x60f80121c31a0d46b5279700f9df786054aa5ee5:21
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "id": "string",
  "token": "string",
  "tokenId": 0,
  "unlockable": true,
  "creator": "string",
  "supply": 0,
  "owners": [
    "string"
  ],
  "royalties": [
    {
      "recipient": "string",
      "value": 0
    }
  ]
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="danger" %}
If you wish to build an order for an ERC1155, it is important you also record the amount returned by the query.
{% endhint %}

**Visit the next section on** [**how to create a sell order**](../exchange/creating-a-sell-order.md)**!**

