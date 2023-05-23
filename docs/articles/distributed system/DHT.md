# Distributed Hash Table
The first generation of P2P file networks required central database coordination, as seen in the popular music file sharing system Napster around the year 2000. In Napster, a central server received all queries and provided the client with a list of data addresses it needed. Such a design was prone to single point of failure, and could even lead to the entire network becoming paralyzed.

In the second generation of distributed file systems, Gnutella used a message flooding approach to locate data. Query messages were broadcasted to all nodes in the network until the information was found and returned to the requester. However, due to limited network capacity, this blind querying could quickly deplete network resources. To control the number of requests within the network, a time-to-live (TTL) value was set to limit the lifespan of each request. Nevertheless, this method still required a significant amount of network requests, which easily resulted in congestion.


In the third generation of distributed file systems, the innovation of Distributed Hash Table (DHT) provided a new solution. The main idea behind DHT is as follows: The entire network maintains a massive file index hash table, where each entry is in the form of &lt;Key, Value>. The Key is typically the hash value of a file using a certain hashing algorithm (it can also be the file name or file content description), and the Value is the IP address where the file is stored. During a query, only the Key needs to be provided, and the querying node can retrieve the address of the storage node from the table and return it to the querying node.

Of course, this hash table is partitioned into smaller chunks and distributed across nodes in the network based on specific algorithms and rules. Each node only needs to maintain a small portion of the hash table. This way, when a node queries a file, it only needs to route the query message to the corresponding node.

Below are three representative partition table types referenced by IPFS, namely Kademlia DHT, Coral DHT, and S/Kademlia.

## 1 Kademlia DHT
Kademlia DHT is an implementation of a distributed hash table, and its inventors are Petar Maymounkov and David Mazieres. Kademlia DHT possesses several notable features, as follows:
1. Both node IDs and keys are generated using the SHA-1 algorithm, resulting in 160-bit digests. This simplifies the amount of information needed during queries and makes it easier to perform searches.
2. Kademlia DHT utilizes XOR to calculate the distance between any two nodes or between a node and a keyword. This distance metric plays a crucial role in the routing process.
3. When searching for a requested item, each node has complete information, and the routing process typically requires only logarithmic (Log(n)) jumps, where n is the total number of nodes in the network. This ensures efficient path finding.
4. The size of the DHT that each node needs to maintain can be adjusted based on the requirements for query speed and storage capacity. This allows for scalability, as nodes can adapt to the network's needs.

The Kademlia (KAD) network brings significant improvements to the previously mentioned DHT. When a new node joins the network, it is assigned an ID. Each node maintains a routing table and a DHT. The routing table contains connection information for a subset of nodes in the network, while the DHT stores file information. Each node prioritizes storing information about nodes that are closer in distance to itself. However, it ensures that it maintains at least k nodes (where k is a constant) from the entire range of nodes within the distance range [2^n, 2^(n+1)-1]. These nodes are stored in what is known as K-Buckets.

Each network node gives priority to storing files that are closer in distance to its own ID. During a retrieval, the node calculates the distance between the hash value of the queried file and its own ID. It then identifies the corresponding K-Bucket for that distance and queries the nodes in that K-Bucket. If a queried node possesses the requested data, it returns it to the querying node.

> **Kademlia Binary State Tree:** The node ID in the Kademlia network is maintained by a binary state tree. The characteristics of the resulting binary tree are as follows:
>- Each network node starts from the root node and follows its shortest unique prefix to reach its position.
>- Each network node is a leaf node. For any node in the tree, we can traverse down the tree along its prefix, decomposing it into a series of subtrees that do not include itself. The establishment of the Kademlia binary tree ensures that each network node can be reached from the tree root by following its shortest unique prefix path.

In Kademlia, each DHT entry consists of a &lt;key, value> pair. Every new computer joining the network is randomly assigned a node ID. Value is stored on the node whose ID is closest to the key of the value. To determine the closeness between key values and ID values, we need to define a distance metric. XOR operation can be used to solve this problem. For <key, value> pairs represented by a 160-bit hash, the distance between two nodes x and y is calculated using the binary XOR operation, denoted as d(x, y) = x ⊕ y. When performing XOR on two binary digits, if they are the same, the XOR value is 0; if they are different, the XOR value is 1. For example, let's take the XOR of 111010 and 101011.

