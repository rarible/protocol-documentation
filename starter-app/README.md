
This starter app was built using [Austin Griffith's Scaffold-Eth](https://github.com/austintgriffith/scaffold-eth) framework. For more starter apps and inspiration check out the [BuidlGuidl](https://buidlguidl.com/)


Video Tutorial: 

[![tutorial](https://user-images.githubusercontent.com/4401444/121973242-d1703d80-cd4a-11eb-8a6a-96b103f25ef3.jpeg)](https://youtu.be/MBj3WIj5Wzc![thumbnail])

# Quick Start

⚠️⚠️ **This application connects to the Rarible contracts & API on Ropsten** ⚠️⚠

required: [Node](https://nodejs.org/dist/latest-v12.x/) plus [Yarn](https://classic.yarnpkg.com/en/docs/install/) and [Git](https://git-scm.com/downloads)


```bash
git clone https://github.com/ipatka/scaffold-eth.git

cd scaffold-eth

git checkout rarible-starter-app
```

```bash

yarn install

```

```bash

yarn start

```

📱 Open http://localhost:3000 to see the app

🏗 scaffold-eth is a hackthon stack for quick product prototyping on Ethereum.

👩‍🔬 This scaffolding leverages state of the art tooling from the ecosystem.

🖼 This fork of scaffold-eth implements the basic features and functionality of the Rarible protocol

🧪 It is a free standing dapp so you can learn by making small changes.


> *After installing*, your dev environment should look like this:

![Screen Shot 2021-06-14 at 3 45 30 PM](https://user-images.githubusercontent.com/4401444/121971693-55282b00-cd47-11eb-84ad-abe74acfc6d2.png)

> React dev server, Ropsten network, deploy terminal, code IDE, and frontend browser.

# Mint your collectibles

Mint some NFTs that we can use to test out the Rarible protocol.


> in a second terminal window:

```bash
cd simple-nft-example
yarn generate
yarn accounts
```

🔐 Generate a deploy account with `yarn generate` and view it with `yarn account`

![Screen Shot 2021-06-14 at 3 40 06 PM](https://user-images.githubusercontent.com/4401444/121971731-725cf980-cd47-11eb-84ed-98c02ac1c5df.png)


💵 Fund your deployer account with some Ropsten Eth from a [faucet](https://faucet.ropsten.be/)

![Screen Shot 2021-06-14 at 3 40 25 PM](https://user-images.githubusercontent.com/4401444/121971709-65400a80-cd47-11eb-9913-4a08a49f2716.png)


> Deploy your contract (optional, you can also just use the default one):

```bash
yarn deploy
```

![Screen Shot 2021-06-14 at 3 44 00 PM](https://user-images.githubusercontent.com/4401444/121971753-7a1c9e00-cd47-11eb-8db9-d5e635048c43.png)

✏️  Edit the address in `packages/react-app/src/contract/YourCollectible.address.js` to your deployed contract address


💼 Edit the mint script `mint.js` in `packages/hardhat/scripts` to mint to your browser wallet address


![Screen Shot 2021-06-14 at 3 46 11 PM](https://user-images.githubusercontent.com/4401444/121971773-87398d00-cd47-11eb-9b0b-b91f7a8e9061.png)


> *After minting*, your dev environment should look like this:

![Screen Shot 2021-06-14 at 3 46 31 PM](https://user-images.githubusercontent.com/4401444/121971787-91f42200-cd47-11eb-85f9-66410d45c356.png)


You can visit your new NFTs on the Rarible site by specifying the contract address & tokenID in the URL like this

![Screen Shot 2021-06-14 at 3 53 15 PM](https://user-images.githubusercontent.com/4401444/121971823-a59f8880-cd47-11eb-8aff-84a51728d8f9.png)


`https://ropsten.rarible.com/token/0x66f806bf40bfa98f2dac85a85d437895043f2be5:1?tab=owners`


# Rarible Item Indexer

Go to the Rarible Item indexer tab and enter the contract address for our YourCollectible contract.

This tab uses the metadata indexer which is documented here: https://api-reference.rarible.com/#operation/getItemMetaById


![Screen Shot 2021-06-14 at 4 04 38 PM](https://user-images.githubusercontent.com/4401444/121971842-b3550e00-cd47-11eb-9a93-1ebaa32e2bc0.png)


You can also use the Rarible indexer to get NFT data for an entire collection, all NFTs owned by an address, and more.

# Rarible Order Book

We can create a sell order for one of these NFTs right from the item indexer.

First we need to make sure the Rarible Exchange is approved to transfer NFTs on our behalf when they are sold.

Enter the Rarible Transfer Proxy address and hit 'Approve'.

Rarible Transfer Proxy on Ropsten: 0xf8e4ecac18b65fd04569ff1f0d561f74effaa206

![Screen Shot 2021-06-14 at 4 11 37 PM](https://user-images.githubusercontent.com/4401444/121971856-bd770c80-cd47-11eb-9f47-472f53232466.png)


[Example Tx](https://ropsten.etherscan.io/tx/0x288715731a6daac47757968c3dcd89e8af462b87df410cf2a4c5a14ae3c481a4)

Now select 'Sell for ETH' and enter the ETH amount and use the *️⃣ button to format it in wei.

Sign the order to submit it to Rarible.


![Screen Shot 2021-06-14 at 4 16 10 PM](https://user-images.githubusercontent.com/4401444/121971873-c667de00-cd47-11eb-9fba-18e6874d4ea4.png)



Now go over to the Rarible Order Indexer tab and find the order you just created by entering the collection address and token ID.

Fill the order, and submit the transaction to the Rarible exchange.


![Screen Shot 2021-06-14 at 4 28 29 PM](https://user-images.githubusercontent.com/4401444/121971889-d1227300-cd47-11eb-9964-6592d5ad2388.png)


[Example Tx](https://ropsten.etherscan.io/tx/0xabe5433e500a6d3db229fb7630f898c37d30d4422dde69c1ab20a2b84cce2462)

Now on the Rarible UI you can see the listing and transfer history!


![Screen Shot 2021-06-14 at 5 20 35 PM](https://user-images.githubusercontent.com/4401444/121971901-d97aae00-cd47-11eb-966b-d8c36bd0e6db.png)


# Lazy Minting

With Lazy Minting you can defer the gas costs of minting the NFT to the first buyer.

For this example we will use the standard Rarible ERC721 contract deployed here: 0xB0EA149212Eb707a1E5FC1D2d3fD318a8d94cf05

All you need for lazy minting is the IPFS hash of your NFT content.

Go to the IPFS upload tab

Modify the content, or just hit upload

![Screen Shot 2021-06-14 at 7 39 22 PM](https://user-images.githubusercontent.com/4401444/121972118-5148d880-cd48-11eb-9260-2ced5b4f7660.png)


Copy the IPFS hash

Go to the lazy minting tab and enter the hash.

Press mint

![Screen Shot 2021-06-14 at 7 39 35 PM](https://user-images.githubusercontent.com/4401444/121972108-4c842480-cd48-11eb-8474-85f56c23d3f4.png)



Now let's go to the item indexer and see the lazy minted NFT

Copy your contract address and token ID from the lazy minting tab

Enter them on the item indexer

We can also view the lazy minted item on the Rarible UI!

![Screen Shot 2021-06-14 at 7 40 48 PM](https://user-images.githubusercontent.com/4401444/121972194-750c1e80-cd48-11eb-83a6-2345de2f9c7f.png)



https://ropsten.rarible.com/token/0xB0EA149212Eb707a1E5FC1D2d3fD318a8d94cf05:52585140568026265337503508601622814624376142828352682734444878603886713110535?tab=history

# Selling a lazy minted item

Same process as the normal minted item. Enter your contract address & token ID on the order indexer, and click 'fill order'.

![Screen Shot 2021-06-14 at 5 59 45 PM](https://user-images.githubusercontent.com/4401444/121971984-05962f00-cd48-11eb-92ed-93e2a96eadb1.png)


# Built with scaffold-eth

This starter app was built using Austin Griffith's Scaffold-Eth framework. For more starter apps and inspiration check out the [BuidlGuidl](https://buidlguidl.com/)
