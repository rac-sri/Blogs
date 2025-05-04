---
title: "Kademlia DHT: A P2P Information System"
seoTitle: "Kademlia P2P"
seoDescription: "Kademlia DHT: A P2P Information System"
datePublished: Sun May 04 2025 15:04:06 GMT+0000 (Coordinated Universal Time)
cuid: cma9s8bsu000609jw8qft03cv
slug: kademlia-dht-a-p2p-information-system
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1746370994359/d6f714f8-2fee-4302-9f16-6ae7a188b532.png
tags: distributed-system, blockchain, p2p, ethereum, distributed-hash-tables, avail-light-client, dht, kademlia

---

A **P2P (peer-to-peer) network** is like a group of friends where **everyone shares and helps each other directly** — there’s no boss or central person in charge.

Instead of going to a central server (like a library or a big store), **you go directly to another person** (a "peer") to get what you need — like a file, song, or book. And just like you can download from someone, they can also download from you.

So in a P2P network:

* Everyone is both a **giver** and a **receiver**.
    
* There’s **no central control** — the system is spread out and more democratic.
    
* If one peer goes offline, the rest can still keep sharing.
    

**Real-World Example:**

Imagine you and your friends all bring different books to a hangout. Instead of having one person collect and lend all the books (like a library), you all **share books with each other directly**. If one friend leaves, the rest can still trade books.

That’s how P2P file sharing works — with files instead of books.

---

### **Distributed Hash Table (DHT)**

A **Distributed Hash Table (DHT)** is like a **giant, shared address book** spread across a large group of computers (or peers), where **everyone helps store and find information** — but no one person has the whole book.

One data structure that allows storing elements as **&lt;key, value&gt;** pairs is a traditional map. We store separate parts of the map on separate computers. We could connect these parts by designating a centralized computer, or coordinator, which would coordinate which keys are stored on which computer. This map would not be very resilient, however, to when the coordinator goes offline.

A Distributed Hash Table (DHT) is a kind of data structure stored on multiple computers that aims to address this issue. We still want our basic operations **PUT(key, value)** and **GET(key)**, like we had in our map, but we want our data structure to still stand even if some computer leaves our network. In designing our DHT, we want it to be:

* *Scalable*. When more computers participate, we want the DHT to split up the elements evenly among the computers.
    
* *Fault-Tolerant*. If a computer leaves the network, we want to be able to access the elements that it previously held.
    

---

### Simple Analogy:

Imagine you and your friends are part of a huge **book exchange club**. Instead of using one central list to track where each book is, each friend **remembers a small part** of the list — like a puzzle piece.

Now, suppose you want a specific book. You don’t know who has it, but you **know its title (a key)**. You ask around, and based on how the list is organized, your request is **routed through the network**, jumping from friend to friend, until it reaches the person who knows where that book is stored — or who has it themselves.

**How It Works Technically (Simplified):**

* Data is stored as **(key, value)** pairs. For example:  
    `key = "`[`MySong.mp`](http://MySong.mp)`3"`  
    `value = "Stored at peer X"`
    
* A **hash function** turns the key into a number (a hash), which tells the system **which peer should store or look up that key**.
    
* Peers **cooperate to route requests**, usually in just a few steps, even if millions of peers exist.
    

**Real-World Uses:**

* **BitTorrent**: uses DHT to find which peers have pieces of a file.
    
* **IPFS (InterPlanetary File System)**: uses DHT to locate files in a decentralized way.
    
* **Blockchain networks**: sometimes use DHT-like structures to store or sync data.
    

---

## Kademlia Protocol

Kademlia operates over a 160 bit node ID, and uses **XOR metric** as a means to define distance between points in the key space o minimize internode messaging.

This, if the distance is expressed as log2(n) nodes, this means that for a network with 10,000,000 Kademlia nodes, only about 20hops would be necessary at most for communication with any subset of nodes.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746368924553/9e5fdc58-5a59-4d1e-8a83-aff965cf1244.png align="center")

## XOR Metric

Kademlia has a 160 bi node ID. Kademlia defined distance between two identifiers using XOR i.e. \\(d(x,y_) = x \oplus y  \\) . XOR is a valid, albeit non-Euclidean metric.

* \\(d(x,x) = 0\\)
    
* \\(d(x,y) > 0 \\) if \\(x  \ne y\\)
    
* \\(\forall x,y: d(x,y) = d(y,x)\\)
    
* \\(d(x,y) + d(y,z) \ge d(x,z)\\)
    

XOR is unidirectional. For any given point x and distance \\(\delta\\)\&gt;0, there is exactly one point y such that \\(d(x,y) = \delta\\). Unidirectionality ensures that all lookups for the same key converge along the same path, regardless of the originating node.

## Search Tree Partitions

All ids and keys are in a keyspace of \\([0,1,…2^3−1][0,1,…2^3−1]\\) and are represented in binary. We can represent this space as a complete binary tree where each leaf node is a key. Circled leaves are ids that correspond to a participating computer in the network.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746369638877/85f094d2-4e61-456f-b1ce-1eae902e10b6.png align="center")

