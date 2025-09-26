# Planning ahead - Building transactions with complex spending conditions in BDK

### What is this talk about? 

BDK offers a range of tooling designed to be used in descriptor-based wallets.
An output descriptor defines the set of conditions which need to be satisfied in order to spend bitcoin.
A descriptor can describe something as simple as a single-sig wallet, or arbitrarily complex spending conditions commonly used in the creation of high-security vaults.
Policy language is a human-readable way to describe the exact script semantics which can then be compiled to miniscript.
Miniscript is a subset of Bitcoin Script that has been formally specified and enables safe composition and analysis.

If multiple spending conditions are possible, then we as the user have to decide which path to take and the conditions which need to be met.
This is relevant to transaction building because complex scripts tend to have a larger onchain footprint and are more costly to satisfy.
The spending plan gives us insight about how an input contributes to the weight of a transaction allowing us to select inputs in a way that meets a desired fee rate.

### What would an attendee learn from this talk?

- How to start making transactions using BDK
- or if you are currently using BDK what to know about tx building in 2025 and beyond.

### Is there anything folks should read up on before they attend this talk?

- Output descriptors
- Miniscript

### Relevant Links

- `descriptors.md` <https://github.com/bitcoin/bitcoin/blob/master/doc/descriptors.md>  
- BIP-0379 <https://github.com/bitcoin/bips/blob/master/bip-0379.md>  
- Taproot and Policy with James Chiang <https://www.youtube.com/watch?v=EdRm_mnoCWc>  

### Social Links

Github: @ValuedMammal  
X: @valuedmammal
