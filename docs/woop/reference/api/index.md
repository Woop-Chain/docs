---
description: WoopChain network JSON-RPC API methods reference
---

# API methods

!!! attention

    * This reference contains API methods that apply to only private networks.
      For API methods that apply to both private and public networks, see the
      [public network API reference](../../../reference/api/index.md).
    * All JSON-RPC HTTP examples use the default host and port endpoint `http://127.0.0.1:8545`. If
      using the [--rpc-http-host](../../../reference/cli/options.md#rpc-http-host) or
      [--rpc-http-port](../../../reference/cli/options.md#rpc-http-port) options, update the endpoint.

## `QBFT` methods

The `QBFT` API methods provide access to the [QBFT](../../how-to/configure/consensus/qbft.md) consensus engine.

!!! note

    The `QBFT` API methods are not enabled by default for JSON-RPC. To enable the `QBFT` API
    methods, use the [`--rpc-http-api`](../../../reference/cli/options.md#rpc-http-api) or
    [`--rpc-ws-api`](../../../reference/cli/options.md#rpc-ws-api) options.

### `qbft_discardValidatorVote`

Discards a proposal to
[add or remove a validator](../../how-to/configure/consensus/qbft.md#add-and-remove-validators) with
the specified address.

#### Parameters

`address`: *string* - 20-byte address of proposed validator

#### Returns

`result`: *boolean* - indicates if the proposal is discarded

!!! example

    === "curl HTTP request"

        ```bash
        curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_discardValidatorVote","params":["0xef1bfb6a12794615c9b0b5a21e6741f01e570185"], "id":1}' http://127.0.0.1:8545
        ```

    === "wscat WS request"

        ```bash
        {"jsonrpc":"2.0","method":"qbft_discardValidatorVote","params":["0xef1bfb6a12794615c9b0b5a21e6741f01e570185"], "id":1}
        ```

    === "JSON result"

        ```json
        {
          "jsonrpc" : "2.0",
          "id" : 1,
          "result" : true
        }
        ```

### `qbft_getPendingVotes`

Returns [votes](../../how-to/configure/consensus/qbft.md#add-and-remove-validators) cast in the current
[epoch](../../how-to/configure/consensus/qbft.md#genesis-file).

#### Parameters

None

#### Returns

`result`: *map* of *strings* to *booleans* - map of account addresses to corresponding boolean values indicating the
vote for each account; if `true`, the vote is to add a validator. If `false`, the proposal is to
remove a validator.

!!! example

    === "curl HTTP request"

        ```bash
        curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_getPendingVotes","params":[], "id":1}' http://127.0.0.1:8545
        ```

    === "wscat WS request"

        ```bash
        {"jsonrpc":"2.0","method":"qbft_getPendingVotes","params":[], "id":1}
        ```

    === "JSON result"

        ```json
        {
            "jsonrpc": "2.0",
            "id": 1,
            "result": {
                "0xef1bfb6a12794615c9b0b5a21e6741f01e570185": true,
                "0x42d4287eac8078828cf5f3486cfe601a275a49a5": true
            }
        }
        ```

### `qbft_getSignerMetrics`

Provides the following validator metrics for the specified range:

* Number of blocks from each validator

* Block number of the last block proposed by each validator (if any proposed in the specified
  range)

* All validators present in the last block of the range

#### Parameters

* `fromBlockNumber`: *string* - integer representing a block number or the string tag `earliest` as described
  in [Block Parameter](../../how-to/use-woop-api/json-rpc.md#block-parameter)

* `toBlockNumber`: *string* - integer representing a block number or one of the string tags `latest` or
  `pending`, as described in
  [Block Parameter](../../how-to/use-woop-api/json-rpc.md#block-parameter)

If you specify:

* No parameters, the call provides metrics for the last 100 blocks, or all blocks if there are less
  than 100 blocks.

* Only the first parameter, the call provides metrics for all blocks from the block specified to
  the latest block.

#### Returns

`result`: *array* of *objects* - list of validator objects

!!! note

    The proposer of the genesis block has address `0x0000000000000000000000000000000000000000`.

!!! example

    === "curl HTTP"

        ```bash
        curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_getSignerMetrics","params":["1", "100"], "id":1}' http://127.0.0.1:8545
        ```

    === "wscat WS"

        ```bash
        {"jsonrpc":"2.0","method":"qbft_getSignerMetrics","params":["1", "100"], "id":1}
        ```

    === "JSON result"

        ```json
        {
            "jsonrpc": "2.0",
            "id": 1,
            "result": [
                {
                    "address": "0x7ffc57839b00206d1ad20c69a1981b489f772031",
                    "proposedBlockCount": "0x21",
                    "lastProposedBlockNumber": "0x61"
                },
                {
                    "address": "0x42eb768f2244c8811c63729a21a3569731535f06",
                    "proposedBlockCount": "0x21",
                    "lastProposedBlockNumber": "0x63"
                },
                {
                    "address": "0xb279182d99e65703f0076e4812653aab85fca0f0",
                    "proposedBlockCount": "0x21",
                    "lastProposedBlockNumber": "0x62"
                }
            ]
        }
        ```

### `qbft_getValidatorsByBlockHash`

Lists the validators defined in the specified block.

#### Parameters

`block`: *string* - 32-byte block hash

#### Returns

`result`: *array* of *strings* - list of validator addresses

!!! example

    === "curl HTTP request"

        ```bash
        curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_getValidatorsByBlockHash","params":["0xbae7d3feafd743343b9a4c578cab5e5d65eb735f6855fb845c00cab356331256"], "id":1}' http://127.0.0.1:8545
        ```

    === "wscat WS request"

        ```bash
        {"jsonrpc":"2.0","method":"qbft_getValidatorsByBlockHash","params":["0xbae7d3feafd743343b9a4c578cab5e5d65eb735f6855fb845c00cab356331256"], "id":1}
        ```

    === "JSON result"

        ```json
        {
            "jsonrpc": "2.0",
            "id": 1,
            "result": [
                "0x42d4287eac8078828cf5f3486cfe601a275a49a5",
                "0xb1b2bc9582d2901afdc579f528a35ca41403fa85",
                "0xef1bfb6a12794615c9b0b5a21e6741f01e570185"
            ]
        }
        ```

### `qbft_getValidatorsByBlockNumber`

Lists the validators defined in the specified block.

#### Parameters

* `blockNumber`: *string* - integer representing a block number or one of the string tags `latest`,
  `earliest`, or `pending`, as described in
  [Block Parameter](../../../how-to/use-woop-api/json-rpc.md#block-parameter)

#### Returns

`result`: *array* of *strings* - list of validator addresses

!!! example

    === "curl HTTP request"

        ```bash
        curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_getValidatorsByBlockNumber","params":["latest"], "id":1}' http://127.0.0.1:8545
        ```

    === "wscat WS request"

        ```bash
        {"jsonrpc":"2.0","method":"qbft_getValidatorsByBlockNumber","params":["latest"], "id":1}
        ```

    === "JSON result"

        ```json
        {
            "jsonrpc": "2.0",
            "id": 1,
            "result": [
                "0x42d4287eac8078828cf5f3486cfe601a275a49a5",
                "0xb1b2bc9582d2901afdc579f528a35ca41403fa85",
                "0xef1bfb6a12794615c9b0b5a21e6741f01e570185"
            ]
        }
        ```

### `qbft_proposeValidatorVote`

Proposes to
[add or remove a validator](../../how-to/configure/consensus/qbft.md#add-and-remove-validators) with
the specified address.

#### Parameters

* `address`: *string* - account address

* `proposal`: *boolean* - `true` to propose adding validator or `false` to propose removing validator

#### Returns

`result`: *boolean* - `true`

!!! example

    === "curl HTTP request"

        ```bash
        curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_proposeValidatorVote","params":["42d4287eac8078828cf5f3486cfe601a275a49a5",true], "id":1}' http://127.0.0.1:8545
        ```

    === "wscat WS request"

        ```bash
        {"jsonrpc":"2.0","method":"qbft_proposeValidatorVote","params":["42d4287eac8078828cf5f3486cfe601a275a49a5",true], "id":1}
        ```

    === "JSON result"

        ```json
        {
          "jsonrpc" : "2.0",
          "id" : 1,
          "result" : true
        }
        ```

<!-- Links -->
[add or remove a validator]: ../../how-to/configure/consensus/qbft.md#add-and-remove-validators

*[EEA]: Enterprise Ethereum Alliance
