# Concepts

## **Asset Introduction**

**Rarible protocol has two asset types, ERC721, and ERC1155, the main difference between them is that ERC721 creates unique 1 of 1 item, whereas ERC1155, allows the user to create an item with multiple editions (The maximum amount of editions is 2\*\*256 - 1).**

### **ERC721 & ERC1155**

**With the new ERC721 & ERC1155 Asset contracts we no longer call the mint function directly, instead, we implement lazy minting via the mintAndTransfer function. Lazy minting allows the item to be created and store off-chain until someone purchases/transfers this item, only at this point does the item get created on-chain.**

**Direct calls to mint() should be avoided and replaced with mintAndTransfer(). However, standard minting is still possible with this function. See ERC721 Standard Minting or ERC1155 Standard Minting for details.**\
