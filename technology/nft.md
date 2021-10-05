# NFT

NFT is a token that links to some piece of data, e.g. link to a peice of digital art, video, image, etc. NFT has a unique identifier that lets the owner prove that it's one of a kind.

Smart contract is necessary so that people can verify that a NFT came from a smart contract signed by the creator. Creators ususally publicly announce their wallet address so no one can pretend to be them. The buyer can also know that it is not a duplicate since it has a unique identifier from the original collection.

## Solidity

An object-oriented, high-level language for implementing smart contracts. https://docs.soliditylang.org/. Influenced by C++, Python, and Javascript and is designed to target the Ethereum Virtual Machine (EVM). Statically typed, supports inheritance, libraries, and complex user-defined types.

## ERC721

ERC721 is the NFT standard https://eips.ethereum.org/EIPS/eip-721. OpenZeppelin has a library implementation of it: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol.

Every NFT has the same metadata:

`{"name":"str","description":"str","image":"str"}`

## OnChain

If imgur goes down then the image link is useless. Storing the NFT data "on-chain" -- the data lives on the contract itself; making the NFT truly permanent. NFT data is lost if the blockchain itself goes down.

Common way to store NFT data for images is using an SVG. 

### Public variables

https://docs.soliditylang.org/en/develop/units-and-global-variables.html#block-and-transaction-properties

## Glossary

- gas fees: https://ethereum.org/en/developers/docs/gas/. Gas refers tot he unit that measures the amount of computational effort required to execute specific operations on the Ethereum network. Gas fees are paid in Ethereum's native currency, ETH, denoted in gwei, 10^-9 ETH.

