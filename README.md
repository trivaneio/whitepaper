

# **Trivane: A Unified Liquidity Aggregator for the Superchain**

### **I. Abstract**

The Optimism Superchain paradigm represents the future of horizontal scalability for Ethereum; however, this growth introduces a significant challenge known as liquidity fragmentation. With the emergence of each new OP Chain, the total liquidity is divided into smaller, less efficient pools, leading to higher slippage and worse trade execution for users. Trivane is a Superchain-native liquidity aggregation protocol built specifically to solve this problem. Trivane leverages the Superchain's native interoperability and messaging infrastructure, combining it with a powerful routing algorithm based on convex optimization. By aggregating liquidity across the entire Superchain ecosystem as a unified layer, Trivane offers users trades at a mathematically provable best price.

### **II. Introduction: The Superchain and the Fragmentation Dilemma**

#### **2.1. The Rise of a Horizontally Scaled Ecosystem**

The Optimism Superchain vision represents not just a collection of isolated Layer-2 (L2) solutions, but a network of interoperable OP Chains¹ (such as Base, Zora, Mode, etc.) that function as a single cohesive unit. <sup>[1](https://www.alchemy.com/overviews/optimism-superchain-op-stack-guide)</sup> The fundamental goal of this paradigm is to treat individual chains as "commoditized" and interchangeable computational resources, thereby abstracting the underlying chain from the user and developer experience. <sup>[2](https://docs.optimism.io/superchain/superchain-explainer)</sup> This approach is built upon shared security, governance, and a standardized, open-source technology stack known as the OP Stack², with the ultimate vision of creating a single, unified network. <sup>[1](https://www.alchemy.com/overviews/optimism-superchain-op-stack-guide)</sup>

#### **2.2. The Inevitable Challenge of Liquidity Fragmentation**

The growth of the Superchain through horizontal scalability spreads the ecosystem's total economic capacity—its liquidity—across numerous chains. With the launch of each new OP Chain, such as Base, Zora, and Mode, the total value locked (TVL) in the ecosystem is distributed among a multitude of decentralized exchanges (DEXs) on different chains. <sup>[6](https://integral.xyz/blog/what-is-optimism-superchain)</sup> This leads to shallow liquidity in any single market. <sup>[14](https://www.gate.com/learn/articles/in-the-layer-2-era-how-to-save-the-broken-liquidity/1318)</sup>

For users, the consequences of this fragmentation are severe: higher slippage, worse trade execution rates, and a complex user experience that requires manual bridging and swapping across multiple interfaces. <sup>[15](https://openexo.com/l/4fdfa392)</sup> For developers, it means the challenge of deploying and bootstrapping liquidity on multiple chains, which increases costs and operational overhead. <sup>[14](https://www.gate.com/learn/articles/in-the-layer-2-era-how-to-save-the-broken-liquidity/1318)</sup>

This situation can be seen as a form of "tax" on scalability. The core value of the Superchain is its ability to scale horizontally by adding more chains to increase transaction throughput. <sup>[4](https://dev.to/modenetworkl2/exploring-op-stack-and-superchain-the-future-of-ethereum-layer-2-2eg8)</sup> However, each new chain added to the network's computational capacity divides its economic capacity—its liquidity. <sup>[14](https://www.gate.com/learn/articles/in-the-layer-2-era-how-to-save-the-broken-liquidity/1318)</sup> This division creates inefficiencies (slippage) that act like an implicit tax on every transaction. Without a unifying mechanism, the more the network scales horizontally, the higher this tax becomes. Therefore, a solution like Trivane is not just a "nice-to-have" feature but a

*necessary* piece of infrastructure to unlock the full economic potential of the Superchain's architectural vision. It reunites the economic layer that the technical layer has fragmented.

#### **2.3. The Trivane Thesis: A Native Liquidity Aggregation Protocol**

Trivane is not merely a cross-chain aggregator but a foundational protocol built on top of the Superchain infrastructure to aggregate liquidity across the entire ecosystem. As a Superchain-native application, Trivane leverages the underlying network's trust-minimized messaging protocol and combines it with a powerful convex optimization framework that treats all OP Chains' liquidity as a single unified market. For users, Trivane offers the best possible trade price, guaranteed by mathematical optimization. For the Superchain ecosystem, Trivane transforms a collection of fragmented liquidity pools into a single, deep, and efficient aggregated market.

### **III. Mathematical Foundations of Optimal Routing**

At the heart of Trivane lies an algorithm that solves market inefficiencies not arbitrarily, but with mathematical certainty. The effectiveness of this algorithm stems from the fundamental geometry of Constant Function Market Makers (CFMMs) and the ability of this geometry to formulate and solve convex optimization problems.

#### **3.1. The Geometric Basis of Market Making**

Understanding CFMMs³ not simply through formulas like $x \cdot y = k$ but as fundamental geometric objects is key to Trivane's theoretical soundness. The core insight is that any CFMM, regardless of its specific formula, can be defined by fundamental geometric objects. <sup>[16](https://arxiv.org/abs/2312.14392)</sup>

---
³ **CFMM (Constant Function Market Maker)**: A class of automated market makers that use mathematical functions to determine asset prices and facilitate trades. Examples include Uniswap's constant product formula ($x \cdot y = k$) and Balancer's weighted geometric mean.

* **Trading Set ($T_i$):** For any market $i$, there exists a set $T_i$ containing all valid trades. This set is axiomatically defined as closed, convex, and downward-closed. <sup>[16](https://arxiv.org/abs/2312.14392)</sup> 
  
  **What this means in practice:** If you can trade 100 WETH for 200,000 USDC on Uniswap, and also trade 50 WETH for 100,500 USDC, then any linear combination of these trades is also valid. The convexity property is critical; it ensures that the marginal return does not improve as the size of a trade increases, which prevents obvious arbitrage and makes the optimization problem well-behaved. <sup>[16](https://arxiv.org/abs/2312.14392)</sup>
  
  **Mathematical interpretation:** For any two valid trades $\Delta_a$ and $\Delta_b$ in $T_i$, and any $\lambda \in [0,1]$, the trade $\lambda \Delta_a + (1-\lambda) \Delta_b$ is also in $T_i$. This reflects the realistic constraint that larger trades typically have worse exchange rates due to slippage.

* **Flow Cone ($K_i$):** We introduce the concept of the *flow cone* as the conic completion of the trading set. <sup>[16](https://arxiv.org/abs/2312.14392)</sup> This is a powerful abstraction that homogenizes the representation of different markets and simplifies the mathematical framework. The relevant work shows that any convex flow problem can be reduced to an equivalent conic flow problem. <sup>[17](https://arxiv.org/abs/2401.07954)</sup> This insight is critical for Trivane's extensibility.
  
  **Intuitive explanation:** Think of the flow cone as a way to represent all possible "directions" of trade in a market. If you can trade WETH for USDC, the flow cone includes not just that specific trade, but all scaled versions of it. This mathematical abstraction allows us to treat different types of markets (Uniswap, Balancer, Curve) with the same underlying framework.

#### **3.2. The Convex Flow Problem: A Formalism for Superchain Aggregation**

This section formally presents the optimization problem that Trivane solves. We will define the components based on the framework in <sup>[16](https://arxiv.org/abs/2312.14392)</sup> and <sup>[18](https://docs.optimism.io/interop/message-passing)</sup>:

**The Mathematical Model:**

* **Network:** A hypergraph where $n$ assets are the nodes and the $m$ DEXs across all OP Chains are the hyperedges.  
  
  **Concrete example:** Imagine we have 3 assets (WETH, USDC, OP) and 5 DEXs (Uniswap on Base, Velodrome on Optimism, etc.). Each DEX is a hyperedge that can connect multiple assets.

* **Variables:**  
  * $\Delta_i \in T_i$: The trade vector for each market $i$. Each $\Delta_i$ represents what we trade on market $i$.
  * $\Psi = \sum A_i\Delta_i$: The user's net trade vector across the entire Superchain.
  
  **What this means:** If $\Delta_1 = (-100 \text{ WETH}, +200,000 \text{ USDC})$ on Uniswap Base, and $\Delta_2 = (-50 \text{ WETH}, +15,000 \text{ OP})$ on Velodrome, then $\Psi = (-150 \text{ WETH}, +200,000 \text{ USDC}, +15,000 \text{ OP})$ is your total outcome.

* **Objective:** $\text{maximize } U(\Psi)$, where $U$ is the user's convex utility function.
  
  **Examples of utility functions:**
  - **Exact swap:** "I want to trade exactly 100 WETH for maximum USDC"
  - **Portfolio rebalancing:** "I want to end up with equal dollar values of WETH, USDC, and OP"
  - **Arbitrage:** "I want to find any profitable trade opportunity"

Formulating this problem as $\text{maximize } U(\Psi)$ under the constraint $\Psi \in \sum A_iT_i$ results in a convex optimization problem. This is because $U$ is convex, and the sum of convex sets ($T_i$) is also convex. This guarantees that a global optimum exists and can be found efficiently. <sup>[16](https://arxiv.org/abs/2312.14392)</sup>

The Superchain architecture is designed to allow a network of chains to act as a single entity. <sup>[2](https://docs.optimism.io/superchain/superchain-explainer)</sup> The Convex Flow Problem, in turn, models a network of markets as a single computational graph. Trivane bridges this gap. The

*physical reality* of the Superchain's interoperability layer provides the perfect substrate for the *mathematical abstraction* of the convex flow problem. Without the Superchain's native messaging system, executing the optimal solution $\Delta_i$ would require slow, insecure, and expensive third-party bridges, which would violate the assumption of a unified liquidity layer. Thus, Trivane not only implements a mathematically optimal algorithm; it leverages the Superchain's native infrastructure to create the ideal aggregation protocol for this specific ecosystem.

#### **3.3. The User's Objective: Defining Utility $U(\Psi)$**

The framework allows for various utility functions. Inspired by <sup>[16](https://arxiv.org/abs/2312.14392)</sup>, we will detail a few with concrete examples:

* **Basket Liquidation:** For a fixed input of $c$ units of asset $k$, $\text{maximize } \Psi_j$ under the constraints $\Psi_k = -c$ and $\Psi_l \ge 0$ for all other assets $l$.
  
  **Real-world example:** "I have exactly 100 WETH to sell, and I want to maximize the USDC I receive. I don't want to end up with any other tokens."
  - Constraint: $\Psi_{\text{WETH}} = -100$ (I give away exactly 100 WETH)
  - Objective: $\text{maximize } \Psi_{\text{USDC}}$ (maximize USDC received)
  - Constraint: $\Psi_{\text{OP}} \ge 0, \Psi_{\text{other}} \ge 0$ (don't acquire other tokens)

* **Arbitrage:** With $p$ as a reference price vector, $\text{maximize } p^T\Psi$ under the constraint $\Psi \ge 0$.
  
  **Real-world example:** "Find me any profitable trade based on Coinbase prices."
  - If Coinbase shows 1 WETH = $3,000 and 1 OP = $2, then $p = (3000, 1, 2)$ for (WETH, USDC, OP)
  - Objective: $\text{maximize } 3000 \cdot \Psi_{\text{WETH}} + 1 \cdot \Psi_{\text{USDC}} + 2 \cdot \Psi_{\text{OP}}$
  - Constraint: $\Psi \ge 0$ (only profitable trades, no initial investment required)

### **IV. Trivane Algorithm and Architecture**

Knowing that a theoretically optimal path exists is one thing; finding and executing it efficiently is another. As a Superchain-native aggregation protocol, Trivane solves these challenges through intelligent algorithm design and an architecture that fully leverages the underlying Superchain infrastructure's native capabilities.

#### **4.1. Solving the Unsolvable: Dual Decomposition**

**The Computational Challenge:**
Solving the primal problem directly is computationally infeasible for a large network as it involves a massive, unified constraint matrix. Imagine trying to optimize across 50+ DEXs simultaneously with thousands of trading pairs - the matrix would be enormous and computationally intractable.

**The Solution: Dual Decomposition⁴**
Instead, we use the dual decomposition method. This method breaks the global problem into smaller, independent parts. This is achieved by introducing a dual variable $\nu \in \mathbb{R}^n$, which can be interpreted as network-wide "exchange prices" or "no-arbitrage prices" for each asset. <sup>[16](https://arxiv.org/abs/2312.14392)</sup>

---
⁴ **Dual Decomposition**: A mathematical technique that transforms a complex optimization problem into smaller, more manageable subproblems that can be solved independently and in parallel, greatly improving computational efficiency.

**How It Works:**
Think of $\nu$ as a "shadow price" vector where $\nu_{\text{WETH}}$ represents the fair value of WETH, $\nu_{\text{USDC}}$ the fair value of USDC, etc. These prices act as a coordination mechanism between all markets.

The dual function is defined as: 
$$g(\nu) = \bar{U}(\nu) + \sum_{i=1}^m \text{arb}_i(A_i^T\nu)$$

where:
- $\bar{U}(\nu)$ is the conjugate of the utility function (represents the user's willingness to pay at prices $\nu$)
- $\text{arb}_i(A_i^T\nu)$ is the maximum profit achievable on market $i$ given prices $\nu$

The algorithm's goal is to: $\text{minimize } g(\nu)$

**Intuition:** We're finding the price vector $\nu$ that minimizes the total "regret" - the sum of what the user pays and what markets can extract as arbitrage profit.

#### **4.2. The Core Computational Step: The Arbitrage Subproblem**

For a given price vector $\nu$, the arbitrage subproblem for each market $i$ is to find the most profitable trade: 
$$\text{maximize } (A_i^T\nu)^T\Delta_i \text{ subject to } \Delta_i \in T_i$$ <sup>[16](https://arxiv.org/abs/2312.14392)</sup>

**What this means in practice:**
Suppose we have shadow prices $\nu = (3000, 1, 2.1)$ for (WETH, USDC, OP), and we're looking at a Uniswap WETH/USDC pool on Base. We ask: "Given these shadow prices, what's the most profitable trade we can make on this specific pool?"

If the pool offers a trade $\Delta_i = (-1 \text{ WETH}, +2990 \text{ USDC})$, the profit is:
$$(3000 \times (-1)) + (1 \times 2990) = -3000 + 2990 = -10$$

This is unprofitable, so we wouldn't use this trade. But if another market offers $\Delta_j = (-1 \text{ WETH}, +3010 \text{ USDC})$, the profit would be $+10$, making it attractive.

**Why this is efficient:**
This is the key to Trivane's performance. Since each $\text{arb}_i$ depends only on $\nu$ and the state of market $i$, all $m$ subproblems can be solved in parallel by the off-chain engine. <sup>[16](https://arxiv.org/abs/2312.14392)</sup> For most common CFMMs (Uniswap, Balancer, etc.), this subproblem has a closed-form solution or can be solved with extremely fast numerical methods like one-dimensional root-finding. <sup>[16](https://arxiv.org/abs/2312.14392)</sup> This makes the solver's core loop highly efficient.

**Parallel Processing:** Imagine having 50 DEXs across 5 chains. Instead of solving one massive problem, we solve 50 small problems simultaneously, each asking "what's the best trade on this specific DEX given these shadow prices?"

#### **4.3. Trivane Architecture: A Superchain-Native Aggregation System**

As a protocol built specifically for the Superchain ecosystem, Trivane maximizes efficiency and security by separating computation and execution tasks while leveraging the Superchain's native infrastructure.

* **Off-Chain Solver:**  
  **Step 1: State Ingestion**
  Trivane's off-chain nodes continuously monitor the state (reserves, parameters, etc.) of all relevant DEXs on the Superchain.
  
  *Example:* The system tracks that Uniswap on Base has 1,000 WETH and 3,000,000 USDC, Velodrome on Optimism has 500 WETH and 15,000 OP tokens, etc.  
  **Step 2: Problem Formulation**
  A user request (e.g., "swap 100 WETH for USDC") defines the utility function $U(\Psi)$. The solver constructs the convex flow problem.
  
  *Example:* User wants to swap exactly 100 WETH for maximum USDC. This creates the constraint $\Psi_{\text{WETH}} = -100$ and objective $\text{maximize } \Psi_{\text{USDC}}$.  
  **Step 3: Iterative Solution**
  The solver uses an iterative method like L-BFGS-B to find the optimal price vector $\nu^*$. 
  
  *The algorithm works as follows:*
  - Start with initial price guess: $\nu^{(0)} = (3000, 1, 2)$ for (WETH, USDC, OP)
  - For each iteration $k$:
    - Solve all arbitrage subproblems in parallel to compute $g(\nu^{(k)})$ and its gradient
    - Update: $\nu^{(k+1)} = \nu^{(k)} - \alpha \nabla g(\nu^{(k)})$ (gradient descent step)
    - Repeat until convergence
  
  *Convergence criteria:* Stop when $|\nu^{(k+1)} - \nu^{(k)}| < \epsilon$ for small $\epsilon$.  
  4. **Optimal Plan:** The final $\nu^*$ yields the set of optimal individual trades $\{\Delta_i^*\}.  
* **On-Chain Execution with Native Messaging:**  
  1. **Transaction Encoding:** The solver encodes the individual trades $\Delta_i^*$ into a series of cross-chain messages.  
  2. **Message Dispatch:** Trivane's smart contract on the user's source chain initiates the process by calling the L2ToL2CrossDomainMessenger.sendMessage function. <sup>[18](https://docs.optimism.io/interop/message-passing)</sup>  
  3. **Cross-Chain Execution:** The Superchain's infrastructure (e.g., OP Supervisor) ensures these messages are relayed and executed on the respective destination chains, triggering the swaps on the target DEXs. <sup>[20](https://docs.optimism.io/stack/interop/explainer)</sup>  
  4. **Settlement:** The final assets are routed to the user's address on their desired destination chain.

**Native Superchain Advantages:**

One of the biggest risks in cross-chain operations is transaction failure or double-spending due to reorgs. The Superchain's interoperability protocol is designed to be "reorg-aware". <sup>[22](https://docs.optimism.io/stack/interop/compatible-tokens)</sup> If a source chain reorgs, the corresponding execution message on the destination chain is invalidated by the protocol. This means a trade via Trivane is atomic at the protocol level: either all legs of the trade execute correctly, or the invalid legs fail safely without loss of funds (excluding gas costs).

This is a profound security advantage over cross-chain aggregators that rely on external relayers (like 1inch Fusion+) or third-party bridges, where reorgs can lead to complex failure modes, stuck transactions, or relayer trust issues. Trivane inherits the atomicity guarantees of the underlying Superchain infrastructure, eliminating the need for external dependencies while achieving **1-block latency** for cross-chain execution.

Unlike external relayer-based systems that can take minutes to complete cross-chain trades and introduce additional trust assumptions, Trivane's native integration provides immediate finality with the same security guarantees as the underlying Superchain networks.

### **V. Trivane in the Ecosystem: A Comparative Analysis**

#### **5.1. Comparative Landscape**

The following table provides a clear summary of Trivane's advantages over existing solutions, synthesizing insights on trust models, latency, and capital efficiency. While protocols like 1inch Fusion+ offer cross-chain aggregation, they rely on external relayers and bridge infrastructure, introducing additional trust assumptions and latency. Trivane's Superchain-native approach eliminates these dependencies by leveraging the ecosystem's built-in interoperability.

| Feature | Trivane (Superchain-Native Aggregator) | Cross-Chain Aggregator (e.g., 1inch Fusion+) | Third-Party Bridge (e.g., Across) |
| :---- | :---- | :---- | :---- |
| **Liquidity Scope** | Aggregates across entire Superchain ecosystem | Multiple chains but limited by relayer network | Variable, but pools are separate |
| **Cross-Chain Mechanism** | Native Superchain OP Stack messaging (1-block latency) | External relayers + bridge infrastructure | Own protocol infrastructure |
| **Trust Model** | Leverages Superchain's shared security model | L2 security + Relayer trust assumptions | Relies on its own validator/relayer set |
| **Latency** | **Low**: 1-block native messaging <sup>[22](https://docs.optimism.io/stack/interop/compatible-tokens)</sup> | **Medium**: Relayer-dependent (minutes) | **High**: Often depends on L1 finality |
| **Capital Efficiency** | **Very High**: Aggregates all Superchain liquidity | **Medium**: Limited by relayer liquidity | **Low**: Liquidity is fragmented per bridge |
| **Optimization Method** | **Globally Optimal**: Convex Flow Problem | **Locally Optimal**: Per-chain routing + relayer matching | **Path-Dependent**: Often point-to-point |
| **Extensibility** | High: Any convex market can be added <sup>[16](https://arxiv.org/abs/2312.14392)</sup> | Limited to supported chains with relayers | Requires custom integration |
| **Infrastructure Dependency** | **Native**: Built on Superchain infrastructure | **External**: Requires third-party relayers | **External**: Requires bridge operators |

### **VI. Challenges, Future Work, and Conclusion**

#### **6.1. Addressing Implementation Challenges**

* **Sequencer Centralization and Latency:** While the protocol is decentralized, current OP Chains often use a single sequencer. Trivane's performance will depend on the latency and reliability of these sequencers.  
* **Cross-Chain MEV (Maximal Extractable Value):** Aggregating trades across chains creates new opportunities for MEV. We will discuss potential MEV scenarios, such as front-running one leg of a trade, and possible mitigation strategies.  
* **Protocol Evolution:** The Superchain is still under active development. <sup>[2]</sup> Trivane must be designed to be adaptable and modular to accommodate future upgrades to the OP Stack and interoperability protocols.

#### **6.2. Roadmap and Future Developments**

* **Fixed Costs and Non-Convexities:** The provided research <sup>[17](https://arxiv.org/abs/2401.07954)</sup> examines the NP-hard fixed-cost routing problem. To handle gas costs (which act like a fixed fee for using an edge/chain), we will propose incorporating a heuristic or a convex relaxation from the same research. <sup>[17](https://arxiv.org/abs/2401.07954)</sup> The Shapley-Folkman lemma <sup>[17](https://arxiv.org/abs/2401.07954)</sup> shows that this relaxation is very tight in practice.  
* **Asset Universe Expansion:** Integration with token standards like SuperchainERC20 <sup>[22](https://docs.optimism.io/stack/interop/compatible-tokens)</sup> will be crucial for seamless and capital-efficient asset bridging, complementing Trivane's message-based trade execution.

#### **6.3. Conclusion: The Optimal Liquidity Aggregator for the Superchain**

Trivane represents more than a conventional DEX aggregator; it is a purpose-built liquidity aggregation protocol that unlocks the Superchain's foundational promise of unified, scalable blockchain infrastructure. As the Superchain continues to expand horizontally through new OP Chain deployments, the challenge of liquidity fragmentation will only intensify. Without a native aggregation solution, each new chain added to the ecosystem effectively taxes the entire network through increased slippage and reduced capital efficiency.

Our contribution addresses this challenge at its mathematical core. By formulating cross-chain liquidity aggregation as a convex optimization problem, Trivane guarantees mathematically optimal trade execution across all OP Chains within the Superchain ecosystem. This is not an approximation or heuristic—it is provably the best possible outcome for users given the current state of all available markets.

The architectural design of Trivane is built specifically to leverage the Superchain's native interoperability infrastructure, eliminating the trust assumptions and latency penalties associated with third-party bridge solutions. Through the dual decomposition algorithm, the aggregation protocol scales efficiently as new chains and markets are added to the Superchain, maintaining optimal performance regardless of ecosystem size.

Perhaps most importantly, Trivane transforms the economic topology of the Superchain from a collection of isolated liquidity islands into a single, unified aggregated market. This fundamental shift unlocks the true economic potential of the Superchain's horizontal scaling approach, where each new chain added to the network enhances rather than fragments the available liquidity.

In essence, Trivane solves the fundamental tension inherent in the Superchain's design: how to scale computational capacity horizontally while maintaining—and indeed enhancing—economic efficiency. As the Superchain evolves toward its vision of commoditized, interchangeable chains, Trivane provides the essential aggregation layer that makes this abstraction not just technically feasible, but economically optimal.

The protocol establishes a new paradigm where liquidity fragmentation becomes liquidity aggregation, where scaling challenges become scaling opportunities, and where the sum of the Superchain ecosystem becomes demonstrably greater than its parts. In doing so, Trivane does not merely support the Superchain—it optimizes it.

### **References**

1. Alchemy. (2025). *What is the Superchain? A Deep Dive Into OP Stack Rollups*. Accessed June 17, 2025, from [https://www.alchemy.com/overviews/optimism-superchain-op-stack-guide](https://www.alchemy.com/overviews/optimism-superchain-op-stack-guide)
2. Optimism Docs. (2025). *Superchain explainer*. Accessed June 17, 2025, from [https://docs.optimism.io/superchain/superchain-explainer](https://docs.optimism.io/superchain/superchain-explainer)
3. Unchained Crypto. (2025). *What Is Optimism Superchain And Why Is it Popular for Projects?*. Accessed June 17, 2025, from [https://unchainedcrypto.com/optimism-superchain/](https://unchainedcrypto.com/optimism-superchain/)
4. Mode Network. (2025). *Exploring OP Stack and Superchain: The Future of Ethereum Layer 2*. DEV Community. Accessed June 17, 2025, from [https://dev.to/modenetworkl2/exploring-op-stack-and-superchain-the-future-of-ethereum-layer-2-2eg8](https://dev.to/modenetworkl2/exploring-op-stack-and-superchain-the-future-of-ethereum-layer-2-2eg8)
5. Optimism Docs. (2025). *Getting started with the OP Stack*. Accessed June 17, 2025, from [https://docs.optimism.io/stack/getting-started](https://docs.optimism.io/stack/getting-started)  
6. Integral. (2025). *What is Optimism Superchain?*. Accessed June 17, 2025, from [https://integral.xyz/blog/what-is-optimism-superchain](https://integral.xyz/blog/what-is-optimism-superchain)  
7. CryptoRank. (2025). *What are OP Stack and Superchain?*. Accessed June 17, 2025, from [https://cryptorank.io/insights/analytics/what-is-op-stack](https://cryptorank.io/insights/analytics/what-is-op-stack)  
8. Conduit. (2025). *Guide to Optimism Superchain*. Accessed June 17, 2025, from [https://www.conduit.xyz/blog/optimism-superchain/](https://www.conduit.xyz/blog/optimism-superchain/)  
9. CoinGecko. (2025). *Optimism DEXs: Top Optimism Decentralized Exchanges by Volume*. Accessed June 17, 2025, from [https://www.coingecko.com/en/exchanges/decentralized/optimistic-ethereum](https://www.coingecko.com/en/exchanges/decentralized/optimistic-ethereum)  
10. QuickNode. (2025). *Top 10 Decentralized Exchanges (DEXs) on Base*. Accessed June 17, 2025, from [https://www.quicknode.com/builders-guide/top-10-decentralized-exchanges-on-base](https://www.quicknode.com/builders-guide/top-10-decentralized-exchanges-on-base)  
11. Alchemy. (2025). *List of 28 Decentralized Exchanges (DEXs) on OP Mainnet*. Accessed June 17, 2025, from [https://www.alchemy.com/dapps/list-of/decentralized-exchanges-dexs-on-op-mainnet](https://www.alchemy.com/dapps/list-of/decentralized-exchanges-dexs-on-op-mainnet)  
12. Alchemy. (2025). *List of 21 Decentralized Exchanges (DEXs) on Base (2025)*. Accessed June 17, 2025, from [https://www.alchemy.com/dapps/list-of/decentralized-exchanges-dexs-on-base](https://www.alchemy.com/dapps/list-of/decentralized-exchanges-dexs-on-base)  
13. Slashdot. (2025). *Best Decentralized Exchanges (DEX) for Base*. Accessed June 17, 2025, from [https://slashdot.org/software/decentralized-exchanges-dex/for-base/](https://slashdot.org/software/decentralized-exchanges-dex/for-base/)  
14. Gate.com. (2025). *In the Layer 2 era, how to save the broken liquidity?*. Accessed June 17, 2025, from [https://www.gate.com/learn/articles/in-the-layer-2-era-how-to-save-the-broken-liquidity/1318](https://www.gate.com/learn/articles/in-the-layer-2-era-how-to-save-the-broken-liquidity/1318)  
15. Let's Talk, Bitcoin. (2025). *Ethereum L2 Fragmentation: Interoperability Solutions Gain Traction*. Accessed June 17, 2025, from [https://openexo.com/l/4fdfa392](https://openexo.com/l/4fdfa392)  
16. Diamandis, T., Angeris, G., & Edelman, A. (2024). *Convex Network Flows*. arXiv preprint arXiv:2312.14392. Available at: [https://arxiv.org/abs/2312.14392](https://arxiv.org/abs/2312.14392)  
17. Diamandis, T., & Angeris, G. (2024). *Solving the Convex Flow Problem*. arXiv preprint arXiv:2401.07954. Available at: [https://arxiv.org/abs/2401.07954](https://arxiv.org/abs/2401.07954)  
18. Optimism Docs. (2025). *Interop message passing overview*. Accessed June 17, 2025, from [https://docs.optimism.io/interop/message-passing](https://docs.optimism.io/interop/message-passing)  
19. Optimism Docs. (2025). *Interop message passing tutorial*. Accessed June 17, 2025, from [https://docs.optimism.io/interop/tutorials/message-passing](https://docs.optimism.io/interop/tutorials/message-passing)  
20. Optimism Docs. (2025). *Superchain interoperability explainer*. Accessed June 17, 2025, from [https://docs.optimism.io/stack/interop/explainer](https://docs.optimism.io/stack/interop/explainer)  
21. Mint. (2025). *Superchain Interoperability: From Fragmented Cross-Chain Liquidity to a Unified Native Liquidity Layer Future*. DEV Community. Accessed June 17, 2025, from [https://dev.to/mint\_/superchain-interoperability-from-fragmented-cross-chain-liquidity-to-a-unified-native-liquidity-1pfg](https://dev.to/mint_/superchain-interoperability-from-fragmented-cross-chain-liquidity-to-a-unified-native-liquidity-1pfg)  
22. Optimism Docs. (2025). *Superchain interop compatible tokens*. Accessed June 17, 2025, from [https://docs.optimism.io/stack/interop/compatible-tokens](https://docs.optimism.io/stack/interop/compatible-tokens)  
23. Optimism Specs. (2025). *Interoperability \- OP Stack Specification*. Accessed June 17, 2025, from [https://specs.optimism.io/interop/overview.html](https://specs.optimism.io/interop/overview.html)  
24. GitHub. (2025). *Interoperability: Catch-all Discussion \+ Q\&A · ethereum-optimism specs*. Accessed June 17, 2025, from [https://github.com/ethereum-optimism/specs/discussions/128](https://github.com/ethereum-optimism/specs/discussions/128)  
25. Optimism Docs. (2025). *Chain architecture*. Accessed June 17, 2025, from [https://docs.optimism.io/operators/chain-operators/architecture](https://docs.optimism.io/operators/chain-operators/architecture)
