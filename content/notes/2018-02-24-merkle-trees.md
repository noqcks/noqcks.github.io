---
title: "Merkle Trees: The Backbone of Distributed Software"
date: '2018-02-24'
tags:
  - markle trees
  - crypto
  - decentralization
slug: merkle-trees-the-backbone-of-distributed-software
---


I know it sounds complicated, but bear with me, it’s worth it.

A Merkle Tree is a data structure useful in distributed scenarios because it allows one to securely verify data structures without having to transfer the entire data structure.

# EXAMPLES

Thes use cases have been exploding in recent years:

- Distributed Version Control (Git/Mercurial)
- BitTorrent
- Copy-On-Write Filesystems (btrfs/[ZFS](https://blogs.oracle.com/bonwick/zfs-end-to-end-data-integrity))
- Distributed NoSQL Databases (Cassandra, Riak, Dynamo)
- The distributed web! ([IPFS](https://taravancil.com/blog/how-merkle-trees-enable-decentralized-web/))
- Cryptos (Bitcoin, [Ethereum](https://blog.ethereum.org/2015/11/15/merkling-in-ethereum/))

These all use Merkle trees to ensure data consistency.

# MERKLE STRUCTURE
The structure of a Merkle Tree  a tree in which:

 1. every leaf node is labelled with the hash of a data block
 2. every non-leaf node is labelled with the cryptographic hash of the labels of its child nodes¹

The parts of the Merkle tree are the branches (lines between nodes), the nodes, and the root (the very top node).

<p><img src="https://www.noqcks.io/img/merkle-tree-structure.png" alt="Screenshot showing merkle tree"></p>

The hashes 0-0 and 0-1 are the hash values of the data blocks (data chunks) L1 and L2, respectively. The hash 0 is a concatenation of the hashes 0-0 and 0-1 and so on. By walking this tree, one can verify that a data chunk exists!

I still don’t get it, why are they used precisely?

# WHY USE MERKLE TREES?

There must be good reasons why the use of Merkle Trees have exploded in recent years. Let’s list them now!

1. They allow you to verify a data structure securely! ZFS uses this to ensure that the data stored is consistent with earlier versions of data.
2. They significantly reduce the size of data you need to prove the consistency of data. Bitcoin “light clients” only need to download block headers for each block in its blockchain instead of the entire block to verify its integrity. This is the main reason why Merkle Trees are used in distributed applications.
3. It separates the verification of the data from the data itself. Cassandra DB’s AntiEntropy service uses Merkle Trees to detect inconsistencies in data between replicas. They do this because there is no need to send the data itself across replicas for verification, only hash data.


# WHAT KIND OF PROOFS ARE THERE?


There are two Merkle Proofs that allow you to verify Merkle data in different ways.

- **Audit Proof**: This kind of proof lets you verify that a single chunk exists in the tree.
- **Consistency Proof**: This kind of proof is only applicable to append-only Merkle trees.  The consistency proof lets you verify that earlier versions of the tree are consistent with newer versions of the tree (that they’re in the same order, and that all new records come after all old records).

For more information about the specific implementation of these proofs, you can reference [this blog post](https://www.codeproject.com/Articles/1176140/Understanding-Merkle-Trees-Why-use-them-who-uses-t#WhoUsesMerkleTrees2).

Hopefully, you now understand a little better the uses cases for Merkle Trees in distributed applications. Enjoy!

¹ https://en.wikipedia.org/wiki/Merkle_tree

