---
description: >-
  This Documentation will give you a very brief overview of creating an item
  using Rarible Protocol.
---

# Asset Creation

## Asset Introduction

Rarible protocol has two asset types, ERC721, and ERC1155, the main difference between them is that ERC721 creates unique 1 of 1 item, whereas ERC1155, allows the user to create an item with multiple editions \(The maximum amount of editions is 2\*\*256 - 1\).  
  
You can find the protocol smart contracts [here.](https://github.com/rariblecom/protocol-contracts)

#### Rinkeby

#### Asset Contract ERC721 [Etherscan link ↗](https://rinkeby.etherscan.io/address/0x6ede7f3c26975aad32a475e1021d8f6f39c89d82)

```text
0x6ede7f3c26975aad32a475e1021d8f6f39c89d82
```

#### Asset Contract ERC1155 [Etherscan link ↗](https://rinkeby.etherscan.io/address/0x1AF7A7555263F275433c6Bb0b8FdCD231F89B1D7)

```text
0x1AF7A7555263F275433c6Bb0b8FdCD231F89B1D7
```

## Asset Types Explained

### ERC721 & ERC1155

With the new ERC721 & ERC1155 Asset contracts we no longer call the mint function directly, instead, we implement lazy minting via the mintAndTransfer function. Lazy minting allows the item to be created and store off-chain until someone purchases/transfers this item, only at this point does the item get created on-chain.

**ERC721 Mint Function Signature**

```text
mintAndTransfer(LibERC721LazyMint.Mint721Data memory data, address to)
```

```text
struct Mint721Data {
 uint tokenId;
 string uri;
 LibPart.Part[] creators;
 LibPart.Part[] royalties;
 bytes[] signatures;
}
```

```javascript
    // ABI for ERC-721 mintAndTransfer
    {
      "inputs": [
        {
          "components": [
            {
              "internalType": "uint256",
              "name": "tokenId",
              "type": "uint256"
            },
            {
              "internalType": "string",
              "name": "uri",
              "type": "string"
            },
            {
              "components": [
                {
                  "internalType": "address payable",
                  "name": "account",
                  "type": "address"
                },
                {
                  "internalType": "uint256",
                  "name": "value",
                  "type": "uint256"
                }
              ],
              "internalType": "struct LibPart.Part[]",
              "name": "creators",
              "type": "tuple[]"
            },
            {
              "components": [
                {
                  "internalType": "address payable",
                  "name": "account",
                  "type": "address"
                },
                {
                  "internalType": "uint256",
                  "name": "value",
                  "type": "uint256"
                }
              ],
              "internalType": "struct LibPart.Part[]",
              "name": "royalties",
              "type": "tuple[]"
            },
            {
              "internalType": "bytes[]",
              "name": "signatures",
              "type": "bytes[]"
            }
          ],
          "internalType": "struct LibERC721LazyMint.Mint721Data",
          "name": "data",
          "type": "tuple"
        },
        {
          "internalType": "address",
          "name": "to",
          "type": "address"
        }
      ],
      "name": "mintAndTransfer",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    }
```

**ERC1155 Mint Function Signature**

```text
mintAndTransfer(LibERC1155LazyMint.Mint1155Data memory data, address to, uint256 _amount)
```

```text
struct Mint1155Data {
 uint tokenId;
 string uri;
 uint supply;
 LibPart.Part[] creators;
 LibPart.Part[] royalties;
 bytes[] signatures;
}
```

```javascript
    // ABI for ERC-1155 mintAndTransfer
    {
      "inputs": [
        {
          "components": [
            {
              "internalType": "uint256",
              "name": "tokenId",
              "type": "uint256"
            },
            {
              "internalType": "string",
              "name": "uri",
              "type": "string"
            },
            {
              "internalType": "uint256",
              "name": "supply",
              "type": "uint256"
            },
            {
              "components": [
                {
                  "internalType": "address payable",
                  "name": "account",
                  "type": "address"
                },
                {
                  "internalType": "uint256",
                  "name": "value",
                  "type": "uint256"
                }
              ],
              "internalType": "struct LibPart.Part[]",
              "name": "creators",
              "type": "tuple[]"
            },
            {
              "components": [
                {
                  "internalType": "address payable",
                  "name": "account",
                  "type": "address"
                },
                {
                  "internalType": "uint256",
                  "name": "value",
                  "type": "uint256"
                }
              ],
              "internalType": "struct LibPart.Part[]",
              "name": "royalties",
              "type": "tuple[]"
            },
            {
              "internalType": "bytes[]",
              "name": "signatures",
              "type": "bytes[]"
            }
          ],
          "internalType": "struct LibERC1155LazyMint.Mint1155Data",
          "name": "data",
          "type": "tuple"
        },
        {
          "internalType": "address",
          "name": "to",
          "type": "address"
        },
        {
          "internalType": "uint256",
          "name": "_amount",
          "type": "uint256"
        }
      ],
      "name": "mintAndTransfer",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    }
```

{% tabs %}
{% tab title="tokenId" %}
tokenId must be supplied as an uint256, this is a unique identifying number for the token. The tokenId typically is made up of two sections, the first 20 bytes in the users' address and the next 12 bytes can be any random number. We will provide an API to allow you to get the next free available ID.
{% endtab %}

{% tab title="uri" %}
This is the suffix for the tokenURI, typically our prefix would be `ipfs://`
{% endtab %}

{% tab title="supply \(ERC1155 Only\)" %}
supply should be supplied as an uint256, this is the number of copies \(Editions\) of this token that exist. \(Maximum value is 2\*\*256 - 1\).
{% endtab %}

{% tab title="creators" %}
creators is supplied as an array of LibPart.Part structures, this array should contain all the addresses \(with their respective part of the creation - in basis points\) who are considered the authors/creators of this token. The address array is public and can be queried by anyone.  
Sum of fields value in this array should be 10000 \(100% in basis points\)
{% endtab %}

{% tab title="royalties" %}
royalties are an array of addresses and values. The fees array is public and can be queried by anyone. Values are specified in basis points. So for example, 200 means 2%
{% endtab %}

{% tab title="signatures" %}
Signatures is an array of wallet signatures for this transaction from every creator, the only exception to this is when the creator sends the mint transaction - then empty signature can be passed for the executing transaction creator.
{% endtab %}
{% endtabs %}

### Uploading the image to IPFS

The image needs to be hosted on IPFS, at Rarible we use pinata, below is a NodeJS example of uploading an image using their API.

```javascript
const axios = require("axios");
const fs = require("fs");
const FormData = require("form-data");

export const pinFileToIPFS = (pinataApiKey, pinataSecretApiKey) => {
  const url = `https://api.pinata.cloud/pinning/pinJSONToIPFS`;
  let data = new FormData();
  
  data.append("file", fs.createReadStream("./yourfile.png"));
  
  return axios.post(url, data, {
      headers: {
        "Content-Type": `multipart/form-data; boundary= ${data._boundary}`,
        pinata_api_key: pinataApiKey,
        pinata_secret_api_key: pinataSecretApiKey,
      },
    })
    .then(function (response) {
      console.log(repsonse.IpfsHash);
    })
    .catch(function (error) {
      console.log(error)
    });
};

