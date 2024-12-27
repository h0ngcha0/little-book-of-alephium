# Alephium Basics

Before diving deeper into Alephium’s technical innovations and features, it’s essential to understand the fundamental building blocks of the Alephium blockchain. This section provides a foundational understanding of the components that power Alephium, making it approachable for both newcomers and developers.

## The Stateful UTXO Model

Alephium is built on the stateful UTXO (Unspent Transaction Output) model, which is a hybrid of Bitcoin’s UTXO structure and Ethereum’s account-based model. Here’s how it works:

- UTXO: Each transaction creates outputs that can be used as inputs for future transactions. This ensures transparency and security.

- Stateful Enhancements: Unlike Bitcoin’s static UTXO model, Alephium’s UTXOs can store programmable states, enabling advanced smart contract functionality.

- Advantages:

  - Improved scalability by minimizing global state dependencies.

  - Enhanced security through clear input-output mapping.

  - Flexibility for complex decentralized applications.

## Sharding and Scalability

Sharding is a core feature of Alephium’s design, enabling the network to scale effectively. It divides the blockchain into smaller, manageable pieces called shards.

- Parallel Processing: Transactions within a shard are processed independently, allowing for higher throughput.

- Dynamic Load Distribution: Alephium’s system ensures balanced workloads across shards to prevent bottlenecks.

- Cross-Shard Communication: A seamless protocol ensures transactions between shards are secure and efficient.

This design achieves scalability without compromising decentralization or security, making Alephium capable of handling thousands of transactions per second.

## Alephium’s Native Token (ALPH)

The native token of the Alephium blockchain, ALPH, plays a vital role in maintaining and operating the network:

- Utility:

  - Transaction fees.

  - Rewarding miners for validating transactions.

- Tokenomics:

  - Fixed supply to ensure scarcity and value retention.

  - Gradual release through mining rewards to incentivize early network participation.

## Proof of Less Work

Alephium introduces an innovative consensus mechanism called Proof of Less Work (PoLW). This mechanism:

- Reduces Energy Consumption: Optimizes traditional Proof of Work to be more energy-efficient while maintaining security.

- Fair Mining: Lowers entry barriers for miners, encouraging broader participation.

- Sustainability: Aligns Alephium with environmentally conscious blockchain goals.

## Transactions in Alephium

Transactions in Alephium follow the stateful UTXO model, enabling advanced features while ensuring simplicity:

- Basic Structure:

  - Inputs: References to existing UTXOs.

  - Outputs: New UTXOs created by the transaction.

- Smart Transaction Capability: Transactions can trigger smart contract execution stored within UTXOs.

- Low Fees: Sharding and scalability reduce congestion, keeping transaction costs minimal.

## Security and Decentralization

Alephium prioritizes security and decentralization in its design:

- Decentralized Validation: Shards ensure that no single node processes the entire blockchain, reducing centralization risks.

- Consensus Integrity: The BlockFlow consensus ensures transaction validity across shards while resisting malicious actors.

- Resilience: Alephium’s architecture is designed to withstand attacks and ensure consistent performance.

## Why Alephium Basics Matter

Understanding these foundational concepts is crucial for grasping Alephium’s value proposition. Whether you’re sending a simple transaction, developing a decentralized application, or exploring mining, these basics form the bedrock of how Alephium operates.

By mastering these core ideas, you’re well-equipped to delve into more advanced topics like smart contracts, networking, and the ecosystem in subsequent chapters.
