---
description: >-
  Rarible protocol is a combination of smart-contracts for minting, exchanging
  tokens, APIs for order creation, discovery, standards used in smart contracts.
---

# Rarible Protocol Overview

Rarible pursues the goal of creating a highly liquid environment for all NFTs out there: a robust on-chain protocol designed for NFTs to exist in a connected space. As a separate initiative with a dedicated team, it will enable direct interactions with the protocol from multiple front ends like storefronts or wallets, offering additional distribution channels and enhancing liquidity. It will also fuel the discovery of new NFT trading mechanics.

Rarible is designed as the NFT protocol for all, owned and governed by the community. In this regard, a special place in the initiative is reserved for Rarible native governance token $RARI as the basic building block for the future NFT ecosystem.

## Protocol Features

Rarible's protocol includes contracts, standards, and APIs for:

**Minting**
* Minting (Both ERC721 & ERC1155)
* Lazy Minting - Token metadata & minting signatures are stored on Rarible back-end until a buyer fills the order. Then a `mintAndTransfer` call is made on chain when the order is filled


**Exchange** (Buy, Sell, Bid)
* Signature based order matching using an off chain order book
* Asset discovery is off chain, then buyers or sellers can submit both sides of an order, including relevant signatures to execute a transfer.
* Asset owners must `approve` the Rarible exchange to transfer on their behalf
* Multiple asset types are supported to fill orders (ERC721, ERC1155, ERC20)
* Bidding is supported

**Indexer**
* Rarible API exposes ways to query NFTs indexed on Ethereum
* Rarible API exposes ways to create orders


### API Reference

https://api-reference.rarible.com/



| Base URL  | Network | Chain ID |
| -------- | -------- | -------- |
| https://api.rarible.com/protocol/v0.1/     | Mainnet     | 1     |
| https://api-staging.rarible.com/protocol/v0.1/     | Rinkeby     | 4     |
| https://api-dev.rarible.com/protocol/v0.1/     | Ropsten     | 3     |



{% hint style="info" %}
Source code for the ERC721, ERC1155, Rarible Exchange are available on github:  
[https://github.com/rariblecom/protocol-contracts](https://github.com/rariblecom/protocol-contracts)
{% endhint %}

## Why build on Rarible protocol? 

1. **Supply and demand of the whole Rarible ecosystem** Rarible is one of the biggest NFT marketplaces out there with over $64 million in total lifetime volume and 57k monthly protocol users, slick UX, and a variety of use cases across industries. You can utilize the shared order book with Rarible.
2. **Advanced & robust tech done for you** Creating the tech from scratch is hard and time-consuming. Rarible provides access to the tools that the team has been developing for the past 1,5 years with wide functionality and data on all the NFTs created.
3. **Monetization**  Rarible protocol enables arbitrary front-end fees: you can additionally monetize your creations.
4. **DAO**  Rarible is steadily moving towards becoming a fully decentralized autonomous organization. The DAO will offer multiple opportunities for creators to get funding and exposure. It will incentivize people to build on top of the protocol, and we expect the DAO to reward the early builders.

## Protocol Flow 

1. [Creating ERC721/1155 Asset Metadata and Calling the Mint function](asset/creating-an-asset/) In this step, we build asset metadata, upload this metadata to IPFS and finally create our NFT on-chain. It is also very important to read up about our [Royalties Schema](asset/creating-an-asset/royalties-schema.md).
2. [Asset Discovery](asset/asset-discovery.md) In our Asset Discovery section, you will learn how to query items and their metadata, using filters and paging, this includes items created outside of Rarible.
3. [Creating a sell order](exchange/creating-a-sell-order.md) In this section you will learn about approving assets through the transfer proxy, generating the order structure, calculating origin and protocol fees as well as signing your order and submitting it to the Rarible Indexer.
4. [Accepting a buyer/bid order](exchange/accepting-a-buy-order.md) In this section we will demonstrate the conversion of ETH to WETH \(For placing a bid\), approvals via the ERC20 Proxy Contract, generating the buy/bid order structure,  Calculating Protocol and Origin fees as well as Submitting your order to the Rarible Indexer.
5. [Order Discovery](exchange/order-discovery.md) Order Discovery explorers finding active orders \(Buy/Sell\) for items via an API query.
6. [Matching Order](smart-contracts/matching-orders.md) Matching Orders is a very important section as it deals with the Rarible Protocols Matching system, as well as custom matching contracts which can be created externally and utilized.  
7. [Updating/Canceling An Order](exchange/updating-cancelling-an-order.md) As the name suggests, this deals with the on-chain calls required to cancel an order \(Buy/Sell/Bid\) as well as how to update an order.
8. API Reference

