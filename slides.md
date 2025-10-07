---
marp: true
theme: my-theme
_class: lead
paginate: false
backgroundColor: #FFFFFF

---

# Planning ahead 📋

#### Building transactions with complex spending conditions in BDK

<br>

**Github**: @ValuedMammal
**X**: @valuedmammal

<!-- about me -->

---

<!-- _class: slide_small -->

## Policy

<!-- Background & Why should we care -->
<!-- def: Policy describes 1+ spending conditions -->
<!-- Can be composed of a number of individual sub-policies -->
<!-- Nested structure that represents an abstract syntax tree in a human readable language -->
<!-- Why: moving beyond single-sig -->
<!-- encoding contracts, contingency plans at the level of Bitcoin Script -->
<!-- enables more complex contracts e.g. vaults -->
<!-- better security, privacy -->
<!-- Example -->
<!-- Note that in the real world, A, B, and C will be substituted with actual keys, xpubs, etc -->

This policy says: "Both A and B must sign, or C can sign after 144 blocks have passed since the UTXO was created."

```
or(
    and(pk(A), pk(B)),
    and(pk(C), older(144))
)
```

<br>

Compiled miniscript descriptor (P2WSH)
    
```
wsh(andor(pk(A),pk(B),and_v(v:pk(C),older(144))))
```

A: `[e5c7abd3/84h/0h/0h]xpub6BtRAUCU5BTosU6UhHXAskUqHbb5Ud2ijxWgwV6HoLYpdZJqBAiWhkMiPVWFHRMn2GEssUDDq3mxCgyGHARtwHQZoyXxXDtR5ToUvq3cU7d/0/*`  
B: `[9421f833/84h/0h/0h]xpub6CiZyifs86jgdhz6EHPRkqBWPMuJHjooamCLHFXKB6bzDKo6SS55UVCKEUcCZo5KChJkB29tP79H7mFieJtiRDJTiJbcuUnkD6drFhW4cd4/0/*`
C: `[6de9955d/84h/0h/0h]xpub6BrWXUpwTKEfNqBxJZAFbEgAv5aGr5CuUKKBrTqGSpAPhwT9wkKp2ydvamUxcNNKad7qzkzpWeoySKXceBKcFHAJCvjD8og7wvxbV8agsrF/0/*`

---
## The `policy_path` API

https://docs.rs/bdk_wallet/2.1.0/bdk_wallet/struct.TxBuilder.html#method.policy_path

<!-- Describe the API -->
![](assets/policy-path-docs-light.jpg)

---

<!-- Failure to do any of the aforementioned steps -->
<!-- will definitely result in an error that might look like this -->
<!-- It's important to understand WHY this error occurs -->
![](assets/ferris.png)
**Error: Spending policy required: External**
---

---
### Policy object
<!-- _class: slide_small -->
```
wsh(thresh(2,pk(A),sj:and_v(v:pk(B),n:older(6)),snj:and_v(v:pk(C),after(630000))))
```
<!-- Policy object -->
<!-- Things to note: -->
<!-- Nested data structure -->
<!-- painful UX -->
<!-- Policy ID: checksum of the json serialized policy node item -->
<!-- not easily reproducible depending on which keys happen to be available -->
<!-- list of indices into the items beneath said node -->