When distributing keys among computers in a peer-to-peer system, tries (prefix trees) offer a natural way to represent and navigate binary identifiers. Each path from the root to a leaf reflects the binary representation of a key, and shared prefixes indicate shared ancestry between keys — a useful property for routing and assignment.

However, assigning a key to the “closest” computer isn’t always straightforward. For example, two computer IDs might share the same longest common prefix with a given key, leading to a tie. One naive solution is to break the tie arbitrarily, but this can lead to imbalance.

A more elegant and deterministic approach is to compare the **most significant bit** (MSB) where the computer IDs differ. The key is then assigned to the computer whose bit at that position **matches the key’s bit**. This ensures a consistent and fair assignment, even in the case of ties, by leveraging bit-level structure instead of arbitrary decisions.

This method generalizes the idea of the longest common prefix and leads to more balanced and predictable routing in distributed systems.

## Routing Table

A **routing table** in Kademlia is a structure that helps each node **find other nodes efficiently**, especially the ones **closer (in XOR distance)** to a target ID. It is:

* Organised by distance, using XOR distance.
    
* **k-buckets:** The routing table is divided into buckets, one for each possible bit length. Each k-bucket holds up to k node IDs ( usually k = 20 ) that fall within a certain XOR distance range. Bucket i holds nodes whose distance is between \\(2^i\\) and \\(2^{i+1}\\).
    
* Uses LRU cache and gives preference to long lived, stabled nodes. When a bucket is full, the oldest node is pined. If it’s alive, it says, else replaced.
    
* When a node wants to find a key, it queries nodes closer and closer using the routing table to guide it. Lookup time typically completes in \\(O(logn)\\) steps.
    
* Another benefit of k-buckets is that they provide resistance to certain DoS attacks. One cannot flush nodes routing state by flooding the system with new nodes. Kademlia nodes will only insert the new nodes in the k-buckets when old nodes leaves the system.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746370350123/ca1c80d4-926b-4a62-a6a6-498050f22ba8.png align="center")
    

## Kademlia Protocol

The Kademlia protocol consists of four RPCs: **PING, STORE, FIND\_NODE, FIND\_VALUE.**

* **PING**: The `PING` RPC is the most basic yet essential building block of Kademlia's routing logic. It serves a simple purpose: to check if a node in the network is still alive. When a node wants to confirm the availability of a peer (often one stored in its routing table), it sends a `PING` message. If the node is active, it replies with a `PONG`. This mechanism is crucial for maintaining the accuracy and health of the routing table, especially in dynamic environments where peers may go offline without warning. Stale or unresponsive peers can then be replaced, ensuring efficient and reliable lookups.
    
* **STORE**: The `STORE` RPC allows Kademlia nodes to insert data into the distributed hash table. When a node wants to store a `(key, value)` pair, it identifies the kkk nodes whose IDs are closest to the key (based on XOR distance) and sends each of them a `STORE` message. These recipient nodes then persist the data. This operation is what enables decentralized data storage in systems like BitTorrent or IPFS, where information is scattered across many peers but still retrievable by key. Although the `STORE` RPC doesn’t require a complex response, it forms the basis of reliable and distributed value preservation.
    
* **FIND\_NODE**: The `FIND_NODE` RPC is used to locate nodes in the network that are closest to a particular target ID. When a node wants to route a query or build its routing table, it sends a `FIND_NODE` message with the target ID to a known peer. In response, that peer returns up to kkk node IDs that it knows to be closest to the target. This process is repeated recursively, narrowing the search until the closest possible nodes are found. `FIND_NODE` is fundamental to Kademlia's efficient lookup mechanism, which allows nodes to locate information or other peers in logarithmic time.
    
* **FIND\_VALUE**: The `FIND_VALUE` RPC is similar in structure to `FIND_NODE`, but with one key difference: it attempts to **retrieve the actual value** associated with a key. When a node wants to retrieve stored data, it sends a `FIND_VALUE` message with the desired key. If the receiving node has the value, it returns it directly. If not, it behaves like `FIND_NODE` and returns a list of peers that are closer to the key, guiding the search forward. This RPC combines efficient lookup with data retrieval, making it the heart of Kademlia's decentralized data access model.
    

---

### 🧠 Conclusion

Kademlia elegantly solves the challenge of decentralized lookup and storage through a clever combination of XOR-based distance, recursive routing, and simple RPCs. Its ability to find any node or data item in logarithmic time, while gracefully handling churn and failures, makes it a foundational protocol in the peer-to-peer world. Whether you're building a distributed file system, a blockchain network, or a resilient storage layer, Kademlia offers a robust and scalable blueprint for decentralized communication. Its design shows that with the right abstractions, even large, dynamic networks can remain efficient, fault-tolerant, and remarkably simple.

---

For more updates, insights, and discussions, connect with me on Twitter: [@privacy\_prophet](https://x.com/privacy_prophet) or [LinkedIn](https://www.linkedin.com/in/rachit-anand-srivastava/).

---

## References

* [https://pdos.csail.mit.edu/~petar/papers/maymounkov-kademlia-lncs.pdf](https://pdos.csail.mit.edu/~petar/papers/maymounkov-kademlia-lncs.pdf)
    
* [https://codethechange.stanford.edu/guides/guide\_kademlia.html](https://codethechange.stanford.edu/guides/guide_kademlia.html#)