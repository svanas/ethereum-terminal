# ethereum-terminal

Ethereum Terminal (ET) is an EVM-compatible toolbox chock-full of commands for developers, wallets, and savvy users.

Usage: `$ et [command] [options]`

ET is available for download as a CLI for the following platforms:
* Windows 32-bit
* Windows 64-bit
* macOS Intel
* macOS ARM

Linux users can clone this repository and compile from source.

## Commands

Use `$ et [command] --help` for more information about a command.

## privacy & security

| command | description                                                                                                                                               |
|---------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| revoke  | un-approve every EOA plus every smart contract that is a proxy (not immutable), or isn’t [Etherscan](https://etherscan.io/)-verified, or has an admin key |
| detect  | detect tainted coins in your wallet, and report the reason why they are high risk (for example: if the sender is sanctioned)                              |
| migrate | transfer all your tokens to a new, fresh wallet if your private key is compromised or you want to revoke every token approval                             |

Note: if a command needs a private key to complete, it will return one or more unsigned transactions plus user-friendly metadata about the transactions. This allows for wallets to prompt the user, display something nice, sign the transactions, and send the raw transactions to the nearest RPC endpoint.

## DeFi commands

| command               | description                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------|
| [get-apy](#get-apy)   | get the annual percentage yield for any [EIP-4626](https://eips.ethereum.org/EIPS/eip-4626) contract |
| [deposit](#deposit)   | deposit your idle stable asset into a DeFi protocol with the highest APY or the lowest risk          |
| [withdraw](#withdraw) | burn your LP tokens and claim the underlying asset                                                   |

Note: because ET can distribute via [BitTorrent](https://en.wikipedia.org/wiki/BitTorrent), it is more censorship-resistant than any web2 frontend out there. Should an official DeFi frontend disappear, depositors can use ET to withdraw the underlying asset.

## miscellaneous on-chain commands

| command                               | description                                                                        |
|---------------------------------------|------------------------------------------------------------------------------------|
| [get-balance](#get-balance)           | get your account's balance in wei                                                  |
| [get-gas-price](#get-gas-price)       | get current gas price in wei                                                       |
| [get-block-number](#get-block-number) | get current block number                                                           |
| [get-transaction](#get-transaction)   | get a transaction's data                                                           |
| [get-receipt](#get-receipt)           | get the receipt of a completed transaction                                         |
| [get-reason](#get-reason)             | get the revert reason for a failed transaction                                     |
| [open](#open)                         | open transaction in a block explorer                                               |
| [cancel](#cancel)                     | cancel a pending transaction                                                       |
| [get-price](#get-price)               | get [Chainlink](https://chain.link/)'s oracle price for an asset in USD            |
| [resolve](#resolve)                   | resolve an [ENS](https://ens.domains/) name to an Ethereum address                 |
| [reverse](#reverse)                   | reverse resolution from an Ethereum address to an [ENS](https://ens.domains/) name |

## miscellaneous off-chain commands

| command                 | description                                                                                       |
|-------------------------|---------------------------------------------------------------------------------------------------|
| [generate](#generate)   | generate new private key                                                                          |
| [from-wei](#from-wei)   | convert wei to any other unit (eg. gwei, ether, etc)                                              |
| [to-wei](#to-wei)       | convert any unit (eg. gwei, ether, etc) to wei                                                    |
| [checksum](#checksum)   | convert an address to a [checksummed](https://eips.ethereum.org/EIPS/eip-55) address              |
| [keccak256](#keccak256) | hash any input, using the [keccak-256](https://en.wikipedia.org/wiki/SHA-3) digest, output as hex |
| [scale](#scale)         | convert a token balance from a float to a bigint                                                  |
| [unscale](#unscale)     | convert a token balance from a bigint to a float                                                  |

### get-apy

Get the annual percentage yield for any [EIP-4626](https://eips.ethereum.org/EIPS/eip-4626) contract.

Usage: `$ et get-apy [options]`

| option     | description                                                                                                        | type   | mandatory |
|------------|--------------------------------------------------------------------------------------------------------------------|--------|-----------|
| --chain    | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1))                       | int    | no        |
| --contract | address of an [EIP-4626](https://eips.ethereum.org/EIPS/eip-4626) contract, or highest APY on the chain if omitted | hex    | no        |
| --symbol   | ERC-20 symbol of the underlying asset, or                                                                          | string | no        |
| --address  | ERC-20 token address of the underlying asset                                                                       | hex    | no        |
| --period   | sampled period in days. likely values are `1` or `3` or `7` or `14` of `31`. defaults to `14` days if omitted      | int    | no        |

Example: `$ et get-apy --contract=0x690E382baD9016F688Cfc18AF306e1fDD6CB28E8`

### deposit

Deposit your idle stable asset into a DeFi protocol with the highest APY or the lowest risk.

Usage: `$ et deposit [options]`

| option     | description                                                                                  | type   | mandatory |
|------------|----------------------------------------------------------------------------------------------|--------|-----------|
| --chain    | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1)) | int    | no        |
| --contract | address of an [EIP-4626](https://eips.ethereum.org/EIPS/eip-4626) contract, or               | hex    | no        |
| --apy      | `highest` or `safest`                                                                        | string | no        |
| --owner    | address of the depositor                                                                     | hex    | yes       |
| --symbol   | ERC-20 symbol of the underlying asset, or                                                    | string | no        |
| --address  | ERC-20 token address of the underlying asset                                                 | hex    | no        |
| --amount   | amount of the underlying asset, or your entire balance of the underlying asset if omitted    | bigint | no        |

Example: `$ et deposit --owner=0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045 --apy=highest --symbol=DAI`

This example will approve `0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045`'s DAI and then deposits `0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045`'s enitire balance of DAI into a DeFi protocol with the highest APY on Ethereum.

The `deposit` command will return at least two unsigned transactions plus user-friendly metadata about the deposit. This allows for wallets to prompt the user, display something nice, and sign the transactions.

### withdraw

Burn your LP tokens and claim the underlying asset.

Usage: `$ et withdraw [options]`

| option     | description                                                                                                    | type   | mandatory |
|------------|----------------------------------------------------------------------------------------------------------------|--------|-----------|
| --chain    | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1))                   | int    | no        |
| --contract | address of an EIP-4626 contract, or [all known EIP-4626 contracts](https://erc4626.info/#vaultscan) if omitted | hex    | no        |
| --owner    | address of the depositor                                                                                       | hex    | yes       |
| --symbol   | ERC-20 symbol of the underlying asset, or                                                                      | string | no        |
| --address  | ERC-20 token address of the underlying asset                                                                   | hex    | no        |
| --amount   | amount of the underlying asset, or your entire balance of LP tokens if omitted                                 | bigint | no        |

Example: `$ et withdraw --contract=0x690E382baD9016F688Cfc18AF306e1fDD6CB28E8 --owner=0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045`

### get-balance

Get your account's balance in wei.

Usage: `$ et get-balance [options]`

| option     | description                                                                                  | type   | mandatory |
|------------|----------------------------------------------------------------------------------------------|--------|-----------|
| --chain    | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1)) | int    | no        |
| --owner    | address of the owner                                                                         | hex    | yes       |

Example: `$ et get-balance --owner=0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045`

### get-gas-price

Get current gas price in wei.

Usage: `$ et get-gas-price [options]`

| option  | description                                                                                  | type   | mandatory |
|---------|----------------------------------------------------------------------------------------------|--------|-----------|
| --chain | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1)) | int    | no        |

Example: `$ et get-gas-price`

Note: on [EIP-1559](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1559.md)-enabled chains, this command will return the lastest block's `baseFeePerGas` value plus the `eth_maxPriorityFeePerGas` value. On other chains, it will use a gas price oracle, for example the [blockscout gas price oracle](https://docs.gnosischain.com/tools/oracles/gas-price) on [Gnosis Chain](https://chainlist.org/chain/100). If no gas oracle is available, this command will return the [eth_gasPrice](https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_gasprice) value.

### get-block-number

Get current block number.

Usage: `$ et get-block-number [options]`

| option  | description                                                                                  | type   | mandatory |
|---------|----------------------------------------------------------------------------------------------|--------|-----------|
| --chain | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1)) | int    | no        |

Example: `$ et get-block-number`

### get-transaction

Get a transaction's data.

Usage: `$ et get-transaction [options]`

| option  | description                                                                                  | type   | mandatory |
|---------|----------------------------------------------------------------------------------------------|--------|-----------|
| --chain | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1)) | int    | no        |
| --hash  | a transaction hash                                                                           | hex    | yes       |

Example: `$ et get-transaction --hash=0x9b629147b75dc0b275d478fa34d97c5d4a26926457540b15a5ce871df36c23fd`

### get-receipt

Get the receipt of a completed transaction.

Usage: `$ et get-receipt [options]`

| option  | description                                                                                  | type   | mandatory |
|---------|----------------------------------------------------------------------------------------------|--------|-----------|
| --chain | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1)) | int    | no        |
| --hash  | a transaction hash                                                                           | hex    | yes       |

Example: `$ et get-receipt --hash=0x9b629147b75dc0b275d478fa34d97c5d4a26926457540b15a5ce871df36c23fd`

### get-reason

Get the revert reason for a failed transaction.

Usage: `$ et get-reason [options]`

| option  | description                                                                                  | type   | mandatory |
|---------|----------------------------------------------------------------------------------------------|--------|-----------|
| --chain | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1)) | int    | no        |
| --hash  | a transaction hash                                                                           | hex    | yes       |

Usage: `$ et get-reason hash=0xf212cc42d0eded75041225d71da6c3a8348bdb9102f2b73434b480419d31d69a`

### open

Open transaction in a block explorer.

Usage: `$ et open [options]`

| option  | description                                                                                  | type   | mandatory |
|---------|----------------------------------------------------------------------------------------------|--------|-----------|
| --chain | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1)) | int    | no        |
| --hash  | a transaction hash                                                                           | hex    | yes       |

Example: `$ et open --hash=0x9b629147b75dc0b275d478fa34d97c5d4a26926457540b15a5ce871df36c23fd`

### cancel

Cancel a pending transaction.

Usage: `$ et cancel [options]`

| option  | description                                                                                  | type   | mandatory |
|---------|----------------------------------------------------------------------------------------------|--------|-----------|
| --chain | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1)) | int    | no        |
| --from  | the address of the sender                                                                    | hex    | yes       |
| --nonce | a transaction counter                                                                        | int    | yes       |

Example: `$ et cancel --from=0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045 --nonce=999`

Note: this command will return an unsigned transaction. This allows for wallets to prompt the user, sign the transaction, and send the raw transaction to the nearest RPC endpoint.

### get-price

Usage: `$ et get-price [options]`

Get [Chainlink](https://chain.link/)'s oracle price for an asset in USD.

| option    | description                                                                                  | type   | mandatory |
|-----------|----------------------------------------------------------------------------------------------|--------|-----------|
| --chain   | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1)) | int    | no        |
| --symbol  | a token symbol                                                                               | string | yes       |

Example: `$ et get-price --symbol=ETH`

### resolve

Usage: `$ et resolve [options]`

Resolve an [ENS](https://ens.domains/) name to an Ethereum address.

| option | description | type   | mandatory |
|--------|-------------|--------|-----------|
| --name | an ENS name | string | yes       |

Example: `$ et resolve --name=thecoinoffering.eth`

### reverse

Reverse resolution from an Ethereum address to an [ENS](https://ens.domains/) name.

Usage: `$ et reverse [options]`

| option    | description         | type | mandatory |
|-----------|---------------------|------|-----------|
| --address | an Ethereum address | hex  | yes       |

Example: `$ et reverse --address=0xa0400ba66b4fe0b13bb909abfd377596c02b6733`

### generate

Generate new private key.

Usage: `$ et generate`

### from-wei

Convert wei to any other unit (eg. gwei, ether, etc)

Usage: `$ et from-wei [options]`

| option   | description                                                                                                                                      | type   | mandatory |
|----------|--------------------------------------------------------------------------------------------------------------------------------------------------|--------|-----------|
| --value  | wei                                                                                                                                              | bigint | yes       |
| --to     | the unit you want to convert `--value` into. valid units are [here](https://github.com/svanas/delphereum/blob/master/web3.eth.utils.pas#L42-L65) | string | yes       |

Example: `$ et from-wei --value=9000000000 --to=gwei`

### to-wei

Convert any unit (eg. gwei, ether, etc) to wei.

Usage: `$ et to-wei [options]`

| option   | description                                                                                                                   | type   | mandatory |
|----------|-------------------------------------------------------------------------------------------------------------------------------|--------|-----------|
| --value  | any numerical value                                                                                                           | float  | yes       |
| --from   | the unit `--value` is in. valid units are [here](https://github.com/svanas/delphereum/blob/master/web3.eth.utils.pas#L42-L65) | string | yes       |

Example: `$ et to-wei --from=gwei --value=9`

### checksum

Convert an address to a [checksummed](https://eips.ethereum.org/EIPS/eip-55) address.

Usage: `$ et checksum [options]`

| option    | description                      | type   | mandatory |
|-----------|----------------------------------|--------|-----------|
| --address | an EOA or smart contract address | hex    | yes       |

Example: `$ et checksum --address=0x5aaeb6053f3e94c9b9a09f33669435e7ef1beaed`

### keccak256

Hash any input, using the [keccak-256](https://en.wikipedia.org/wiki/SHA-3) digest, output as hex.

Usage: `$ et keccak256 [options]`

| option | description            | type   | mandatory |
|--------|------------------------|--------|-----------|
| --hex  | hex-encoded bytes, or  | hex    | no        |
| --text | a UTF-8 encoded string | string | no        |

Example: `$ et keccak256 --text="Hello World"`

### scale

Convert a token balance from a float to a bigint.

Usage: `$ et scale [options]`

| option    | description                                                                                  | type   | mandatory |
|-----------|----------------------------------------------------------------------------------------------|--------|-----------|
| --chain   | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1)) | int    | no        |
| --symbol  | a ERC-20 token symbol, or                                                                    | string | no        |
| --address | a ERC-20 token address, and                                                                  | hex    | no        |
| --amount  | a token balance                                                                              | float  | yes       |

Example: `$ et scale --symbol=DAI --amount=99.99`

### unscale

Convert a token balance from a bigint to a float.

Usage: `$ et unscale [options]`

| option    | description                                                                                  | type   | mandatory |
|-----------|----------------------------------------------------------------------------------------------|--------|-----------|
| --chain   | a [chain id](https://chainlist.org/) (defaults to [Ethereum](https://chainlist.org/chain/1)) | int    | no        |
| --symbol  | a ERC-20 token symbol, or                                                                    | string | no        |
| --address | a ERC-20 token address, and                                                                  | hex    | no        |
| --amount  | a token balance                                                                              | bigint | yes       |

Example: `$ et unscale --symbol=DAI --amount=99990000000000000000`

## License

Distributed under the [GNU AGP v3.0](https://github.com/svanas/ethereum-terminal/blob/master/LICENSE) with [Commons Clause](https://commonsclause.com/) license.