> For the XOR operation, there are several mathematical properties:
> 1. d(x, x) = 0: The XOR distance between an element and itself is always zero.
> 2. d(x, y) > 0, if x ≠ y: The XOR distance between two distinct elements is always greater than zero.
> 3. Symmetry: For any elements x and y, d(x, y) = d(y, x). The XOR distance is symmetric.
> 4. Triangle Inequality: For any elements x, y, and z, d(x, y) + d(y, z) ≥ d(x, z). The sum of XOR distances between three elements is greater than or equal to the XOR distance between the first and third elements.
> 5. XOR Composition: For any elements x, y, and z, d(x, y) ^ d(y, z) = d(x, z). The XOR distance between two elements can be obtained by XORing the XOR distances between the elements with a common element.
> 6. Existence of x ≥ 0, y ≥ 0, such that x + y ≥ x ^ y: There exist non-negative integers x and y such that their sum is greater than or equal to their XOR.

We naturally observe that given x, any value a (a ≥ 0) uniquely determines another node y such that d(x, y) = a. Let's assume x is the key of the file we want to query. By continuously updating y in the direction where d(x, y) decreases, we can ensure that we converge to the point closest to x. As mentioned earlier, files are stored on the nodes with the XOR distance closest to the file's hash value. In other words, as long as we search in the direction of decreasing XOR distance, starting from any network node, we will always be able to find the address where the file is stored. Moreover, with each step, we are able to filter out half of the nodes, which means that it takes at most Log N steps to reach the destination.

### 1.1 Node Routing Table K-Bucket
The node routing table is used to store the connection information of other nodes within a certain distance range from each node. Each routing entry consists of three parts: IPAddress, UDP Port, and NodeID. In the Kademlia routing table, the key space is divided into 160 K-buckets, each capable of storing K nodes. The i-th routing table contains K entries for distances in the range [2^i, 2^(i+1)-1]. Within each K-bucket, the entries are ordered based on the last seen time, with the earliest seen entry at the head and the latest seen entry at the tail. This ordering is important because nodes in the network can be either online or offline, and nodes that were frequently online in the past have a higher probability of being online when we need to access them. Therefore, we prioritize accessing the nodes at the tail of each K-bucket.

Typically, when the value of i is small, the corresponding K-bucket is often empty (taking 0 as an example, the distance 0 corresponds to only one node, which is the node itself). On the other hand, when i is large, the corresponding K-bucket can have a large number of entries due to the larger distance range. In this case, we select only K entries to be stored. The choice of K needs to balance system performance and network load. For example, in the implementation of BitTorrent, the value of K is set to 8.

Since each K-bucket covers an exponentially increasing distance range, it is sufficient to store up to 160K routing entries to cover the entire range of nodes. During the querying process, a recursive approach is used. It can be proven that in a Kademlia network with N nodes, it takes at most logN steps to accurately locate the target node.