```

This will return our IPFS CID, the full response looks like this:

```javascript
{
    IpfsHash: // This is the IPFS multi-hash provided back for your content,
    PinSize: // This is how large (in bytes) the content you just pinned is,
    Timestamp: // This is the timestamp for your content pinning (represented in ISO 8601 format)
}
```

### Creating our NFT's Metadata

Now that we have our IPFS CID \(Called hash here on out\), we can begin constructing our NFT's Metadata file that will be linked to the NFT on-chain. Below is a Metadata file with an explanation of the key and its value.

```javascript
{
   "name": /* NFT Name - This must be a string */,
   "description": /* Description of the NFT - This must be a string */,
   "image": /*  IPFS Hash to our content, this must be prefixed with "ipfs://ipfs/{{ IPFS_HASH ))" - This must be a string */,
   "external_url": /* This is the link to Rarible which we currently don't have, we can fill this in shortly */,
   "animation_url": /* IPFS Hash just as image field, but it allows every type of multimedia files. Like mp3, mp4 etc */,
   // the below section is not needed.
   "attributes": [
      {
         "key": /* Key name - This must be a string */,
         "trait_type": /* Trait name - This must be a string */,
         "value": /* Key Value - This must be a string */
      }
   ]
}
```

### Adding Generated Metadata to IPFS

First, we need to make sure our `external_url` is a Rarible link. We can calculate this link based on the tokenId we create \(The tokenId typically is made up of two sections, the first 20 bytes in the users' address and the next 12 bytes can be any random number. We will provide an API to allow you to get the next free available ID.\), for this example, our external\_url must be the collection address + tokenId  and it will look like this

```javascript
"external_url": "https://app.rarible.com/0x60f80121c31a0d46b5279700f9df786054aa5ee5:123913"
```

_**Notice we use the collection address and value address from our previous call to the tokens endpoint.**_

Now we need to post our NFT's Metadata to IPFS below is an example of how to do this:

```javascript
var axios = require('axios');
var data = JSON.stringify({"name":"Test NFT","description":"Test NFT","image":"ipfs://ipfs/QmW4P1Mgoka8NRCsFAaJt5AaR6XKF6Az97uCiVtGmg1FuG/image.png","external_url":"https://app.rarible.com/0x60f80121c31a0d46b5279700f9df786054aa5ee5:123913","attributes":[{"key":"Test","trait_type":"Test","value":"Test"}]});

