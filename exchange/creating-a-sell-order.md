# Creating A Sell Order

## Creating an order

**Step 1. Approval**

We need to call approval on the transfer proxy contract.

{% hint style="danger" %}
The approval on the transfer proxy only needs to be called if a previous approval does not exist.
{% endhint %}

**Step 2. Creating signature**

Before creating the actual order we have to create our signature that will be used for transaction processing

The signature must follow EIP-712 that allows us to sign data structures instead of raw messages.

{% hint style="info" %}
Here's quick example and concept how it works. The full working example can be explored here: [https://github.com/evgenynacu/sign-typed-data](https://github.com/evgenynacu/sign-typed-data)
{% endhint %}

```typescript
export type OrderAssetType = {
	assetClass: string
	data: string
}

export type OrderAsset = {
	assetType: OrderAssetType
	value: string
}

export type Order = {
	maker: string
	makeAsset: OrderAsset
	taker: string
	takeAsset: OrderAsset
	salt: string
	start: string
	end: string
	data: string
	dataType: string
}

// RINKEBY
const signable = {
	types: {
		AssetType: [
			{ name: 'assetClass', type: 'bytes4' },
			{ name: 'data', type: 'bytes' },
		],
		Asset: [
			{ name: 'assetType', type: 'AssetType' },
			{ name: 'value', type: 'uint256' },
		],
		Order: [
			{ name: 'maker', type: 'address' },
			{ name: 'makeAsset', type: 'Asset' },
			{ name: 'taker', type: 'address' },
			{ name: 'takeAsset', type: 'Asset' },
			{ name: 'salt', type: 'uint256' },
			{ name: 'start', type: 'uint256' },
			{ name: 'end', type: 'uint256' },
			{ name: 'dataType', type: 'bytes4' },
			{ name: 'data', type: 'bytes' },
		],
	},
	domain: {
     name: "Exchange",
     version: "2",
     chainId: 4,
     verifyingContract: "0x43162023C187662684abAF0b211dCCB96fa4eD8a"
  },
	message: Order,
	primaryType: "Order"
}

// use this function to sign structure from above
function signTypedData(web3: any, from: string, data: any) {
  const msgData = JSON.stringify(data);
  return new Promise<any>((resolve, reject) => {
    function cb(err: any, result: any) {
      if (err) return reject(err);
      if (result.error) return reject(result.error);
      const sig = result.result;
      const sig0 = sig.substring(2);
      const r = "0x" + sig0.substring(0, 64);
      const s = "0x" + sig0.substring(64, 128);
      const v = parseInt(sig0.substring(128, 130), 16);
      resolve({ data, sig, v, r, s });
    }
  
  return web3.currentProvider.sendAsync({
      method: "eth_signTypedData_v3",
      params: [from, msgData],
      from 
    }, cb);
  })
}

```

**Step 3. Send order with signature to our API**

To Create an order we first need to build the order via a Put request to our [http://api-staging.rarible.com/protocol/ethereum/order/indexer/v1/orders](http://api-staging.rarible.com/protocol/ethereum/order/indexer/v1/orders) endpoint

The order should be built as an Object like below

```typescript
export type AssetTypeForm = {
  "assetClass": "ETH"
} | {
  "assetClass": "ERC721"
  contract: string
  tokenId: string
} | {
  "assetClass": "ERC20"
	contract: string
  tokenId: string
} | {
	"assetClass": "ERC1155"
	contract: string
	tokenId: string
}

export type AssetForm = {
	assetType: AssetTypeForm
	value: string
}

export type OrderDataV1Form = {
	dataType: "RARIBLE_V2_DATA_V1",
	payouts: Part[],
	originFees: Part[]
}
export type OrderDataForm = OrderDataV1Form

export type OrderForm = {
	type: "RARIBLE_V1" | "RARIBLE_V2"
	maker: string,
	make: AssetForm,
	taker?: string,
	take: AssetForm,
	salt: string,
	start?: string,
	end?: string,
	data: OrderDataForm,
	signature: string
}

// See Step 2. to create this signature
const signature = "0xafbfc3bc623517d5a8876a082485f1a3051185a08880db575e1ab9446caff149722e83dc9c61d77f0a8cbb2d16f763d8f3a270ca02d2bc76750eb72c1d053fcd1b"
const form: OrderForm = {
  type: "RARIBLE_V2"
  maker: "0xc66d094ed928f7840a6b0d373c1cd825c97e3c7c",
  make: {
    assetType: {
      assetClass: "ERC721",
      token: "0x25646b08d9796ceda5fb8ce0105a51820740c049",
      tokenId: "0xc66d094ed928f7840a6b0d373c1cd825c97e3c7c00000000000000000000000a"
    },
    value: "1",
  },
  take: {
    assetType: {
      assetClass: "ETH"
    },
    value: "10000000000000000",
  },
  salt: "2",
  data: {
    dataType: "RARIBLE_V2_DATA_V1",
    payouts: [],
    originFees: []
  },
  signature: signature
}
```

{% hint style="info" %}
In order to get latest information about API, please follow [OpenAPI doc](https://api-reference.rarible.com/#operation/createOrUpdateOrder) for createOrUpdateOrder
{% endhint %}