```
{
    id: "w4vpzs9c",
    item: Thresh {
        items: [
            Policy {
                id: "j7fc067u",
                item: EcdsaSignature(
                    Pubkey(
                        PublicKey {
                            compressed: true,
                            inner: PublicKey(
                                66590627d359ecdebc304e50df2e78ddaf1a5fb234ff30de5aec671e7dd027bd1bae571da757302f129f7ddc66f114efbe0fe31bd966c3ad32efdc064d90e7d3,
                            ),
                        },
                    ),
                ),
                satisfaction: None,
                contribution: Complete {
                    condition: Condition {
                        csv: None,
                        timelock: None,
                    },
                },
            },
            Policy {
                id: "e844ja4f",
                item: Thresh {
                    items: [
                        Policy {
                            id: "n3sj28kh",
                            item: EcdsaSignature(
                                Pubkey(
                                    PublicKey {
                                        compressed: true,
                                        inner: PublicKey(
                                            9cb0f5f0acecc3668e1c27c66a296d0baf8f8c0ca784eff9e34c9c1382dd897cd967404e190afc80ec5370daa7e884a7c6d059308a80994e0ae2075198f5a7c3,
                                        ),
                                    },
                                ),
                            ),
                            satisfaction: None,
                            contribution: Complete {
                                condition: Condition {
                                    csv: None,
                                    timelock: None,
                                },
                            },
                        },
                        Policy {
                            id: "cmxxtyq5",
                            item: RelativeTimelock {
                                value: Blocks(
                                    Height(
                                        6,
                                    ),
                                ),
                            },
                            satisfaction: None,
                            contribution: Complete {
                                condition: Condition {
                                    csv: Some(
                                        Sequence(0x00000006),
                                    ),
                                    timelock: None,
                                },
                            },
                        },
                    ],
                    threshold: 2,
                },
                satisfaction: Partial {
                    n: 2,
                    m: 2,
                    items: [],
                    sorted: None,
                    conditions: {},
                },
                contribution: PartialComplete {
                    n: 2,
                    m: 2,
                    items: [
                        0,
                        1,
                    ],
                    sorted: None,
                    conditions: {
                        [
                            0,
                            1,
                        ]: {
                            Condition {
                                csv: Some(
                                    Sequence(0x00000006),
                                ),
                                timelock: None,
                            },
                        },
                    },
                },
            },
            Policy {
                id: "e5wzq6h4",
                item: Thresh {
                    items: [
                        Policy {
                            id: "dvt7p936",
                            item: EcdsaSignature(
                                Pubkey(
                                    PublicKey {
                                        compressed: true,
                                        inner: PublicKey(
                                            7600ae9258ef3a68c8da7949b537a49a28fd7f55222dc9814dc2d544c4e6d7e8b3c413ff9a22975ab574e1deb673fca6b1898008fd11dde692b287cc08035670,
                                        ),
                                    },
                                ),
                            ),
                            satisfaction: None,
                            contribution: Complete {
                                condition: Condition {
                                    csv: None,
                                    timelock: None,
                                },
                            },
                        },
                        Policy {
                            id: "xpf2twg8",
                            item: AbsoluteTimelock {
                                value: 630000 blocks,
                            },
                            satisfaction: None,
                            contribution: Complete {
                                condition: Condition {
                                    csv: None,
                                    timelock: Some(
                                        630000 blocks,
                                    ),
                                },
                            },
                        },
                    ],
                    threshold: 2,
                },
                satisfaction: Partial {
                    n: 2,
                    m: 2,
                    items: [],
                    sorted: None,
                    conditions: {},
                },
                contribution: PartialComplete {
                    n: 2,
                    m: 2,
                    items: [
                        0,
                        1,
                    ],
                    sorted: None,
                    conditions: {
                        [
                            0,
                            1,
                        ]: {
                            Condition {
                                csv: None,
                                timelock: Some(
                                    630000 blocks,
                                ),
                            },
                        },
                    },
                },
            },
        ],
        threshold: 2,
    },
// ...
```
---
### Policy object (cont'd)
<!-- _class: slide_small -->
```
wsh(thresh(2,pk(A),sj:and_v(v:pk(B),n:older(6)),snj:and_v(v:pk(C),after(630000))))
```

```
// ...
    satisfaction: Partial {
        n: 3,
        m: 2,
        items: [],
        sorted: None,
        conditions: {},
    },
    contribution: PartialComplete {
        n: 3,
        m: 2,
        items: [
            0,
            1,
            2,
        ],
        sorted: None,
        conditions: {
            [
                0,
                1,
            ]: {
                Condition {
                    csv: Some(
                        Sequence(0x00000006),
                    ),
                    timelock: None,
                },
            },
            [
                0,
                2,
            ]: {
                Condition {
                    csv: None,
                    timelock: Some(
                        630000 blocks,
                    ),
                },
            },
            [
                1,
                2,
            ]: {
                Condition {
                    csv: Some(
                        Sequence(0x00000006),
                    ),
                    timelock: Some(
                        630000 blocks,
                    ),
                },
            },
        },
    },
}
```

---
## Introducing the `Plan`

#### What:

"A plan represents a particular spending path on a descriptor. It contains the witness template and
the timelocks needed for satisfying the plan. Calling `plan` on a descriptor will return this
structure containing the cheapest spending path possible considering the assets given."

<https://docs.rs/miniscript/12.3.5/miniscript/plan/struct.Plan.html>

