---
id: bls
title: BLS
description: "Explanation and instructions regarding BLS mode."
keywords:
  - docs
  - woopchain
  - woop
  - bls
---

## Overview

BLS is a signature scheme that can aggregate multiple signatures. In WoopChain, BLS is used by default in order to provide better security in the IBFT consensus mode. BLS can aggregate signatures into a single byte array and reduce the block header size. The ECDSA key is used regardless of whether the BLS mode is enabled or not.