var config = {
  method: 'post',
  url: 'https://api.pinata.cloud/pinning/pinFileToIPFS',
  headers: { 
    'pinata_api_key': // KEY_HERE, 
    'pinata_secret_api_key': // SECRET_KEY_HERE, 
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config).then(function (response) {
  console.log(JSON.stringify(response.data));
}).catch(function (error) {
  console.log(error);
});
```

Our Result will look something similar to this:

```javascript
{
    "IpfsHash": "QmNybufJtuvWCZ355HGejvKfUXK8VeLcPA5G7CxT9MXJJp",
    "PinSize": 290,
    "Timestamp": "2021-02-10T14:06:09.255Z"
}
```

Make a note of your new `IpfsHash` since this is now the hash we need to attach to our NFT.

### Minting the NFT

Now we have everything we need to mint our NFT, below you will find a NodeJS, ERC721 example used to mint the NFT.

```javascript
import HDWalletProvider from "@truffle/hdwallet-provider"
import Web3 from "web3"
import axios from "axios"

const config = {
	private: "YOUR PK", // DO NOT PUSH PRIVATE KEY IN PUBLIC REPO! YOU PROBABLY SHOULD USE ENV VARIABLES HERE
	rpc: "https://rinkeby.infura.io/v3/84653a332a3f4e70b1a46aaea97f0435",
	erc721ContractAddress: "0x25646B08D9796CedA5FB8CE0105a51820740C049",
	apiBaseUrl: "https://api-staging.rarible.com"
}

const client = axios.create({
	baseURL: "https://api-staging.rarible.com"
})

const maker = new HDWalletProvider(config.private, config.rpc)
const web3 = new Web3(maker)

const contractAbi = JSON.parse(`[{ "inputs": [ { "components": [ { "internalType": "uint256", "name": "tokenId", "type": "uint256" }, { "internalType": "string", "name": "uri", "type": "string" }, { "internalType": "address[]", "name": "creators", "type": "address[]" }, { "components": [ { "internalType": "address payable", "name": "account", "type": "address" }, { "internalType": "uint256", "name": "value", "type": "uint256" } ], "internalType": "struct LibPart.Part[]", "name": "royalties", "type": "tuple[]" }, { "internalType": "bytes[]", "name": "signatures", "type": "bytes[]" } ], "internalType": "struct LibERC721LazyMint.Mint721Data", "name": "data", "type": "tuple" }, { "internalType": "address", "name": "to", "type": "address" } ], "name": "mintAndTransfer", "outputs": [], "stateMutability": "nonpayable", "type": "function" }]`)
const contract = new web3.eth.Contract(contractAbi, config.erc721ContractAddress)

performMint(maker.getAddress())
	.then(x => console.log("Hash:", x))
	.catch(error => console.log("Mint error", error))

async function performMint(maker: string) {
	const nonce = await getNonce(config.erc721ContractAddress, maker)

	const invocation = contract.methods
		.mintAndTransfer(
			[
				nonce,
				"/ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp",
				[maker],
				[],
				["0x0000000000000000000000000000000000000000000000000000000000000000"]
			],
			maker
		)
		return new Promise(async (resolve, reject) => {
			web3.eth.sendTransaction({
					data: invocation.encodeABI(),
					to: config.erc721ContractAddress,
					from: maker,
					chainId: await web3.eth.net.getId(),
					gasPrice: "5000000000",
					gas: "10000000"
			})
			.once("transactionHash", resolve)
			.once("error", reject)
	}) 
}

async function getNonce(token: string, minter: string) {
	const res = await client.get(`protocol/ethereum/nft/indexer/v0.1/collections/${token}/generate_token_id?minter=${minter}`)
	return res.data.tokenId
}

```

**YAY! Your NFT is now minted! Visit the next section on** [**how to create a sell order**](../exchange/creating-a-sell-order.md) **or to check if your Asset is indexed you can** [**view this page**](asset-discovery.md)**.**

