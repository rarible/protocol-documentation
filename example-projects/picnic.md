# Picnic

We use Rarible to help us identify NFTs from creators and collectors in the [Picnic](https://picnic.show) showcase. The [Rarible API](https://api-reference.rarible.com) provides a few great endpoints for fetching the necessary data.

## API Calls

The following endpoints can be used:

* **Production:** [https://api.rarible.com/](https://api.rarible.com/)
* **Staging \(Rinkeby/Ropsten\):** [https://api-staging.rarible.com/](https://api-staging.rarible.com/)

### Getting Tokens by Owner

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

