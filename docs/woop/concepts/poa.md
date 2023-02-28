---
description: Woop proof of authority consensus protocols comparison
---

# Proof of authority consensus

Woop implements the QBFT, proof of authority (PoA) [consensus protocols](../how-to/configure/consensus/index.md).
PoA consensus protocols work when participants know each other and there is a level of trust
between them.

PoA consensus protocols have faster block times and a much greater transaction
throughput than the Ethash proof of work consensus protocol used on the Ethereum Mainnet.

In QBFT, a group of nodes in the network act as validators.
The existing nodes in the signer/validator pool vote to add nodes to or remove nodes from the pool.


## Properties

* Immediate finality.
* Minimum number of validators.
* Liveness.
* Speed.

### Immediate finality

QBFT has immediate finality; there are no forks and all valid blocks get
included in the main chain.


### Minimum number of validators

To be Byzantine fault tolerant, QBFT requires a minimum of four validators.


### Liveness

QBFT networks require greater than or equal to two-thirds of validators to be
operating to create blocks. For example, a QBFT of:

* Four to five validators tolerates one unresponsive validator.
* Six to eight validators tolerates two unresponsive validators.

Networks with three or less validators can produce blocks but do not guarantee finality when
operating in adversarial environments.

### Speed

For QBFT , the time to add new blocks increases as the number of validators increases.
