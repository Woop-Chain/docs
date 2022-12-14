---
id: overview 
title: WoopChain
sidebar_label: What is WoopChain
description: "An introduction to WoopChain."
keywords:
  - docs
  - woopchain
  - woop
  - network
  - modular
  
---

WoopChain is a modular and extensible Ethereum-compatible blockchain network and general scaling solution.

Its primary use is to bootstrap a new blockchain network while providing full compatibility with Ethereum smart contracts and transactions. It uses IBFT (Istanbul Byzantine Fault Tolerant) consensus mechanism, supported in two flavours as [PoA (proof of authority)](/docs/woop/consensus/poa) and [PoS (proof of stake)](/docs/woop/consensus/pos-stake-unstake).

WoopChain also supports communication with multiple blockchain networks, enabling transfers of both [ERC-20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20) and [ERC-721](https://ethereum.org/en/developers/docs/standards/tokens/erc-721) tokens.

Industry standard wallets can be used to interact with WoopChain through the [JSON-RPC](/docs/woop/working-with-node/query-json-rpc) endpoints and node operators can perform various actions on the nodes through the [gRPC](/docs/woop/working-with-node/query-operator-info) protocol.

To find out more about WoopChain, visit the [official website](https://wikiwoop.com).

**[GitHub repository](https://github.com/Woop-Chain/woopchain)**

To get started by running a `woopchain` network locally, please read: [Installation](/docs/woop/get-started/installation) and [Local Setup](/docs/woop/get-started/set-up-ibft-locally).
