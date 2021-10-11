# Royalties Schema

## Royalties V2

Rarible defines an interface to query royalties from a contract. This is implemented on the standard [Rarible token contracts](https://github.com/rariblecom/protocol-contracts/blob/57043e3f9e93223ef9d65dae351d3c55b34e5bf1/tokens/contracts/erc-721/ERC721Lazy.sol#L12).

It exposes a [method](https://github.com/rariblecom/protocol-contracts/blob/57043e3f9e93223ef9d65dae351d3c55b34e5bf1/royalties/contracts/impl/AbstractRoyalties.sol#L8) called `getRoyalties` which expects an ID as input (usually tokenId) and returns an array of accounts & basis points.

```
    function getRaribleV2Royalties(uint256 id) override external view returns (LibPart.Part[] memory) {
        return royalties[id];
    }
```

## Royalties V1

The exchange contract interacts with the Rarible royalties implementation indirectly through a [Royalty Registry](https://github.com/rariblecom/protocol-contracts/blob/57043e3f9e93223ef9d65dae351d3c55b34e5bf1/royalties-registry/contracts/RoyaltiesRegistry.sol#L58). The registry checks if the NFT contract supports the expected interface, and if so queries for the Rarible royalties array.

This allows for Rarible to support different royalty standards for different collections.

Rarible Protocol Supports on-chain Royalties, these are handled in the ExchangeV1 contract by the royalties array which is needed to execute the mint function.

This tuple is made up of two variables, fees.recipient & fees.value.

fees.recipient refers to either the item owner (By default) or an address where the Royalties will be received.

fees.value is the royalties percentage, by default this value is 1000 on Rarible which is a 10% royalties fee. This is done using basis points, more information regarding basis point can be found [here](https://corporatefinanceinstitute.com/resources/knowledge/finance/basis-point-beep/).

Below you can find the code block from ExchangeV1 which handles the on-chain Royalties.

```javascript
contract HasSecondarySaleFees is ERC165 {
    event SecondarySaleFees(uint256 tokenId, address[] recipients, uint[] bps);
    /*
     * bytes4(keccak256('getFeeBps(uint256)')) == 0x0ebd4c7f
     * bytes4(keccak256('getFeeRecipients(uint256)')) == 0xb9c4d9fb
     *
     * => 0x0ebd4c7f ^ 0xb9c4d9fb == 0xb7799584
     */
    bytes4 private constant _INTERFACE_ID_FEES = 0xb7799584;
    constructor() public {
        _registerInterface(_INTERFACE_ID_FEES);
    }
    function getFeeRecipients(uint256 id) public view returns (address payable[] memory);
    function getFeeBps(uint256 id) public view returns (uint[] memory);
}
```
