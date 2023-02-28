---
description: WoopChain QBFT proof of authority (PoA) consensus protocol implementation
---

# Configure QBFT consensus

WoopChain implements the QBFT proof of authority (PoA) [consensus protocol](index.md).
QBFT is the recommended enterprise-grade consensus protocol for private networks.

In QBFT networks, approved accounts, known as validators, validate transactions and blocks.
Validators take turns to create the next block. Before inserting the block onto the chain, a
super-majority (greater than or equal to 2/3) of validators must first sign the block.

Existing validators propose and vote to [add or remove validators](#add-and-remove-validators).

The QBFT properties are:

* `blockperiodseconds` - The minimum block time, in seconds.
* `epochlength` - The number of blocks after which to reset all votes.
* `requesttimeoutseconds` - The timeout for each consensus round before a round change, in seconds.
* `blockreward` - Optional reward amount in Wei to reward the beneficiary. Defaults to zero (0).
    Can be specified as a hexadecimal (with 0x prefix) or decimal string value. If set, then all
    nodes on the network must use the identical value.
* `validatorcontractaddress` - Address of the validator smart contract.
    Required only if using a contract validator selection.
    The address must be identical to the address in the `alloc` section. This option can also be
    used in the [transitions](#transitions) configuration item if swapping
    [validator management methods](#add-and-remove-validators) in an existing network.
* `miningbeneficiary` - Optional beneficiary of the `blockreward`. Defaults to the validator
    that proposes the block. If set, then all nodes on the network must use the same beneficiary.
* [`extraData`](#extra-data) - RLP encoded [extra data](#extra-data).



### Extra data

The `extraData` property is an RLP encoding of:

* 32 bytes of vanity data.
* If using:
    * [Block header validator selection](#add-and-remove-validators-using-block-headers), a list of validator addresses.
    * [Contract validator selection](#add-and-remove-validators-using-a-smart-contract), no validators.
* Any validator votes. No vote is included in the genesis block.
* The round the block was created on. The round in the genesis block is 0.
* A list of seals of the validators (signed block hashes). No seals are included in the genesis block.

When using block header validator selection, the important information in the genesis block extra data is the list of validators.
All other details have empty values in the genesis block.



    When using contract validator selection to manage validators, the list of validators is configured in the `alloc`
    property's `storage` section.
    [View the example smart contract] for more information on how to generate the `storage` section.

Formally, `extraData` in the genesis block contains:

* If using block header validator selection: `RLP([32 bytes Vanity, List<Validators>, No Vote, Round=Int(0), 0 Seals])`.
* If using contract validator selection: `RLP([32 bytes Vanity, 0 Validators, No Vote, Round=Int(0), 0 Seals])`.

!!! info

    RLP encoding is a space-efficient object serialization scheme used in Ethereum.


### Block time

When the protocol receives a new chain head, the block time (`blockperiodseconds`) timer starts.
When `blockperiodseconds` expires, the round timeout (`requesttimeoutseconds`) timer starts and
the protocol proposes a new block.

If `requesttimeoutseconds` expires before adding the proposed block, a round change occurs, with
the block time and timeout timers reset. The timeout period for the new round is two times
`requesttimeoutseconds`. The timeout period continues to double each time a round fails to add a
block.

Usually, the protocol adds the proposed block before reaching `requesttimeoutseconds`. A new round
then starts, resetting the block time and round timeout timers. When `blockperiodseconds`
expires, the protocol proposes the next new block.

Once `blockperiodseconds` is over, the time from proposing a block to adding the block is
small (usually around one second) even in networks with geographically dispersed validators.


## Add and remove validators

QBFT provides two methods to manage validators:

* [Block header validator selection](#add-and-remove-validators-using-block-headers) - Existing validators propose and
  vote to add or remove validators using the QBFT JSON-RPC API methods.

* [Contract validator selection](#add-and-remove-validators-using-a-smart-contract) - Use a smart contract to specify
  the validators used to propose and validate blocks.

You can use [transitions](#transitions) to swap between block header validator selection and contract
validator selection in an existing network.

For block header validator selection, initial validators are configured in the genesis file's
[`extraData`](#extra-data) property, whereas the initial validators when using the contract validator selection
method are configured in the genesis file's `storage` section.

### Add and remove validators using block headers

Enable the HTTP interface with [`--rpc-http-enabled`](../../../../reference/cli/options.md#rpc-http-enabled) or the
WebSockets interface with [`--rpc-ws-enabled`](../../../../reference/cli/options.md#rpc-ws-enabled).

The QBFT API methods are disabled by default.
To enable them, specify the [`--rpc-http-api`](../../../../reference/cli/options.md#rpc-http-api) or
[`--rpc-ws-api`](../../../../reference/cli/options.md#rpc-ws-api) option and include `QBFT`.

The methods to add or remove validators are:

* [`qbft_getPendingVotes`](../../../reference/api/index.md#qbft_getpendingvotes).
* [`qbft_proposeValidatorVote`](../../../reference/api/index.md#qbft_proposevalidatorvote).
* [`qbft_discardValidatorVote`](../../../reference/api/index.md#qbft_discardvalidatorvote).

To view validator metrics for a specified block range, use
[`qbft_getSignerMetrics`](../../../reference/api/index.md#qbft_getsignermetrics).

!!! note

    If network conditions render it impossible to add and remove validators by voting, you can
    [add and remove validators without voting](add-validators-without-voting.md).

#### Add a validator

To propose adding a validator, call
[`qbft_proposeValidatorVote`](../../../reference/api/index.md#qbft_proposevalidatorvote),
specifying the address of the proposed validator and `true`. A majority of validators must execute
the call.

!!! example "JSON-RPC `qbft_proposeValidatorVote` request example"

    ```bash
    curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_proposeValidatorVote","params":["0xFE3B557E8Fb62b89F4916B721be55cEb828dBd73", true], "id":1}' <JSON-RPC-endpoint:port>
    ```

When the validator proposes the next block, the protocol inserts one proposal received from
[`qbft_proposeValidatorVote`](../../../reference/api/index.md#qbft_proposevalidatorvote) into the
block. If blocks include all proposals, subsequent blocks proposed by the validator will not
contain a vote.

When more than 50% of the existing validators have published a matching proposal, the protocol
adds the proposed validator to the validator pool and the validator can begin validating blocks.

To return a list of validators and confirm the addition of a proposed validator, use
[`qbft_getValidatorsByBlockNumber`](../../../reference/api/index.md#qbft_getvalidatorsbyblocknumber).

!!! example "JSON-RPC `qbft_getValidatorsByBlockNumber` request example"

    ```bash
    curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_getValidatorsByBlockNumber","params":["latest"], "id":1}' <JSON-RPC-endpoint:port>
    ```

To discard your proposal after confirming the addition of a validator, call
[`qbft_discardValidatorVote`](../../../reference/api/index.md#qbft_discardvalidatorvote),
specifying the address of the proposed validator.

!!! example "JSON-RPC `qbft_discardValidatorVote` request example"

    ```bash
    curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_discardValidatorVote","params":["0xFE3B557E8Fb62b89F4916B721be55cEb828dBd73"], "id":1}' <JSON-RPC-endpoint:port>
    ```

#### Remove a validator

The process for removing a validator is the same as adding a validator except you specify `false`
as the second parameter of
[`qbft_proposeValidatorVote`](../../../reference/api/index.md#qbft_proposevalidatorvote).

#### Epoch transition

At each epoch transition, QBFT discards all pending votes collected from received blocks.
Existing proposals remain in effect and validators re-add their vote the next time they create a
block.

An epoch transition occurs every `epochLength` blocks. Define `epochlength` in the QBFT genesis
file.

### Add and remove validators using a smart contract

In a new QBFT network by specifying the contract details in the [genesis file](qbft.md#genesis-file). 

You can't use the JSON-RPC methods to add or remove validators when using a smart contract to manage nodes.
You must interact with the contract functions using transactions.

If network conditions render it impossible to add and remove validators using a smart contract, you can
[override smart contract validators](add-validators-without-voting.md#override-smart-contract-validators).

### Minimum number of validators

QBFT requires four validators to be Byzantine fault tolerant. Byzantine fault tolerance is the
ability for a blockchain network to function correctly and reach consensus despite nodes failing or
propagating incorrect information to peers.

## Transitions

The `transitions` genesis configuration item allows you to specify a future block number at which to change QBFT
network configuration in an existing network.
For example, you can update the [block time](#configure-block-time-on-an-existing-network),
[block reward](#configure-block-rewards-on-an-existing-network-deployment),
[validator management method](#swap-validator-management-methods), or
[mining beneficiary](#configure-the-mining-beneficiary-on-an-existing-network-deployment).

Do not specify a transition block in the past.
Specifying a transition block in the past could result in unexpected behavior, such as causing the network to fork.

### Configure block time on an existing network

To update an existing network with a new `blockperiodseconds`:

1. Stop all nodes in the network.
2. In the [genesis file](#genesis-file), add the `transitions` configuration item where:

    * `<FutureBlockNumber>` is the upcoming block at which to change `blockperiodseconds`.
    * `<NewValue>` is the updated value for `blockperiodseconds`.

    !!! example "Transitions configuration"

        === "Syntax"

            ```bash
            {
              "config": {
                ...
                "qbft": {
                  "blockperiodseconds": 2,
                  "epochlength": 30000,
                  "requesttimeoutseconds": 4
                },
                "transitions": {
                  "qbft": [
                    {
                      "block": <FutureBlockNumber>,
                      "blockperiodseconds": <NewValue>
                    }
                  ]
                }
              },
              ...
            }
            ```

        === "Example"

            ```bash
            {
              "config": {
                ...
                "qbft": {
                  "blockperiodseconds": 2,
                  "epochlength": 30000,
                  "requesttimeoutseconds": 4
                },
                "transitions": {
                  "qbft": [
                    {
                      "block": 1240,
                      "blockperiodseconds": 4
                    }
                  ]
                }
              },
              ...
            }
            ```

3. Restart all nodes in the network using the updated genesis file.
4. To verify the changes after the transition block, call
    [`qbft_getValidatorsByBlockNumber`](../../../reference/api/index.md#ibft_getvalidatorsbyblocknumber), specifying `latest`.

### Configure block rewards on an existing network deployment

To update an existing network with a new `blockreward`:

1. Stop all nodes in the network.
2. In the [genesis file](#genesis-file), add the `transitions` configuration item where:

    * `<FutureBlockNumber>` is the upcoming block at which to change `blockreward`.
    * `<NewValue>` is the updated value for `blockreward`.

    !!! example "Transitions configuration"

        === "Syntax"

            ```bash
            {
              "config": {
                ...
                "qbft": {
                  "blockperiodseconds": 2,
                  "epochlength": 30000,
                  "requesttimeoutseconds": 4
                  "blockreward": "5000000000000000"
                },
                "transitions": {
                  "qbft": [
                    {
                      "block": <FutureBlockNumber>,
                      "blockreward": <NewValue>
                    },
                    {
                      "block": <FutureBlockNumber>,
                      "blockreward": <NewValue>
                    },
                    {
                      "block": <FutureBlockNumber>,
                      "blockreward": <NewValue>
                    }
                  ]
                }
              },
              ...
            }
            ```

        === "Example"

            ```bash
            {
              "config": {
                ...
                "qbft": {
                  "blockperiodseconds": 2,
                  "epochlength": 30000,
                  "requesttimeoutseconds": 4
                  "blockreward": "5000000000000000"
                },
                "transitions": {
                  "qbft": [
                    {
                      "block": 10,
                      "blockreward": "6000000000000000"
                    },
                    {
                      "block": 15,
                      "blockreward": "75000000000000000"
                    },
                    {
                      "block": 20,
                      "blockreward": "0"
                    }
                  ]
                }
              },
              ...
            }
            ```

    !!! note

        You can add multiple `blockreward` updates in one transition object by specifying multiple future blocks.

3. Restart all nodes in the network using the updated genesis file.

### Swap validator management methods

To swap between block header validator selection and contract validator selection methods in an existing network:

1. Stop all nodes in the network.
2. In the [genesis file](#genesis-file), add the `transitions` configuration item where:

    * `<FutureBlockNumber>` is the upcoming block at which to change the validator selection method.
    * `<SelectionMode>` is the validator selection mode to switch to. Valid options are `contract` and `blockheader`.
    * `<ContractAddress>` is the smart contract address, if switching to the contract validator selection method.

    !!! example "Transitions configuration"

        === "Syntax"

            ```bash
            {
              "config": {
                ...
                "qbft": {
                  "blockperiodseconds": 5,
                  "epochlength": 30000,
                  "requesttimeoutseconds": 10
                },
                "transitions": {
                  "qbft": [
                    {
                      "block": <FutureBlockNumber>,
                      "validatorselectionmode": <SelectionMode>,
                      "validatorcontractaddress": <ContractAddress>
                    }
                  ]
                }
              },
              ...
            }
            ```

        === "Example"

            ```bash
            {
              "config": {
                ...
                "qbft": {
                  "blockperiodseconds": 5,
                  "epochlength": 30000,
                  "requesttimeoutseconds": 10
                },
                "transitions": {
                  "qbft": [
                  {
                    "block": 102885,
                    "validatorselectionmode": "contract",
                    "validatorcontractaddress": "0x0000000000000000000000000000000000007777"
                  }
                  ]
                }
              },
              ...
            }
            ```

3. Restart all nodes in the network using the updated genesis file.

### Configure the mining beneficiary on an existing network deployment

To update an existing network with a new mining beneficiary:

1. Stop all nodes in the network.
2. In the [genesis file](#genesis-file), add the `transitions` configuration item where:

    * `<FutureBlockNumber>` is the upcoming block at which to change `miningbeneficiary`.
    * `<NewAddress>` is the updated 20-byte address for `miningbeneficiary`.
      Starting at `<FutureBlockNumber>`, block rewards go to this address.

    !!! example "Transitions configuration"

        === "Syntax"

            ```bash
            {
              "config": {
                ...
                "qbft": {
                  "blockperiodseconds": 5,
                  "epochlength": 30000,
                  "requesttimeoutseconds": 10
                },
                "transitions": {
                  "qbft": [
                  {
                    "block": <FutureBlockNumber>,
                    "miningbeneficiary": <NewAddress>
                  },
                  {
                    "block": <FutureBlockNumber>,
                    "miningbeneficiary": <NewAddress>
                  }
                  ]
                }
              },
              ...
            }
            ```

        === "Example"

            ```bash
            {
              "config": {
                ...
                "qbft": {
                  "blockperiodseconds": 5,
                  "epochlength": 30000,
                  "requesttimeoutseconds": 10
                },
                "transitions": {
                  "qbft": [
                  {
                    "block": 10000,
                    "miningbeneficiary": "0x0000000000000000000000000000000000000002",
                  },
                  {
                    "block": 20000,
                    "miningbeneficiary": "",
                  }
                  ]
                }
              },
              ...
            }
            ```

    !!! note

        Setting the `miningbeneficiary` to an empty value clears out any override so that block rewards go to the
        block producer rather than a global override address.

3. Restart all nodes in the network using the updated genesis file.

<!-- Acronyms and Definitions -->

*[vanity data]: Validators can include anything they like as vanity data.
*[RLP]: Recursive Length Prefix

