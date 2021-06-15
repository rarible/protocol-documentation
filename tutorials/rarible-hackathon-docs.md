# Rarible Hackathon Docs

### Intro to Rarible Protocol

Rarible's protocol includes contracts, standards, and APIs for:

**Minting**

* Minting \(Both ERC721 & ERC1155\)
* Lazy Minting - Token metadata & minting signatures are stored on Rarible back-end until a buyer fills the order. Then a `mintAndTransfer` call is made on chain when the order is filled

**Exchange** \(Buy, Sell, Bid\)

* Signature based order matching using an off chain order book
* Asset discovery is off chain, then buyers or sellers can submit both sides of an order, including relevant signatures to execute a transfer.
* Asset owners must `approve` the Rarible exchange to transfer on their behalf
* Multiple asset types are supported to fill orders \(ERC721, ERC1155, ERC20\)
* Bidding is supported

**Indexer**

* Rarible API exposes ways to query NFTs indexed on Ethereum
* Rarible API exposes ways to create orders

#### API Reference

[https://api-reference.rarible.com/](https://api-reference.rarible.com/)

| Base URL | Network | Chain ID |
| :--- | :--- | :--- |
| [https://api.rarible.com/protocol/v0.1/](https://api.rarible.com/protocol/v0.1/) | Mainnet | 1 |
| [https://api-staging.rarible.com/protocol/v0.1/](https://api-staging.rarible.com/protocol/v0.1/) | Rinkeby | 4 |
| [https://api-dev.rarible.com/protocol/v0.1/](https://api-dev.rarible.com/protocol/v0.1/) | Ropsten | 3 |

### How to use the starter app

The Rarible starter app is forked from Scaffold-Eth

[https://github.com/ipatka/scaffold-eth/tree/rarible-starter-app](https://github.com/ipatka/scaffold-eth/tree/rarible-starter-app)

* [x] Minting using the standard Rarible collection
* [x] Minting using a custom collection
* [x] Lazy minting
* [x] Creating a sell order for ETH
* [ ] Creating a sell order for an ERC20
* [ ] Creating a sell order for another NFT
* [x] Creating a matching buy order for ETH
* [ ] Creating a matching buy order for an ERC20
* [ ] Creating a matching buy order for another NFT

### Contracts

| Contract | Rinkeby Address | Ropsten Address |
| :--- | :--- | :--- |
| Asset Contract ERC721 | 0x6ede7f3c26975aad32a475e1021d8f6f39c89d82 | 0xB0EA149212Eb707a1E5FC1D2d3fD318a8d94cf05 |
| Asset Contract ERC1155 | 0x1AF7A7555263F275433c6Bb0b8FdCD231F89B1D7 | 0x6a94aC200342AC823F909F142a65232E2f052183 |
| Exchange Contract | 0x1e1B6E13F0eB4C570628589e3c088BC92aD4dB45 |  |
| NFT Transfer Proxy \(for NFT approvals\) | 0x7d47126a2600E22eab9eD6CF0e515678727779A6 | 0xf8e4ecac18b65fd04569ff1f0d561f74effaa206 |
| ERC20 Transfer Proxy \(for ERC20 approvals\) | 0x2FCE8435F0455eDc702199741411dbcD1B7606cA |  |

### Minting

Minting through Rarible Protocol contracts are now built around the new Lazy Mint feature.

**Lazy Minting**: Lazy minting allows the item to be created and stored off-chain until someone purchases/transfers this item, only at this point does the item get minted on-chain.

When minting through the protocol's asset contracts listed above, developers should call `mintAndTransfer()` for NFT creation.

Direct calls to `mint()` should be avoided and replaced with `mintAndTransfer()`. However, standard minting is still possible with this function. See **ERC721 Standard Minting** or **ERC1155 Standard Minting** for details.

#### ERC721 Overview

```text
mintAndTransfer(LibERC721LazyMint.Mint721Data memory data, address to)
```

**Mint721Data Parameter Structure**

```text
struct Mint721Data {
 uint tokenId;
 string uri;
 LibPart.Part[] creators;
 LibPart.Part[] royalties;
 bytes[] signatures;
}
```

_**Parameters**_

 tokenId

The `tokenId` must be supplied as a uint256, which is a unique identifying number for the token.

The `tokenId` is made up of two sections, the first 20 bytes is the user's address and the next 12 bytes can be any random number. [This API](https://api-reference.rarible.com/#operation/generateTokenId) will allow you to get the next available ID.

&lt;/p&gt; &lt;/details&gt;

 uri

This is the suffix for the tokenURI. The prefix for Rarible protocol contracts is `ipfs://`

* Sample IPFS uri:

`/ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp`

* Gets concatenated into the following upon minting:

`ipfs://ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp`

**Note:** If you are not storing your metadata on IPFS, you will need to create your own custom collection contract instead of using the protocol's asset contracts. See **Implementation** for details. &lt;/p&gt; &lt;/details&gt;

 creators

`creators` is an array of addresses and values. The `LibPart.Part` struct it derives from is provided below.

```text
struct Part {
    address payable account;
    uint96 value;
}
```

This array should contain all the addresses of the creators of this token with their respective ownership or contribution to the creation - in basis points. The address array is public and can be queried by anyone.

Sum of the fields value in this array should be 10000 \(100% in basis points\). Can be divided in any number of ways.

I.e. The following array, `[[0x12345..., 5000], [0x6789..., 5000]]`, associates the creation of the given NFT to 2 creators at an equal 50% distribution.

&lt;/p&gt; &lt;/details&gt;

 royalties

`royalties` is an array of addresses and values. Like `creators`, it's also derived from the `LibPart.Part` struct provided below.

```text
struct Part {
    address payable account;
    uint96 value;
}
```

The fees array is public and can be queried by anyone. Values are specified in basis points. For example, 2000 means 20%.

I.e. One address recieves 20% royalties with the following array, `[[0x12345..., 2000]]`. But more than one address can be provided to recieve royalties at specified percentages.

&lt;/p&gt; &lt;/details&gt;

 signatures

`signatures` is an array of wallet signatures for this transaction from every creator.

However, an empty signature, `[[0x]]`, can be passed if the creator is minting immediately instead of creating a Lazy Mint. The steps for standard minting are provided in **ERC721 Standard Minting**.

&lt;/p&gt; &lt;/details&gt;   


**ERC721 mintAndTransfer ABI**

```text
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

#### ERC721 Lazy Minting

If you're storing your metadata on IPFS, you can mint through the Rarible Protocol asset contracts without conflict.

Lazy minting requires the creator's signature in order to allow minting of their NFT when someone else purchases it and to retain its provenance.

We'll go through the steps of getting the creator's signature and creating a lazy mint below. You can also find various code implementations for lazy minting [here](https://github.com/ipatka/scaffold-eth/tree/rarible-starter-app), [here](https://github.com/MysteryDrop/mystery-drop/tree/master/packages/react-app/src/util), and [here](https://github.com/evgenynacu/sign-typed-data).

**For 721 \(Ropsten\)**

**Step 1**: Generate a token ID.

* Since a lazy mint is stored off-chain, it's necessary to generate a token ID through this API to ensure you get the next available token ID since there's no certainty about when the NFT will actually be minted.

GET from `https://api-dev.rarible.com/protocol/v0.1/ethereum/nft/collections/{ContractAddress}/generate_token_id?minter=${account}`

```text
// Sample Call 

const res = await fetch('https://api-dev.rarible.com/protocol/v0.1/ethereum/nft/collections/{ContractAddress}/generate_token_id?minter=${creator}').then((res) => res.json());
```

Response

```text
{
    tokenId: "10269532675691974816893214588076010230265315839066808147818573374451427049545", 
    signature: {
        r: "0x9f73810b77cee6ce00f3924091b22030ba707823bde53635d5ef2a3a1a605e8e"
        s: "0x00803ec40e179172973347ba9f1a8c8dd094e5b2e2e19504a09017f4358f2b1e"
        v: 28
    }
}
```

Get the `tokenId` from the response object.

**Step 2**: Create the Lazy Mint Request Body to be signed by the creator

```text
{
    "@type": "ERC721",
    "contract": "0xB0EA149212Eb707a1E5FC1D2d3fD318a8d94cf05",
    "tokenId": tokenId,
    "uri": "/ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp",
    "creators": [
        { 
            account: "0x1234...", 
            value: "10000" 
        }
    ],
    "royalties": [
        { 
            account: "0x1234...", 
            value: 2000 
        }
    ],
  };
```

**Step 3**: Creator signs the provided typed data, thereby granting permission to mint their NFT upon purchase.

_Note_ See [Signatures](https://hackmd.io/ktJuljjGTA2TivezBXKA5g?view#Signatures) for more details on typed data and EIP-712 and EIP-1271.

**First**, construct the typed data structure:

```text
"types": {
    "EIP712Domain" [
      {
        type: "string",
        name: "name",
      },
      {
        type: "string",
        name: "version",
      },
      {
        type: "uint256",
        name: "chainId",
      },
      {
        type: "address",
        name: "verifyingContract",
      }
    ],
    "Mint721": [
        { name: "tokenId", type: "uint256" },
        { name: "tokenURI", type: "string" },
        { name: "creators", type: "Part[]" },
        { name: "royalties", type: "Part[]" }
    ],
    "Part": [
        { name: "account", type: "address" },
        { name: "value", type: "uint96" }
    ]
},
"domain": {
    name: "Mint721",
    version: "1",
    chainId: 3,
    verifyingContract: "0xB0EA149212Eb707a1E5FC1D2d3fD318a8d94cf05"
},
"primaryType": "Mint721",
"message": {
    "@type": "ERC721",
    "contract": "0xB0EA149212Eb707a1E5FC1D2d3fD318a8d94cf05",
    "tokenId": tokenId,
    "tokenURI": "/ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp"
    "uri": "/ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp",
    "creators": [
        { 
            account: "0x1234...", 
            value: "10000" 
        }
    ],
    "royalties": [
        { 
            account: "0x1234...", 
            value: 2000 
        }
    ],
 };
```

**Then** provide the data structure above to the creator for signing.

```text
// Sample code

async function signTypedData(web3Provider, from, dataStructure) {
  const msgData = JSON.stringify(dataStructure);
  const signature = await web3Provider.send("eth_signTypedData_v4", [from, msgData]);
  const sig0 = sig.substring(2);
  const r = "0x" + sig0.substring(0, 64);
  const s = "0x" + sig0.substring(64, 128);
  const v = parseInt(sig0.substring(128, 130), 16);
  return {
    dataStructure,
    signature,
    v,
    r,
    s,
  };
}
```

**Finally**, get the `signature` from the object that the function above returns and add it as the final field of the Lazy Mint Request Body you created in Step 2.

E.g.

```text
{
    "@type": "ERC721",
    "contract": "0xB0EA149212Eb707a1E5FC1D2d3fD318a8d94cf05",
    "tokenId": tokenId,
    "uri": "/ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp",
    "creators": [
        { 
            account: "0x1234...", 
            value: "10000" 
        }
    ],
    "royalties": [
        { 
            account: "0x1234...", 
            value: 2000 
        }
    ],
    "signatures": ["0x2f1e8dd2838930f0230a9fcbb2977779838eb8dd44391af1…a6cb767fa157d5fb54c084e8ffb41be1c1bc5b6f067fd681b"]
  };
```

**Step 4**: Create your Lazy Minted NFT

POST to `https://api-dev.rarible.com/protocol/v0.1/ethereum/nft/mints`

```text
{
    "@type": "ERC721",
    "contract": "0xB0EA149212Eb707a1E5FC1D2d3fD318a8d94cf05",
    "tokenId": tokenId,
    "uri": "/ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp",
    "creators": [
        { 
            account: "0x1234...", 
            value: "10000" 
        }
    ],
    "royalties": [
        { 
            account: "0x1234...", 
            value: 2000 
        }
    ],
    "signatures": ["0x2f1e8dd2838930f0230a9fcbb2977779838eb8dd44391af1…a6cb767fa157d5fb54c084e8ffb41be1c1bc5b6f067fd681b"]
  };
```

Response

```text
{
  "id": ""0x3437df037bbbeb1aa3e417b32154bc2bb5da1c04:10269532675691974816893214588076010230265315839066808147818573374451427049549"",
  "contract": "0xB0EA149212Eb707a1E5FC1D2d3fD318a8d94cf05",
  "tokenId": tokenId,
  "creators": [
    {
      "account": "0x1234...",
      "value": 10000
    }
  ]
  "supply": 1,
  "lazySupply": 1,
  "owners": [
     "0x1234..."
  ],
  "royalties": [
    {
      "account": "0x1234...",
      "value": 2000
    }
  ],
  "pending": [
    {
      "date": "2019-08-24T14:15:22Z",
      "owner": "0x1234...",
      "from": "0x1234...",
      "contract": "0xB0EA149212Eb707a1E5FC1D2d3fD318a8d94cf05",
      "tokenId": tokenId,
      "value": 0,
      "type": "TRANSFER"
    }
  ]
}
```

You've successfully created a Lazy Minted NFT with the Rarible Protocol! 🎉   


#### ERC721 Standard Minting

You can mint NFTs through the Rarible asset contracts using `mintAndTransfer` like you would for a standard `mint` call. You just need to provide all the expected parameters seen below.

`mintAndTransfer(LibERC721LazyMint.Mint721Data memory data, address to)`

```text
struct Mint721Data {
 uint tokenId;
 string uri;
 LibPart.Part[] creators;
 LibPart.Part[] royalties;
 bytes[] signatures;
}
```

You can do so by instantiating the contract in your app and calling the function directly using `ethers.js` or `web3.js`.

_Note_ For the signature, since you are minting an NFT as a direct call and not a lazy mint, you simply pass an empty signature. E.g. `0x`.

_Note_ Royalties are set as basis point, so 1000 = 10%. [More info](https://corporatefinanceinstitute.com/resources/knowledge/finance/basis-point-beep/)

**Example**

```javascript
async function mintNow() {
    // Get a token id
    const tokenId = await fetch(`https://api-dev.rarible.com/protocol/v0.1/ethereum/nft/collections/${contractAddress}/generate_token_id?minter=${account}`);

    // Instantiate the contract
    const provider = new ethers.providers.Web3Provider(userWalletProvider);
    const signer = provider.getSigner();
    const contract = new ethers.Contract(contractAddress, abi, signer);

    // Call the function
    const tx = await contract.mintAndTransfer(
        [
          tokenId.tokenId,
          uri,
          [[creator, 5000], [creator2, 5000]], // You can assign one or add multiple creators, but the value must total 10000
          [[creator, 1000], [creator2, 1000]], // Royalties are set as basis point, so 1000 = 10%. 
          ["0x"]
        ],
        minter,
      );

      const receipt = await tx.wait();
      console.log('Minting Success', receipt);
}
```

#### ERC1155 Overview

`mintAndTransfer(LibERC1155LazyMint.Mint1155Data memory data, address to, uint256 _amount)`

**Mint1155Data Parameter Structure**

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

_**Parameters**_

 tokenId

The `tokenId` must be supplied as a uint256, which is a unique identifying number for the token.

The `tokenId` is made up of two sections, the first 20 bytes is the user's address and the next 12 bytes can be any random number. [This API](https://api-reference.rarible.com/#operation/generateTokenId) will allow you to get the next available ID.

&lt;/p&gt; &lt;/details&gt;

 uri

This is the suffix for the tokenURI. The prefix for Rarible protocol contracts is `ipfs://`

* Sample IPFS uri:

`/ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp`

* Gets concatenated into the following upon minting:

`ipfs://ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp`

**Note:** If you are not storing your metadata on IPFS, you will need to create your own custom collection contract instead of using the protocol's asset contracts. See **Implementation** for details. &lt;/p&gt; &lt;/details&gt;

 supply

`supply` should be a uint256, this is the number of copies \(or Editions\) of this token that will ever exist. \(Maximum value is 2\*\*256 - 1\).

&lt;/p&gt; &lt;/details&gt;

 creators

`creators` is an array of addresses and values. The `LibPart.Part` struct it derives from is provided below.

```text
struct Part {
    address payable account;
    uint96 value;
}
```

This array should contain all the addresses of the creators of this token with their respective ownership or contribution to the creation - in basis points. The address array is public and can be queried by anyone.

Sum of the fields value in this array should be 10000 \(100% in basis points\). Can be divided in any number of ways.

I.e. The following array, `[[0x12345..., 5000], [0x6789..., 5000]]`, associates the creation of the given NFT to 2 creators at an equal 50% distribution.

&lt;/p&gt; &lt;/details&gt;

 royalties

`royalties` is an array of addresses and values. Like `creators`, it's also derived from the `LibPart.Part` struct provided below.

```text
struct Part {
    address payable account;
    uint96 value;
}
```

The fees array is public and can be queried by anyone. Values are specified in basis points. For example, 2000 means 20%.

I.e. One address recieves 20% royalties with the following array, `[[0x12345..., 2000]]`. But more than one address can be provided to recieve royalties at specified percentages.

&lt;/p&gt; &lt;/details&gt;

 signatures

`signatures` is an array of wallet signatures for this transaction from every creator.

However, an empty signature, `[[0x]]`, can be passed if the creator is minting immediately instead of creating a Lazy Mint. The steps for standard minting are provided in **ERC1155 Standard Minting**.

&lt;/p&gt; &lt;/details&gt;   


**ERC1155 mintAndTransfer ABI**

```text
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

#### ERC1155 Lazy Minting

If you're storing your metadata on IPFS, you can mint through the Rarible Protocol asset contracts without conflict.

Lazy minting requires the creator's signature in order to allow minting of their NFT when someone else purchases it and to retain its provenance.

We'll go through the steps of getting the creator's signature and creating a lazy mint below. You can also find various code implementations for lazy minting [here](https://github.com/ipatka/scaffold-eth/tree/rarible-starter-app), [here](https://github.com/MysteryDrop/mystery-drop/tree/master/packages/react-app/src/util), and [here](https://github.com/evgenynacu/sign-typed-data).

**For 1155 \(Ropsten\)**

**Step 1**: Generate a token ID.

* Since a lazy mint is stored off-chain, it's necessary to generate a token ID through this API to ensure you get the next available token ID since there's no certainty about when the NFT will actually be minted.

GET from `https://api-staging.rarible.com/protocol/v0.1/ethereum/nft/collections/{ContractAddress}/ generate_token_id?minter=${account}`

```text
// Sample Call 

const res = await fetch('https://api-dev.rarible.com/protocol/v0.1/ethereum/nft/collections/{ContractAddress}/generate_token_id?minter={creator}').then((res) => res.json());
```

Response

```text
{
    tokenId: "10269532675691974816893214588076010230265315839066808147818573374451427049545", 
    signature: {
        r: "0x9f73810b77cee6ce00f3924091b22030ba707823bde53635d5ef2a3a1a605e8e"
        s: "0x00803ec40e179172973347ba9f1a8c8dd094e5b2e2e19504a09017f4358f2b1e"
        v: 28
    }
}
```

Get the `tokenId` from the response object.

**Step 2**: Create the Lazy Mint Request Body to be signed by the creator

```text
{
    "@type": "ERC1155",
    "contract": "0x6a94aC200342AC823F909F142a65232E2f052183 ",
    "tokenId": tokenId,
    "uri": "/ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp",
    "supply": 10,
    "creators": [
        { 
            account: "0x1234...", 
            value: "10000" 
        }
    ],
    "royalties": [
        { 
            account: "0x1234...", 
            value: 2000 
        }
    ],
  };
```

**Step 3**: Creator signs the provided typed data, thereby granting permission to mint their NFT upon purchase.

_Note_ See [Signatures](https://hackmd.io/ktJuljjGTA2TivezBXKA5g?view#Signatures) for more details on typed data and EIP-712 and EIP-1271.

**First**, construct the typed data structure:

```text
"types": {
    "EIP712Domain" [
      {
        type: "string",
        name: "name",
      },
      {
        type: "string",
        name: "version",
      },
      {
        type: "uint256",
        name: "chainId",
      },
      {
        type: "address",
        name: "verifyingContract",
      }
    ],
    "Mint1155": [
        { name: "tokenId", type: "uint256" },
        { name: 'supply', type: 'uint256' },
        { name: "tokenURI", type: "string" },
        { name: "creators", type: "Part[]" },
        { name: "royalties", type: "Part[]" }
    ],
    "Part": [
        { name: "account", type: "address" },
        { name: "value", type: "uint96" }
    ]
},
"domain": {
    name: "Mint1155",
    version: "1",
    chainId: 3,
    verifyingContract: "0x6a94aC200342AC823F909F142a65232E2f052183 "
},
"primaryType": "Mint1155",
"message": {
    "@type": "ERC1155",
    "contract": "0x6a94aC200342AC823F909F142a65232E2f052183 ",
    "tokenId": tokenId,
    "tokenURI": "/ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp"
    "uri": "/ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp",
    "supply": 10,
    "creators": [
        { 
            account: "0x1234...", 
            value: "10000" 
        }
    ],
    "royalties": [
        { 
            account: "0x1234...", 
            value: 2000 
        }
    ],
 };
```

**Then** provide the data structure above to the creator for signing.

```text
// Sample code

async function signTypedData(web3Provider, from, dataStructure) {
  const msgData = JSON.stringify(dataStructure);
  const signature = await web3Provider.send("eth_signTypedData_v4", [from, msgData]);
  const sig0 = sig.substring(2);
  const r = "0x" + sig0.substring(0, 64);
  const s = "0x" + sig0.substring(64, 128);
  const v = parseInt(sig0.substring(128, 130), 16);
  return {
    dataStructure,
    signature,
    v,
    r,
    s,
  };
}
```

**Finally**, get the `signature` from the object that the function above returns and add it as the final field of the Lazy Mint Request Body you created in Step 2.

E.g.

```text
{
    "@type": "ERC1155",
    "contract": "0x6a94aC200342AC823F909F142a65232E2f052183 ",
    "tokenId": tokenId,
    "uri": "/ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp",
    "supply": 10,
    "creators": [
        { 
            account: "0x1234...", 
            value: "10000" 
        }
    ],
    "royalties": [
        { 
            account: "0x1234...", 
            value: 2000 
        }
    ],
    "signatures": ["0x2f1e8dd2838930f0230a9fcbb2977779838eb8dd44391af1…a6cb767fa157d5fb54c084e8ffb41be1c1bc5b6f067fd681b"]
  };
```

**Step 4**: Create your Lazy Minted NFT

POST to `https://api-dev.rarible.com/protocol/v0.1/ethereum/nft/mints`

```text
{
    "@type": "ERC721",
    "contract": "0xB0EA149212Eb707a1E5FC1D2d3fD318a8d94cf05",
    "tokenId": tokenId,
    "uri": "/ipfs/QmWLsBu6nS4ovaHbGAXprD1qEssJu4r5taQfB74sCG51tp",
    "supply": 10
    "creators": [
        { 
            account: "0x1234...", 
            value: "10000" 
        }
    ],
    "royalties": [
        { 
            account: "0x1234...", 
            value: 2000 
        }
    ],
    "signatures": ["0x2f1e8dd2838930f0230a9fcbb2977779838eb8dd44391af1…a6cb767fa157d5fb54c084e8ffb41be1c1bc5b6f067fd681b"]
  };
```

Response

```text
{
  "id": ""0x3437df037bbbeb1aa3e417b32154bc2bb5da1c04:10269532675691974816893214588076010230265315839066808147818573374451427049549"",
  "contract": "0xB0EA149212Eb707a1E5FC1D2d3fD318a8d94cf05",
  "tokenId": tokenId,
  "creators": [
    {
      "account": "0x1234...",
      "value": 10000
    }
  ]
  "supply": 1,
  "lazySupply": 1,
  "owners": [
     "0x1234..."
  ],
  "royalties": [
    {
      "account": "0x1234...",
      "value": 2000
    }
  ],
  "pending": [
    {
      "date": "2019-08-24T14:15:22Z",
      "owner": "0x1234...",
      "from": "0x1234...",
      "contract": "0xB0EA149212Eb707a1E5FC1D2d3fD318a8d94cf05",
      "tokenId": tokenId,
      "value": 0,
      "type": "TRANSFER"
    }
  ]
}
```

You've successfully created a Lazy Minted NFT with the Rarible Protocol! 🎉   


#### ERC1155 Standard Minting

You can mint NFTs through the Rarible asset contracts using `mintAndTransfer` like you would for a standard `mint` call. You just need to provide all the expected parameters seen below.

`mintAndTransfer(LibERC1155LazyMint.Mint1155Data memory data, address to, uint256 _amount)`

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

You can do so by instantiating the contract in your app and calling the function directly using `ethers.js` or `web3.js`.

_Note_ For the signature, since you are minting an NFT as a direct call and not a lazy mint, you simply pass an empty signature. E.g. `0x`.

_Note_ Royalties are set as basis point, so 1000 = 10%. [More info](https://corporatefinanceinstitute.com/resources/knowledge/finance/basis-point-beep/)

**Example**

```javascript
async function mintNow() {
    // Get a token id
    const tokenId = await fetch(`https://api-dev.rarible.com/protocol/v0.1/ethereum/nft/collections/${contractAddress}/generate_token_id?minter=${account}`);

    // Instantiate the contract
    const provider = new ethers.providers.Web3Provider(userWalletProvider);
    const signer = provider.getSigner();
    const contract = new ethers.Contract(contractAddress, abi, signer);

    // Call the function
    const tx = await contract.mintAndTransfer(
        [
          tokenId.tokenId,
          uri,
          totalSupply,
          [[creator, 5000], [creator2, 5000]], // You can assign one or add multiple creators, but the value must total 10000
          [[creator, 1000], [creator2, 1000]], // Royalties are set as basis point, so 1000 = 10%. 
          ["0x"]
        ],
        minter,
        amount
      );

      const receipt = await tx.wait();
      console.log('Minting Success', receipt);
}
```

#### Custom Contracts

If you are **not** storing your metadata on IPFS, you will need to use your own contracts that have a `baseURI` better suited for where the metadata is stored.

As a starting point, so long as your contract follows the [ERC-721](https://eips.ethereum.org/EIPS/eip-721) or [ERC-1155](https://eips.ethereum.org/EIPS/eip-1155) standard it's NFTs can be bought and sold on Rarible and most other NFT marketplaces. Helpful guides on these standards can be found at [OpenZeppelin](https://docs.openzeppelin.com/contracts/4.x/erc721).

However, you'll need to add more to support royalties and lazy minting as these are not built into the current standards. To support royalties, your contract will need to inherit from the appropriate interfaces, which you can find [here](https://www.npmjs.com/package/@rarible/royalties) or [here](https://www.npmjs.com/package/@rarible/royalties-upgradeable) for an upgradeable version.

Similarly for lazy minting support, you will need to add a `mintAndTransfer` function in your contract for the protocol to call that inherits the expected behavior. You can add this yourself or use [this interface](https://www.npmjs.com/package/@rarible/lazy-mint).

In many cases, it may be easier and faster to just [fork the protocol contracts](https://github.com/rariblecom/protocol-contracts/tree/57043e3f9e93223ef9d65dae351d3c55b34e5bf1/tokens) you wanted to use and change the `baseURI` and any other data upon deployment.

_Note_ You can supply your own `tokenId` instead of getting one from the API call used for Lazy Minting when rolling your own contracts, however, the token id needs to have the minter's address followed by 96 bits, which can include any number you want, in order to pass the `require` checks in the default `mintAndTransfer` function. Alternatively, you can still use the generate token id API used above to supply a tokenId for them.

### Royalties V2

Rarible defines an interface to query royalties from a contract. This is implemented on the standard [Rarible token contracts](https://github.com/rariblecom/protocol-contracts/blob/57043e3f9e93223ef9d65dae351d3c55b34e5bf1/tokens/contracts/erc-721/ERC721Lazy.sol#L12).

It exposes a [method](https://github.com/rariblecom/protocol-contracts/blob/57043e3f9e93223ef9d65dae351d3c55b34e5bf1/royalties/contracts/impl/AbstractRoyalties.sol#L8) called `getRoyalties` which expects an ID as input \(usually tokenId\) and returns an array of accounts & basis points.

```text
    function getRoyalties(uint256 id) override external view returns (LibPart.Part[] memory) {
        return royalties[id];
    }
```

**Royalties Registry**

The exchange contract interacts with the Rarible royalties implementation indirectly through a [Royalty Registry](https://github.com/rariblecom/protocol-contracts/blob/57043e3f9e93223ef9d65dae351d3c55b34e5bf1/royalties-registry/contracts/RoyaltiesRegistry.sol#L58). The registry checks if the NFT contract supports the expected interface, and if so queries for the Rarible royalties array.

This allows for Rarible to support different royalty standards for different collections.

### Order Book

The general process for completing an order with the Rarible Exchange is as follows:

1. Seller approves the Rarible exchange contract to transfer NFT on their behalf
2. Seller creates and signs an order. They specify the types and amounts of assets they would like in return.
3. Seller submits the order to the indexer
4. Potential buyers query the indexer to get sell orders for a specific item or collection
5. Buyers can create a matching buy order for a sell order from the indexer. The buyer can then submit a matching buy order to the smart contract and execute the transfer \(no sig required\).
6. Or the buyer can create a new bid and submit it to the indexer \(requires signature\)
7. If the buyer submits a bid, the seller can choose to accept it by creating the matching sell order and submitting it to the contract, or they can send a signed order back to the buyer which the buyer would submit to the contract.

TODO swimlanes

#### Order Structure

Each order \(both buy & sell\) consist of a `makeAsset` and a `takeAsset`

**Make Asset** is what you are sending.

* In a buy order this is what you are _paying_ for the seller's NFT. This can be ETH, ERC20, ERC721, ERC1155, or any custom asset using their Asset Matcher interface
* In a sell order this is the NFT you are selling

**Take Asset** is what you are accepting in return

* In a buy order this is the NFT you are buying
* In a sell order this is what you are willing to accept. 

#### Order

| Field | Description | Required |
| :--- | :--- | :--- |
| **Maker** | Address of entity giving up `makeAsset` | Yes |
| **makeAsset** | Asset the entity is giving up | Yes |
| **taker** | Address of counterparty | no, if 0 then anyone can fill the order |
| **takeAsset** | Asset the entity is receving | Yes |
| **salt** | nonce for signatures submitted with the order | Generally signatures are only needed if msg.sender != maker |
| **start** | uint - order can't be filled before this time | no |
| **end** | uint - order can't be filled after this time | no |
| **dataType** | bytes4, usually hash of a string like v1 or v2 | Yes |
| **data** | generic `bytes`. Can be used for protocol extensions | no |

#### Asset

| Field | Description | Required |
| :--- | :--- | :--- |
| assetType | Specifies ETH, specific ERC20, ERC721, ERC1155 | Yes |
| value | uint | Yes |

#### AssetType

| Field | Description | Required |
| :--- | :--- | :--- |
| assetClass | bytes4 specifies ETH, ERC20, ERC721 | Yes |
| data | bytes - generic data depending on tp. Ex. address for ERC20, token + tokenID for ERC721 | yes |

#### Asset Types

Asset Class data field is calculated as follows

```text
    bytes4 constant public ETH_ASSET_CLASS = bytes4(keccak256("ETH"));
    bytes4 constant public ERC20_ASSET_CLASS = bytes4(keccak256("ERC20"));
    bytes4 constant public ERC721_ASSET_CLASS = bytes4(keccak256("ERC721"));
    bytes4 constant public ERC1155_ASSET_CLASS = bytes4(keccak256("ERC1155"));
```

All asset types get encoded using these helper functions [https://github.com/rariblecom/protocol-contracts/blob/master/exchange-v2/test/assets.js](https://github.com/rariblecom/protocol-contracts/blob/master/exchange-v2/test/assets.js)

**ERC721**

`assetClass`: Truncated hash of string "ERC721" `data`: ABI encoded parameters of `address` and `tokenId` `value`: 1

ERC721 Input \(Pre encoding\)

```text
  {
    assetType: {
      assetClass: "ERC721",
      token: "0x25646b08d9796ceda5fb8ce0105a51820740c049",
      tokenId: "0xc66d094ed928f7840a6b0d373c1cd825c97e3c7c00000000000000000000000a"
    },
    value: "1",
  }
```

**ERC1155**

`assetClass`: Truncated hash of string "ERC1155" `data`: ABI encoded parameters of `address` and `tokenId` `value`: 1-&gt;totalSupply

ERC1155 Input \(Pre encoding\)

```text
  {
    assetType: {
      assetClass: "ERC1155",
      token: "0x25646b08d9796ceda5fb8ce0105a51820740c049",
      tokenId: "0xc66d094ed928f7840a6b0d373c1cd825c97e3c7c00000000000000000000000a"
    },
    value: "100",
  }
```

**ERC20**

`assetClass`: Truncated hash of string "ERC20" `data`: ABI encoded parameters of `address` `value`: 1-&gt;totalSupply

ERC20 Input \(Pre encoding\)

```text
  {
    assetType: {
      assetClass: "ERC20",
      token: "0x25646b08d9796ceda5fb8ce0105a51820740c049",
    },
    value: "10000000000000000",
  }
```

**ETH**

`assetClass`: Truncated hash of string "ETH" `data`: 0x `value`: 1-&gt;1e18

Input \(pre encoding\)

```text
  {
    assetType: {
      assetClass: "ETH"
    },
    value: "10000000000000000",
  },
```

**Custom Asset Matcher**

Any asset can be added

`assetClass`: Truncated hash of any string `data`: Whatever is relevant `value`: some uint

#### Sell Orders

**Sell ERC721 for ERC20**

**Sell ERC721 for ETH**

**You can only fill an ETH order if your side of the order is providing the ETH**. Otherwise you would have to use WETH which has the transferFrom capability.

**First** Encode the order for signing

POST to `https://api-staging.rarible.com/protocol/v0.1/ethereum/order/encoder/order`

```text
{
    "type": "RARIBLE_V2",
    "maker": "0x744222844bFeCC77156297a6427B5876A6769e19",
    "make": {
        "assetType": {
            "assetClass": "ERC721",
            "contract": "0xcfa14f6DC737b8f9e0fC39f05Bf3d903aC5D4575",
            "tokenId": 1
        },
        "value": "1"
    },
    "take": {
        "assetType": {
            "assetClass": "ETH"
        },
        "value": "1000000000000000000"
    },
    "data": {
        "dataType": "RARIBLE_V2_DATA_V1",
        "payouts": [],
        "originFees": []
    },
    "salt": "3621"
}
```

Response:

```text
{
    "maker": "0x744222844bfecc77156297a6427b5876a6769e19",
    "makeAsset": {
        "assetType": {
            "assetClass": "0x73ad2146",
            "data": "0x000000000000000000000000cfa14f6dc737b8f9e0fc39f05bf3d903ac5d45750000000000000000000000000000000000000000000000000000000000000001"
        },
        "value": "1"
    },
    "taker": "0x0000000000000000000000000000000000000000",
    "takeAsset": {
        "assetType": {
            "assetClass": "0xaaaebeba",
            "data": "0x"
        },
        "value": "1000000000000000000"
    },
    "salt": "3621",
    "start": "0",
    "end": "0",
    "dataType": "0x4c234266",
    "data": "0x0000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000000000000000000000000000000000000006000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
}
```

**Then** Sign the order

```text
async function sign(provider, order, account, verifyingContract) {
    const chainId = Number(provider._network.chainId);
    const data = EIP712.createTypeData({
        name: "Exchange",
        version: "2",
        chainId,
        verifyingContract
    }, 'Order', order, Types);
  console.log({data})
    return (await EIP712.signTypedData(provider, account, data)).sig;
}
```

**Then** Post the order to Rarible indexer

POST to `https://api-staging.rarible.com/protocol/v0.1/ethereum/order/orders`

Payload

```text
{
    "type": "RARIBLE_V2",
    "maker": "0x744222844bFeCC77156297a6427B5876A6769e19",
    "make": {
        "assetType": {
            "assetClass": "ERC721",
            "contract": "0xcfa14f6DC737b8f9e0fC39f05Bf3d903aC5D4575",
            "tokenId": 1
        },
        "value": "1"
    },
    "take": {
        "assetType": {
            "assetClass": "ETH"
        },
        "value": "1000000000000000000"
    },
    "data": {
        "dataType": "RARIBLE_V2_DATA_V1",
        "payouts": [],
        "originFees": []
    },
    "salt": "5422",
    "signature": "0x45461654b86e856686e7a2e9a9213b29f8dc32a731046e0c2f1aa01e4eaa991e41ebc67535fac14c333ad5b0d0d821ef518edc9ed08ad7efc0af572620c045ce1c"
}
```

Response

```text
{
    "maker": "0x744222844bfecc77156297a6427b5876a6769e19",
    "make": {
        "assetType": {
            "assetClass": "ERC721",
            "contract": "0xcfa14f6dc737b8f9e0fc39f05bf3d903ac5d4575",
            "tokenId": "1"
        },
        "value": "1"
    },
    "take": {
        "assetType": {
            "assetClass": "ETH"
        },
        "value": "1000000000000000000"
    },
    "type": "RARIBLE_V2",
    "fill": "0",
    "makeStock": "1",
    "cancelled": false,
    "salt": "0x000000000000000000000000000000000000000000000000000000000000152e",
    "data": {
        "dataType": "RARIBLE_V2_DATA_V1",
        "payouts": [],
        "originFees": []
    },
    "signature": "0x45461654b86e856686e7a2e9a9213b29f8dc32a731046e0c2f1aa01e4eaa991e41ebc67535fac14c333ad5b0d0d821ef518edc9ed08ad7efc0af572620c045ce1c",
    "createdAt": "2021-05-25T02:04:28.836+00:00",
    "lastUpdateAt": "2021-05-25T02:04:28.983+00:00",
    "pending": [],
    "hash": "0xc9cb92588fc1434a417f4cacb9e1267750e6f1cbe650988a6fdc1d0be8a310a9",
    "makeBalance": "1",
    "takePriceUsd": 2735.1365826056253000000000000000000
}
```

**Sell ERC721 for another ERC721**

**Sell ERC721 for ERC1155**

#### Buy Orders

**Pay ERC20 for ERC721**

**Pay ETH for ERC721**

**You can only fill an ETH order if your side of the order is providing the ETH**. Otherwise you would have to use WETH which has the transferFrom capability.

Encoded order

```text
{
  "maker": "0x744222844bfecc77156297a6427b5876a6769e19",
  "makeAsset": { "assetType": { "assetClass": "0xaaaebeba", "data": "0x" }, "value": "100000000000000000" },
  "taker": "0x0000000000000000000000000000000000000000",
  "takeAsset": {
    "assetType": {
      "assetClass": "0x73ad2146",
      "data": "0x000000000000000000000000cfa14f6dc737b8f9e0fc39f05bf3d903ac5d45750000000000000000000000000000000000000000000000000000000000000005"
    },
    "value": "1"
  },
  "salt": "0",
  "start": "0",
  "end": "0",
  "dataType": "0x4c234266",
  "data": "0x0000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000000000000000000000000000000000000006000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
}
```

Example transaction that has both a signed sell order and an unsigned buy order, submitted by the buyer. [https://rinkeby.etherscan.io/tx/0x2d01b1869b556629acf7304b53d2f77fd927a2c3c9daea02aa093ba8ba41b4c6](https://rinkeby.etherscan.io/tx/0x2d01b1869b556629acf7304b53d2f77fd927a2c3c9daea02aa093ba8ba41b4c6)

**Swap ERC721 for ERC721**

**Pay ERC1155 for ERC721**

#### Bid Orders

A bid is just an unmatched buy order. The seller accepts it by creating a matched order.

### Signatures

Signatures are required to validate the orders. They are only required when the order maker is not the message sender.

This means a buyer can submit an order to the contract without their signature, only the seller's signature. And in return, a seller can submit an order to the contract without their signature, only the buyer's signature.

#### EIP712

**Example**

Sell ERC721 for ETH

```text
{
    "types": {
        "EIP712Domain": [
            {
                "type": "string",
                "name": "name"
            },
            {
                "type": "string",
                "name": "version"
            },
            {
                "type": "uint256",
                "name": "chainId"
            },
            {
                "type": "address",
                "name": "verifyingContract"
            }
        ],
        "AssetType": [
            {
                "name": "assetClass",
                "type": "bytes4"
            },
            {
                "name": "data",
                "type": "bytes"
            }
        ],
        "Asset": [
            {
                "name": "assetType",
                "type": "AssetType"
            },
            {
                "name": "value",
                "type": "uint256"
            }
        ],
        "Order": [
            {
                "name": "maker",
                "type": "address"
            },
            {
                "name": "makeAsset",
                "type": "Asset"
            },
            {
                "name": "taker",
                "type": "address"
            },
            {
                "name": "takeAsset",
                "type": "Asset"
            },
            {
                "name": "salt",
                "type": "uint256"
            },
            {
                "name": "start",
                "type": "uint256"
            },
            {
                "name": "end",
                "type": "uint256"
            },
            {
                "name": "dataType",
                "type": "bytes4"
            },
            {
                "name": "data",
                "type": "bytes"
            }
        ]
    },
    "domain": {
        "name": "Exchange",
        "version": "2",
        "chainId": 3,
        "verifyingContract": "0x1e1B6E13F0eB4C570628589e3c088BC92aD4dB45"
    },
    "primaryType": "Order",
    "message": {
        "maker": "0x744222844bfecc77156297a6427b5876a6769e19",
        "makeAsset": {
            "assetType": {
                "assetClass": "0x73ad2146",
                "data": "0x000000000000000000000000cfa14f6dc737b8f9e0fc39f05bf3d903ac5d45750000000000000000000000000000000000000000000000000000000000000001"
            },
            "value": "1"
        },
        "taker": "0x0000000000000000000000000000000000000000",
        "takeAsset": {
            "assetType": {
                "assetClass": "0xaaaebeba",
                "data": "0x"
            },
            "value": "1000000000000000000"
        },
        "salt": "6274",
        "start": "0",
        "end": "0",
        "dataType": "0x4c234266",
        "data": "0x0000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000000000000000000000000000000000000006000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
    }
}
```

![](https://i.imgur.com/Ry921oC.png)

#### EIP1271

Example implementation: [https://rinkeby.etherscan.io/address/0x998Cf6fDd7FF1EB258c288762284142D28D0ee39\#code](https://rinkeby.etherscan.io/address/0x998Cf6fDd7FF1EB258c288762284142D28D0ee39#code)

EIP1271 allows a contract to delegate authorized signers. Usually EIP1271 expects an authorized delegate to submit a signature via the contract, and uses typical ECrecover to see if the signature is valid.

EIP1271 returns `valid signature: true**` if the signature is valid & the signer is authorized by the contract

\*\* not actually `true`, but instead a magic value to guard against mistaken 'true' bit

The Rarible contract checks if the signature is valid with the following code

```text
    require(
        ERC1271(order.maker).isValidSignature(_hashTypedDataV4(hash), signature) == MAGICVALUE,
        "contract order signature verification error"
    );
```

### Fees

Rarible calculates 3 types of fees during a transfer: **origin**, **royalties**, and **protocol**

Origin fees are specified for each order

Royalties are defined by the NFT

Protocol fees are defined by the contract.

### Indexing

Example of how to query orders for all NFTs in a collection

```text
curl --location --request GET 'https://api-staging.rarible.com/protocol/v0.1/ethereum/order/orders/sell/byCollection?collection=0xcfa14f6DC737b8f9e0fC39f05Bf3d903aC5D4575&sort=LAST_UPDATE'
```

Response

```text
{
    "orders": [
        {
            "maker": "0x744222844bfecc77156297a6427b5876a6769e19",
            "make": {
                "assetType": {
                    "assetClass": "ERC721",
                    "contract": "0xcfa14f6dc737b8f9e0fc39f05bf3d903ac5d4575",
                    "tokenId": "1"
                },
                "value": "1"
            },
            "take": {
                "assetType": {
                    "assetClass": "ETH"
                },
                "value": "1000000000000000000"
            },
            "type": "RARIBLE_V2",
            "fill": "0",
            "makeStock": "1",
            "cancelled": false,
            "salt": "0x00000000000000000000000000000000000000000000000000000000000002b3",
            "data": {
                "dataType": "RARIBLE_V2_DATA_V1",
                "payouts": [],
                "originFees": []
            },
            "signature": "0xf45a9adfb58fdd9da808b5f80302ebdb7d75f032d6aaee9700ee93f567fff3b148116a93ad2bd908a2d8e9576295650460a653d442f7c3472cb8f1275739075e1c",
            "createdAt": "2021-05-25T01:33:43.358+00:00",
            "lastUpdateAt": "2021-05-25T01:33:43.532+00:00",
            "pending": [],
            "hash": "0x0b3007a2a4cd8701f98b8fd02f2a9bc09320d11d5e4fa8134f884904d8bfdf3b",
            "makeBalance": "1",
            "takePriceUsd": 2735.136582605625300000000000000000
        }
    ],
    "continuation": "1621906423532_0b3007a2a4cd8701f98b8fd02f2a9bc09320d11d5e4fa8134f884904d8bfdf3b"
}
```

## Example Projects

### How Picnic uses the Rarible API

We use Rarible to help us identify NFTs from creators and collectors in the [Picnic](https://picnic.show) showcase. The [Rarible API](https://api-reference.rarible.com) provides a few great endpoints for fetching the necessary data.

#### API Calls

The following endpoints can be used:

* **Production:** [https://api.rarible.com/](https://api.rarible.com/)
* **Staging \(Rinkeby/Ropsten\):** [https://api-staging.rarible.com/](https://api-staging.rarible.com/)

**Getting Tokens by Owner**

Paginate through owned tokens

```javascript
import axios from 'axios';

/**
 * Get collector's owned tokens.
 * @param {string} owner - owner address (0x...)
 * @param {object} opts - options
 * @param {string} opts.continuation - Rariable continuation ID
 * @param {integer} opts.size - size of tokens to get (default: 100).
 * @return 
 */
const fetchOwnedTokens = async (owner, opts = {}) => {
  const { continuation, size = 100 } = opts;

  try {
    const result = await axios.get('https://api.rarible.com/protocol/v0.1/ethereum/nft/items/byOwner', {
      params: { owner, continuation },
    });
    const { data } = result;

    // Paginate results
    let hist = [];
    if (data.continuation && data.items.length === size) {
      hist = await getOwnedTokens(owner, { ...opts, continuation: data.continuation });
    }

    // Return full history
    return [...data.items, ...hist];
  } catch (err) {
    console.error(err);
    return [];
  }
};
```

The `byOwner` endpoint does not return token metadata. You can attempt to query this information from the blockchain directly or use another API to collect token metadata information.

You can use the `getItemMetaById` Rarible API endpoint to get token metadata. Be mindful that you’ll have to make one request per token.

```javascript
import axios from 'axios';

/**
 * Get token metadata from token id.
 * @param {string} id - token ID, formatted as CONTRACT_ADDRESS:TOKEN_ID (e.g. 0x1:1001)
 * @return {object}
 */
const fetchTokenMetadata = async id => {
  const { data } = await axios.get(`https://api.rarible.com/protocol/v0.1/ethereum/nft/items/${id}/meta`);
  if (!data?.name) {
    throw new Error('Invalid NFT data', { id, data });
  }
  return data;
};
```

If you have question, please reach out. greg@picnic.show / [@gleuch](https://twitter.com/gleuch)