When node x receives a message, the sender's IP address y is used to update the corresponding K-bucket. The specific steps are as follows:
1. Calculate the distance between oneself and the sender's ID: d(x, y) = x ⊕ y.
2. Select the corresponding K-bucket based on the distance d for the update operation.
3. If y's IP address already exists in the K-bucket, move the corresponding entry to the tail of that K-bucket. If y's IP address is not recorded in the K-bucket, the following actions are taken:
   - If the number of entries in the bucket is less than k, directly insert the (IP address, UDP port, NodeID) information of y at the end of the queue.
   - If the number of entries in the K-bucket is greater than or equal to k, perform an RPC PING operation on the entry at the head of the bucket (let's say it is node z).
      * If z does not respond, remove z's information from the bucket and insert y's information at the end of the queue.
      * If z responds, move z's information to the end of the queue and ignore y's information.

The update mechanism of K-buckets efficiently implements a strategy to prioritize the most recently seen nodes, unless an online node has never been removed from the K-bucket. This means that nodes with longer online durations are more likely to remain in the K-bucket list. This mechanism is based on research into user behavior patterns in the Gnutella network, which showed a positive correlation between a node's online probability and its online duration.

By keeping nodes with longer online durations in the K-bucket, the probability of having online nodes in the K-bucket at the next time interval is significantly increased. This helps maintain the stability of the Kademlia network and reduces network maintenance costs by avoiding frequent reconstruction of node routing tables.

### 1.2 Kademlia Routing Query Mechanism
One of the key features of Kademlia technology is its ability to provide an efficient node lookup mechanism, which can also be adjusted for query speed through parameters. Suppose a node x wants to locate A node with an ID value of t. Kademlia performs the following recursive steps for routing lookup:
1. Calculate the distance to t: d(x, t) = x ⊕ t.
2. Retrieve information of S nodes from the log(d)th K-bucket of x and perform FIND_NODE operation. If the information in this K-bucket is less than A, select a total of A nodes that are closest to d from multiple nearby buckets.
3. For each node that receives the query operation, if it finds that it is t itself, it responds that it is the closest to t. Otherwise, it measures the distance between itself and t, and selects information of A nodes from its corresponding K-bucket to give to x.
4. x performs FIND_NODE operation again on each newly received node, and this process repeats until every branch has a node responding that it is closest to t.
5. Through the above lookup operation, x obtains information on k nodes that are closest to t.

It should be emphasized that the k nodes obtained are the ones closest to t in terms of distance, as it is possible that there may not be a node with the ID t in the network. The parameter a is also set to balance performance, similar to k. In the BitTorrent implementation, the value of a is set to 3. This recursive process continues until x = t or there is no information about t in the routing table. Since each query can retrieve information from K-buckets that are closer to t, this mechanism ensures that each recursive operation obtains at least a halving effect (or a 1-bit reduction) in distance, thus guaranteeing a convergence speed of O(logN) for the entire query process, where N is the total number of nodes in the network.

The aforementioned method describes how to query for node IDs, and the same approach applies to file queries. The only difference is that it involves performing a FIND VALUE operation to determine if the node itself has the file with the ID t. The convergence speed of the file query process is also O(logN), similar to the node ID query.

### 1.3 Node Join and Depature
If node u wants to join the Kademlia network, it needs to establish contact with an existing node in the network, let's say node w. Here are the steps for node u to join the network:

1. u inserts w into the appropriate bucket in its own routing table.
2. u performs a FIND NODE operation on its own node ID and retrieves information from nearby nodes.
3. u updates its own routing table by incorporating the received information.
4. u continues querying neighboring nodes in a progressive manner, from closest to farthest, to build the routing table. At the same tiem, u also its own information was published to the routing tables of other nodes.

In the Kademlia network, each node's routing table is represented as a binary tree, where the leaf nodes correspond to the K-buckets. Each K-bucket stores information about nodes with the same ID prefix, and the position of the bucket in the binary tree represents the prefix. Consequently, each K-bucket covers a specific portion of the ID space, and the combined information of all K-buckets covers the entire 160-bit ID space without any overlaps.

Taking node u as an example, here is the process for generating its routing table:
1. Initially, u's routing table consists of a single K-bucket that covers the entire 160-bit ID space.
2. When u learns about a new node, it attempts to insert the new node's information into the corresponding K-bucket based on its prefix value.
   - If the bucket is not full, the new node is inserted directly into that K-bucket.
   - If the K-bucket is full:
      a. If the K-bucket's range includes the ID of the new node, the bucket is split into two equally sized new K-buckets. The nodes in the original bucket are re-allocated to the new buckets based on their new prefix values.
      b. If the K-bucket's range does not include the ID of the new node, the new node's information is discarded.
3. The above process is repeated continuously until the routing table meets the desired requirements. The result is that nodes closer in distance to u have more information stored, while nodes farther away have less information. This ensures that the routing lookup process can converge quickly.

When a node leaves the KAD network, it does not need to publish any specific information. Instead, it waits for a sufficient amount of time for other network nodes to realize that it is offline.

## 2 S/Kademlia DHT
Kademlia is indeed used in fully open P2P networks, and without any security measures, it is vulnerable to various types of attacks from malicious nodes. To address these concerns, Ingmar Baumgart and Sebastian Mies designed a more secure protocol called S/Kademlia (S/K). Building upon the Kademlia protocol, the S/K protocol incorporates implicit identity authentication and sibling broadcast into the node ID. This enhancement provides S/K with the capability to defend against common attacks such as eclipse attacks and Sybil attacks. 

### 2.1 The Attack Kademlia Faces
Kademlia faces various types of attacks, including:
1. Eclipse Attack: In an eclipse attack, an attacker strategically places malicious nodes in the network, controlling the paths through which information must flow. By doing so, the attacker can hide one or more nodes from the rest of the network, isolating them and potentially manipulating their communication.
2. Sybil Attack: In an open peer-to-peer network, attackers can create multiple fake identities (Sybil nodes) and control them using a small number of network nodes. With a large number of Sybil nodes, attackers can gain control over a significant portion of the network. Mitigating Sybil attacks often requires consuming system and computational resources to raise the cost for the attacker, although complete prevention is difficult.
3. Churn Attack: A churn attack occurs when an attacker possesses several nodes in the network, which can lead to significant churn or loss of nodes, resulting in reduced network stability and performance.
4. Adversarial Routing Attack: In an adversarial routing attack, malicious nodes deviate from the Kademlia protocol by not returning the closest network nodes as required. Instead, they pass the query to their accomplice nodes, creating a chain of non-responsive nodes. This renders the query ineffective. To mitigate this attack, algorithms can be designed to parallelize the queries and ensure that each query path is non-intersecting. As long as one of the parallel query paths does not encounter a malicious node, the query can succeed.

### 2.2 The protection strategies of S/K
To protect against eclipse attacks and Sybil attacks, S/K requires that nodes cannot freely choose their node IDs, cannot generate IDs in large quantities, and cannot steal or impersonate the IDs of other nodes. This can be achieved through asymmetric encryption to ensure that node identities are not stolen. We can also introduce computational barriers that require nodes to perform certain hash computations, thereby preventing the freedom of ID selection and mass ID generation.

To mitigate against routing attacks, we need to perform parallel searches along non-intersecting paths.
#### Secure nodeId assignment
S/K has three requirements for its node ID allocation strategy:
1. Nodes cannot freely choose their ID.
2. Nodes cannot generate multiple IDs.
3. Nodes cannot impersonate or steal the IDs of other nodes.

To meet these requirements, S/K implements the following methods to increase the difficulty of attacks. Each node must solve two cryptographic problems before joining the network:
1. Static Problem: Generate a public-private key pair and ensure that the hashed value of the public key, after being hashed again, has c1 leading zeros. This hashed value becomes the NodeID of the node. This static problem ensures that nodes cannot freely choose their node ID.
2. Dynamic Problem: Continuously generate a random number X, perform an XOR operation between X and the NodeID, and then hash the result. The hashed value must have c2 leading zeros. This dynamic problem increases the cost of generating a large number of IDs. It makes it difficult to conduct Sybil attacks and eclipse attacks.

By implementing these cryptographic problems, S/K effectively addresses the challenges of Sybil attacks and eclipse attacks by limiting node ID selection and increasing the cost of ID generation.

To ensure that node identities are not stolen, nodes need to sign the messages they send. When other nodes receive a message, they first verify the validity of the signature and then check if the node ID meets the requirements of the two aforementioned cryptographic problems.

The time complexity for verifying the legitimacy of the information by other nodes in the network is only O(1). However, for attackers, in order to generate a valid attack message, they would need to select appropriate values for c1 and c2. By doing so, the three attack methods can be effectively mitigated.

#### Lookup over Disjoint Paths
In the Kademlia protocol, when we perform a query, we access A nodes in the K-Bucket, which are closest to the key we need to query. Once we receive replies, we further sort the returned node information and select the top "a" nodes to continue the iteration for requests. However, it is evident that this approach has a drawback. If the returned information contains a group of malicious nodes, the query is likely to fail.

To address this issue, the proposed solution in S/K is as follows: Instead of selecting nodes from a single K-Bucket during each query, we choose k nodes from d different Buckets. These d Buckets are searched in parallel, and the internal lookup process within each Bucket follows the same method as the Kademlia protocol. By doing so, the d search paths remain disjoint. While there is a possibility of individual Buckets becoming compromised, as long as one of the d Buckets successfully retrieves the required information, the overall process is considered complete.

By utilizing disjoint paths for lookups, the S/K protocol mitigates against routing attacks. The S/K protocol is an improvement over the Kademlia protocol and significantly enhances its security performance against common attacks.

## 3 Coral DSHT
Coral protocol, invented in 2004 by Michael Freedman, Eric Freudenthal, and David Nazieres at New York University, is a Content Delivery Network (CDN) system. CDNs are designed to circumvent Internet transmission bottlenecks and alleviate network pressure on content servers, enabling faster and more reliable content delivery to clients. The basic concept of a CDN is to deploy a network of edge servers and establish a virtual network. The servers within the network continuously transmit connection information, latency information, user proximity parameters, and so on. User requests are then redirected to the most suitable server within the network.

There are several benefits to this approach:
1. By leveraging node servers for content delivery, the speed at which users can access web pages is significantly increased.
2. Node servers cache query information from content servers. This reduces the network load on the content servers.
3. In cases where a content server may be temporarily offline or experiencing issues, users can still access the content through the cached copies stored on the node servers.

Coral DSHT is a core component of the Coral CDN system. As mentioned earlier, the Kademlia protocol uses XOR distance to determine which node should store the information. However, it doesn't consider factors such as network latency and data location, which can result in inefficient use of network bandwidth and storage space. Coral addresses this issue by evaluating the connectivity between nodes and dividing them into several tiers based on round-trip time. In Coral, there are three tiers: L2 (<20ms), L1 (<60ms), and L0 (others). Coral also provides two operational interfaces: "put" and "get." These interfaces allow users to add and retrieve key-value pairs, determining which tier of the DSHT should be queried. 

Indeed, Coral DSHT is suitable for retrieving soft-state key-value pairs, where multiple values can be associated with the same key. This mechanism allows mapping a given key to the addresses of Coral servers within the network. For example, it can be used to query domain name servers that are closer to the user, or to search for servers that have cached information for a specific website. It enables locating nearby nodes to minimize request latency and improve the efficiency of content delivery.

### 3.1 Hierarchical lookup layer
Coral handles routing tables in a unique way. Each Coral node is placed in a different DSHT based on its latency characteristics. Nodes within the same DSHT are referred to as a cluster, and each cluster has a maximum latency known as the cluster diameter. The entire system has a predefined diameter, known as the level. Under each level division, each node belongs to a specific DSHT. A group of nodes can only form a cluster if the pairwise diameter between them is smaller than the diameter specified for the corresponding level.

In Coral, the DSHT is divided into three layers. Level-2 corresponds to pairwise latencies smaller than 20 milliseconds, Level-1 corresponds to pairwise latencies smaller than 60 milliseconds, and Level-0 includes all other nodes. When querying in Coral, priority is given to nodes in higher levels with shorter response times. If the query fails, the next level of nodes is requested. This design not only reduces query latency but also increases the likelihood of obtaining data from nearby nodes first.

### 3.2 Sloppy storage layer

In the Kademlia protocol, data is stored directly in nodes that are closer in XOR distance. However, in reality, if certain data is highly popular, other nodes will also receive a large number of queries, leading to congestion. This situation is known as a "Hot-Spot." Additionally, if a cache key-value pair stores too many values, it is referred to as "Tree-saturation." Sloppy storage aims to mitigate these two scenarios.

In Coral, each node defines two exceptional states: Full and Loaded. The Full state is defined as follows: In the current node R, there exist L &lt;Key, Value> pairs where Key=k, and the lifetime of these L key-value pairs is greater than half the lifetime of the new value. The Loaded state is defined as follows: For a given Key=k, the node has received requests exceeding a certain threshold within the past minute.

When performing a storage operation, Coral divides it into two steps.

Step 1 is the forward query, where Coral iteratively searches for nodes with IDs closer to the Key, similar to the Kademlia protocol. Each node returns two pieces of information: (1) whether the node is loaded and (2) the number of Values stored for that Key and the expiration time for each Value. Based on this information, the client decides whether these nodes can store a new value. The forward query process continues until the client finds the closest connectable node to the Key. If there is an exception during the query for a particular node, it will no longer be iterated. The connectable nodes are then pushed into a stack one by one.