#### Why:
The plan finds the minimum satisfaction out of a set of satisfiable conditions.
This has a few benefits:

* Preserves privacy by not revealing every possible condition
* Saves on-chain fees

#### How:
* "...calling `plan` on a descriptor... considering the assets given."
* But wait... **assets?**

---
### Primer on `Assets`

- **def**: Information that we bring to the transaction indicating the conditions we intend to satisfy.
- **example**: A single-sig transaction has a single satisfiable condition, i.e. the signature.
    Therefore the asset is the key with which we're able to produce the signature.
- **Note: The assets consist of public information.**
    A "key" is denoted by the combination of a fingerprint + derivation path (i.e. key source),
    along with the context in which it can sign (ECDSA, Schnorr).
- **Kinds of assets** include keys, hash locks, and timelocks

<!-- Directly analogous to the policy path, but more explicit -->
<!-- A note on terminology: Condition vs Satisfaction -->
<!-- H(preimage) -> hash -->

### UX
<!-- schematic flow -->
<!-- intuition, mental model -->

Descriptor + Derivation Index -> **Definite Descriptor**  
Definite Descriptor + Assets -> **Plan**  
Plan + UTXO -> **Input** (+ satisfaction weight)  
Input + Output + Feerate -> Coin Selection -> **PSBT**  
PSBT + Satisfaction (cryptographic signature) -> **Transaction**  

---
### Putting it together

<!-- Recap: In the end what did we accomplish? -->
1. The plan gives us the **minimum satisfaction**.
    Without this, we'd be forced to rely on something like the "max weight to satisfy",
    which could become wasteful if a single uncommon path requires a large satisfaction.
2. The **satisfaction weight** enables more efficient coin selection.
3. Plan lets us **update the PSBT** with the pertinent information without revealing too much.
    This is a prerequisite to signing.
4. **Finalizer** uses plans to construct the final scriptsig/witness.
    This is a prerequisite to extraction.

### New APIs
<!-- For single-sig everything should Just Work -->
- `PsbtParams::add_assets`
- `Wallet::create_psbt`
- `Finalizer::finalize_psbt`

See the code: <https://github.com/bitcoindevkit/bdk_wallet/pull/297>

---
## Example inheritance plan

<!-- Describe the policy -->
<!-- 2/2 -->
<!-- (pk + older) -->
<!-- X -->
<!-- scenario: user + co-signer, heir, lawyer -->

```
tr(X,{multi_a(2,A,B),and_v(v:pk(C),older(52560))})
```

#### Assets
- pk (internal): `X`
- pk: `A`, `B`, `C` 
- `older`: 52560 blocks

---
<!-- _class: slide_medium -->

## Concept check Qs

For each set of assets, what is the chosen spend path, and what is the resulting tapscript?

ref: <https://github.com/bitcoin/bips/blob/master/bip-0379.md>
ref: <https://bitcoin.sipa.be/miniscript>
ref: <https://github.com/rust-bitcoin/rust-miniscript/blob/master/examples/taproot.rs>

```
tr(X,{multi_a(2,A,B),and_v(v:pk(C),older(52560))})
```

* Keys: `[A, B]`
* ans: (multi_a) `<A> OP_CHECKSIG <B> OP_CHECKSIGADD OP_PUSHNUM_2 OP_NUMEQUAL`
* Key: `A`
* ans: (error) The assets are insufficient.
* Key: `C` + `older(52560)` blocks
* ans: (and_v) `<C> OP_CHECKSIGVERIFY <50cd00> OP_CHECKSEQUENCEVERIFY`
* bonus: Say we add all of the assets indiscriminately: `X` + `[A, B, C]` + `older(52560)`
* ans (key spend): `<X> OP_CHECKSIG`

<!-- Answers -->
<!-- 1. multi_a `<A> OP_CHECKSIG <B> OP_CHECKSIGADD OP_PUSHNUM_2 OP_NUMEQUAL` -->
<!-- 2. Err: The assets are insufficient -->
<!-- 3. csv: `<C> OP_CHECKSIGVERIFY <50cd00> OP_CHECKSEQUENCEVERIFY` -->
<!-- ans: Recall that the plan is supposed to find the minimum satisfaction, so in this case just the internal key is used -->
<!-- 4. `<X> OP_CHECKSIG` -->

---

## Discussion

<!-- Thank you for coming to my tech talk -->
