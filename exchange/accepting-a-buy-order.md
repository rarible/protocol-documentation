# Accepting a Buy Order

To buy an item or accept a bid user should send transaction to [Exchange](https://rinkeby.etherscan.io/address/0x43162023c187662684abaf0b211dccb96fa4ed8a) contract's matchOrders function. Here is an [example transaction](https://rinkeby.etherscan.io/tx/0x5392cff50b12259bcbc24bd878022c65601ff5bd787cee48da0bcf418f04d805) for accepting bid.

matchOrders function defined in [ExchangeV2Core](https://github.com/rariblecom/protocol-contracts/blob/master/exchange-v2/contracts/exchange/v2/ExchangeV2Core.sol) has 4 parameters:

1. left order.
2. left order signature
3. right order
4. right order signature 

{% hint style="info" %}
more about order structure can be found [here](https://docs.rarible.com/exchange/exchangev2#order-structure)
{% endhint %}

```text
//example order encoded for use with web3
["0xfb571f9da71d1ac33e069571bf5c67fadcff18e4",[["0x73ad2146","0x00000000000000000000000025646b08d9796ceda5fb8ce0105a51820740c049fb571f9da71d1ac33e069571bf5c67fadcff18e4000000000000000000000001"],1],"0x0000000000000000000000000000000000000000",[["0xaaaebeba","0x"],"10000000000000000"],1,0,0,"0x4c234266","0x000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000400000000000000000000000000000000000000000000000000000000000000000"]
```

If user finds suitable order for him, he should form the order in opposite direction \(for example, if he wants to buy NFT1 for 1ETH, then he should form the order for exchanging 1ETH to NFT1\)

Then user should take order from an API, signature for that order, his own order and send 3 these parameters to matchOrders function \(right order signature can be left blank\)

