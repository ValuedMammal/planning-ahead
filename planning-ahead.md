# Planning ahead - building transactions with complex spending conditions in BDK

### Outline
- Background on Bdks policy stuff
    - 
- Introducing the Plan
- UX for tx building
- Example descriptor + concept check ?s

### Background

Bdk's policy stuff
policy path concept: node_id -> path
"abc123": [0,1]
It's a clever soln. but pretty arbitrary and a bit clunky

### Introducing the Plan

- The spending path is determined by the assets available
- Kinds of assets?
- Why go to the trouble of planning your spendable outputs?
    - More intuitive UX
    - To avoid revealing more information than necessary about various spending conditions at the time of spending
    - Efficiency, cost savings: larger scripts require more resources to validate and cost more to satisfy

### UX 

with API examples

At a minimum we need to know the inputs and outputs
(ignoring coin selection, assume we're able to select inputs)

<!-- schematic flow -->

- Input from prev_tx
    - plan
        - Plan from definite_desc + assets
            - Definite desc from descriptor at derivation idx
            - Assets from keys, hash preimages, timelocks
    - tx
    - vout
    - tx conf. status

- Output from value, SPK
    - remember to include change
- Obtain the selection
- Create psbt with parameters

### Example

example descriptor: Inheritance plan

Essentially two ways to spend: 2-of-2, or 1sig + rel. timelock

<!-- Wsh -->
andor: "wsh(andor(pk(A),pk(B),and_v(v:pk(C),after(52560))))"

taproot with:
- key path internal key
- script path pk and rel timelock
- script path multi 2 pk

<!-- Tr -->
and_v: "tr(X,{and_v(v:pk(C),older(52560)),and_v(v:pk(A),pk(B))})"

multi_a: "tr(X,{multi_a(2,A,B),and_v(v:pk(C),older(52560))})"

The information in the psbt (e.g. input tapscripts) varies depending on the assets provided.

Assets
pk (internal): X,  
pk: A, B, C  
older: 52560 blocks  

Concept check Qs

For each set of assets, what is the chosen spend path, and what is the resulting tapscript?

1. Keys A + B  
    and_v:   `<A> OP_CHECKSIGVERIFY <B> OP_CHECKSIG`  
    multi:   `2 <A> <B> 2 OP_CHECKMULTISIG`  
    multi_a: `<A> OP_CHECKSIG <B> OP_CHECKSIGADD OP_PUSHNUM_2 OP_NUMEQUAL`  
1a. Key A  
    Err: The assets are insufficient
2. Key C + older fragment  
    csv: `<C> OP_CHECKSIGVERIFY <50cd00> OP_CHECKSEQUENCEVERIFY`
3. Say we just lazily add all of the assets indiscriminately  
    ans: The whole idea of the Plan construct is to find the minimum satisfaction, so in this case just the internal key is used, no tapscript ref: [`best_tap_spend`][0].  
    Clearly not the best use of the API, which otherwise enables powerful/expressive script construction  
    `<I> OP_CHECKSIG`

<!-- References -->
[0]: <https://github.com/rust-bitcoin/rust-miniscript/blob/2ca5a35d0b5277940a3d75f4daf8f8429bb56dbd/src/descriptor/tr/mod.rs#L457>

# TABconf talk proposal

# Description

Planning ahead: building transactions with complex spending conditions in BDK

### What is this talk about? Give us as many details as possible. 

BDK offers a range of tooling designed to be used in descriptor-based wallets.
An output descriptor defines the set of conditions which need to be satisfied in order to spend bitcoin.
A descriptor can describe something as simple as a single-sig wallet, or arbitrarily complex spending conditions commonly used in the creation of high-security vaults.
Policy language is a human-readable way to describe the exact script semantics which can then be compiled to miniscript.
Miniscript is a subset of Bitcoin Script that has been formally specified and enabling safe composition and analysis.

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

# About the Speaker

I'm a developer working on Bitcoin Dev Kit funded by BDK Foundation.

### Social Links

Github: @ValuedMammal

# Talk Details

### Length of Talk

30min

### Preferred Day/Time Slot 

*We will do our best to accommodate your requested time slot. Please let us know if there are any dates/times that ***absolutely do not work*** for you.*
