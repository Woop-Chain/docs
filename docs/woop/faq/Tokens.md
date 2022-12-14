---
id: tokens
title: Tokens FAQ
description: "FAQ for WoopChain tokens"
keywords:
  - docs
  - woopchain
  - woop
  - FAQ
  - tokens
---

## Does WoopChain support EIP-1559?
At the moment, WoopChain does not support EIP-1559.

## What happens to transactions when a chain halts?

All the transactions that haven't been processed are inside the TxPool(enqueued or promoted queue). If the chain halts(all block production stops), these transactions will never go into blocks. <br/>
This is not only the case when the chain halts. If the nodes are stopped or restarted, all the transactions that haven't been executed, and are still in TxPool, will silently get removed. <br/>
The same thing will happen to transactions when a breaking change is introduced, as it is required for the nodes to be restarted.
