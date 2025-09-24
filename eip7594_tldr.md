# EIP-7594 PeerDAS - Peer Data Availability Sampling

## TL;DR

A proposal to make Ethereum’s new blob data (from EIP-4844) more scalable.

- Instead of every node downloading all blob data, nodes only **download and check small random samples**
- This is called **Peer Data Availability Sampling (PeerDAS)**
- The math (erasure coding + cryptographic proofs) ensures that if your samples look good, the whole blob is almost certainly available

## Requirements

EIP-4844

## Purpose and Motivation

- **Blobs are big:**
  - EIP-4844 already reduced rollup costs by introducing blobs
  - But every node still had to fetch full blobs, which is heavy if blob usage explodes
- **Scalability:**
  - By letting nodes sample instead of downloading everything, Ethereum can safely allow way more blobs per block.
- **Maintain decentralization:**
  - Nodes with average bandwidth/storage can still run Ethereum, even if blob throughput scales up massively

## Expected Changes

- **Blobs split into "cells":**
  - Each blob is chopped into many small pieces, then extended with erasure coding (so missing pieces can be reconstructed)
- **Sampling instead of full download:**
  - Nodes fetch a few random cells from different peers
- **Cell proofs with KZG commitments:**
  - Nodes verify cryptographic proofs that each sampled cell really belongs to the committed blob
- **Peer custody / subnets:**
  - Different peers are responsible for holding different columns (slices) of blob data
  - Nodes advertise which columns they store so others can sample from them

## Security Benefits

- **Data withholding detection:**
  - If a block producer tries to commit to a blob without publishing it, sampling will almost certainly catch them
- **Probalistic guarantee:**
  - Even though each node only sees a fraction, the network as a whole ensures the data is available with overwhelming probability
- **Future-proof scaling:**
  - Paves the way for full Danksharding, where blob counts per block increase drastically

## Risks & Trade-offs

- **Withholding attacks:**
  - If parameters (number of samples, peer diversity) are too weak, an attacker might slip through
- **Peer connectivity:**
  - Nodes need enough diverse peers; if connections are poor, samples may not cover enough of the data
- **Extra complexity:**
  - Networking: subnets, custody assignments, advertising who has what
  - Cryptography: erasure coding + cell proofs add verification overhead
- **Small chance of undetected attack:**
  - Unlike 4844’s full download guarantee, PeerDAS is probabilistic. Security relies on math + parameters

## How it builds on EIP-4844

- **4844:** Introduced blobs (cheap, temporary storage for rollups) but required all nodes to download full blob data
- **7594:** Lets nodes only sample parts of blobs → reduces bandwidth/storage cost and allows Ethereum to raise the number of blobs per block

## Summary

- **EIP-4844** made rollup data cheaper
- **EIP-7594** makes blob handling scalable, instead of every node carrying the full weight, nodes check only small parts but still keep Ethereum safe
