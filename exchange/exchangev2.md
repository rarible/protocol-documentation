# Exchange Overview

## **Asset matching**

The purpose of this is to validate that `makeAsset` of the **left** order matches `takeAsset` from the **right** order and vice versa. New types of assets can be added without a smart contract upgrade. This is done using a custom IAssetMatcher.

There are possible improvements to the protocol using these custom matcher contracts such as:

* Support for parametric assets. For example, the user can put an order to exchange 10ETH for any NFT from a popular collection.
* Support for NFT bundles.

## **Order Structure**

**Order**:

* **maker**: address.
* **makeAsset**: Asset.
* **taker**: address \(can be zero, then anyone can fill the order\).
* **takeAsset**: Asset.
* **salt**: uint \(random number\).
* **start**: uint \(before this date order can't be filled\).
* **end**: uint \(after this date order can't be filled\).
* **dataType**: bytes4 \(type of data type, usually hash of some string, e.g.: "v1", "v2"\).
* **data**: bytes \(generic data, can be anything, extendable part of the order\).

**Asset**:

* **assetType**: AssetType \(defines type of asset - ETH, specific ERC20 token, specific ERC721 NFT etc.\).
* **amount**: uint.

**AssetType**:

* **tp**: bytes4 \(type of asset type: ETH, ERC20, ERC721 etc.\).
* **data**: bytes \(generic data, describes asset type, eg: token address for ERC20, token + tokenId for ERC721\).

## **Order validation**

* Check the start/end date of the orders.
* Check if the taker of the order is blank or taker = `order.taker`
* Check if the order is signed by its maker or maker of the order is executing the transaction.
* If the maker of the order is a contract, then an ERC-1271 check is performed.

{% hint style="info" %}
Currently, only off-chain orders are supported, this part of the smart contract can be easily updated to support on-chain order books.
{% endhint %}

## **Order execution**

Order execution is done by TransferManager. There are 2 variants:

* SimpleTransferManager \(it simply transfers assets from maker to taker and vice versa\).
* RaribleTransferManager \(sophisticated version, it takes into account protocol commissions, royalties, etc\).

{% hint style="success" %}
There are plans to extend RaribleTransferManager to support more royalty schemes and add new features like custom fees, multiple order beneficiaries.
{% endhint %}

This part of the algorithm can be extended with a custom ITransferExecutor. In the future, new executors will be added to support new asset types, for example, executor for handling bundles can be added.

{% hint style="info" %}
Possible improvements:

* Support bundles.
* Support random boxes.
{% endhint %}

## **Fees**

RaribleTransferManager supports these types of fees:

* Protocol fees \(These fees are taken from both sides of the deal\).
* Origin fees \(Origin and origin fee is set for every order. it can be different for two orders involved\).
* Royalties \(Authors of the work will receive part of each sale\).

**Fees calculation, fee side**

To take a fee we need to calculate, what side of the deal can be used as money. There is a simple algorithm to do it:

* If ETH is from any side of the deal, it's used.
* If not, then if ERC-20 is in the deal, it's used.
* If not, then if ERC-1155 is in the deal, it's used.
* Otherwise, the fee is not taken \(for example, two ERC-721 are involved in the deal\).

When we established, what part of the deal can be treated as money, then we can establish, that

* The Buyer is the side of the deal who owns the money.
* The Seller is the other side of the deal.

Then the total amount of the asset \(money side\) should be calculated

* Protocol fee is added on top of the filled amount.
* The origin fee of the buyer's order is added on top too.

If the buyer is using an ERC-20 token for payment, then he must approve at least this calculated amount of tokens.

If the buyer is using ETH, then he must send this calculated amount of ETH with the transaction.

