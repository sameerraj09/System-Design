### Key-Value Store Overview
- **Definition**: A key-value store is a type of non-relational database where data is stored as unique key-value pairs. Each key is unique and maps directly to a value.
  - Examples of keys: "last_logged_in_at", "user_id:1234".
  - Examples of values: Strings, lists, or objects.
- **Popular Implementations**: Amazon DynamoDB, Memcached, Redis.
- **Advantages**:
  - Simple structure.
  - Fast access to data.

---

### Understanding the Problem and Design Scope
- **Constraints**:
  - Keys and values are small (less than 10 KB per pair).
  - System must handle large-scale data.
  - High availability and scalability.
  - Automatic scaling of servers as traffic changes.
  - Configurable consistency (strong or eventual).
  - Low-latency operations.

---

### Single Server Key-Value Store
- **Approach**:
  - Use a hash table to store key-value pairs in memory.
- **Challenges**:
  - Memory is limited; cannot store large datasets.
  - Requires optimizations:
    - Data compression.
    - Storing frequently accessed data in memory and the rest on disk.

---

### Distributed Key-Value Store
- **Definition**: Extends key-value storage across multiple servers.
- **CAP Theorem**:
  - **Consistency**: All clients see the same data simultaneously.
  - **Availability**: System responds to all requests, even during failures.
  - **Partition Tolerance**: System works even if network partitions occur.
  - Trade-offs:
    - **CP Systems**: Prioritize consistency and partition tolerance (e.g., bank systems).
    - **AP Systems**: Prioritize availability and partition tolerance (e.g., social media feeds).
    - **CA Systems**: Not feasible due to inevitable network partitions.

---
### Notes on Ideal and Real-World Scenarios in Distributed Systems

#### **Ideal Situation**
- **Scenario**: In an ideal world, network partitions never occur.
  - Data written to a server (e.g., `n1`) is automatically replicated to other servers (e.g., `n2`, `n3`).
  - **Outcome**: Both consistency and availability are achieved simultaneously.

![image](https://github.com/user-attachments/assets/7b361eca-9839-4797-a4ab-c0eb26b1ebeb)

#### **Real-World Distributed Systems**
- **Reality**: Network partitions are unavoidable in real-world systems.
  - When partitions occur, trade-offs between **consistency** and **availability** must be made.

#### **Partition Scenario Example**
- **Issue**: One server (e.g., `n3`) cannot communicate with others (`n1` and `n2`).
  - Data written to `n1` or `n2` will not propagate to `n3`.
  - If data is written to `n3`, `n1` and `n2` will have stale data.

![image](https://github.com/user-attachments/assets/17a7d245-9588-45e6-819e-cfedf250dd10)


---

### **Choosing the Right CAP Trade-off**
- **Key Consideration**: The choice between **CP** and **AP** depends on the specific use case:
  - Use **CP** for systems that demand accurate and up-to-date information.
  - Use **AP** for systems where availability is critical, and eventual consistency is acceptable.

### Core Components and Techniques
#### 1. Data Partitioning
- **Why Partition?**
  - Single servers cannot handle large datasets.
- **How?**
  - Use **consistent hashing** to:
    - Evenly distribute data across servers.
    - Minimize data movement when nodes are added/removed.
  - Hash ring structure ensures efficient data placement.

![image](https://github.com/user-attachments/assets/a5d9588e-7260-4493-95b6-b9a95d02929e)

#### 2. Data Replication
- **Why Replicate?**
  - Ensure high availability and reliability.
- **How?**
  - Replicate data asynchronously to multiple servers.
  - Place replicas in different data centers for disaster recovery.
  - Example: A key is stored on 3 unique servers based on hash ring traversal.

![image](https://github.com/user-attachments/assets/928688a4-7450-4e00-a5f2-1b51be29b5b6)

#### 3. Consistency
- **Types**:
  - **Strong Consistency**: Always returns the latest data (e.g., banking systems).
  - **Eventual Consistency**: Updates propagate over time; allows stale reads temporarily.
- **Quorum Consensus**:
  - **N**: Total replicas.
  - **W**: Write quorum size.
  - **R**: Read quorum size.
  - Rules:
    - Fast writes: Set W = 1, R = N.
    - Fast reads: Set R = 1, W = N.
    - Strong consistency: Ensure W + R > N.

![image](https://github.com/user-attachments/assets/a09d3c44-dbf8-4761-9795-4675a0364666)

![image](https://github.com/user-attachments/assets/25724fa5-1f3b-4143-a47c-bbe3bc5d9eee)

#### 4. Inconsistency Resolution
- **Problem**: Concurrent writes can cause data conflicts.
- **Solution**:
  - Use **vector clocks** to track versions across servers.
  - Conflict resolution is handled by clients based on vector clock analysis.


![image](https://github.com/user-attachments/assets/4ca04c32-9924-46bf-a68f-d481be15a0aa)

#### 5. Handling Failures
- **Temporary Failures**:
  - Use **sloppy quorum**: Write to any healthy servers temporarily.
  - **Hinted Handoff**: Temporarily store data elsewhere and sync back when the server recovers.
- **Permanent Failures**:
  - Use **anti-entropy protocols** to synchronize replicas.
  - Employ **Merkle trees** to identify and resolve inconsistencies efficiently.

![image](https://github.com/user-attachments/assets/24a39307-6918-4155-a930-eaf5805dbf66)

![image](https://github.com/user-attachments/assets/0eb033b8-6ecd-4ab7-bcd2-6eca9d82c2c3)


#### 6. Data Center Outage
- **Solution**:
  - Replicate data across multiple data centers.
  - Ensure uninterrupted service even if one data center fails.

---

### System Architecture
- **Design Principles**:
  - Decentralized system with no single point of failure.
  - Nodes arranged on a hash ring using consistent hashing.
  - Data is replicated for high availability.
  - Clients interact with the system via simple APIs like `put(key, value)` and `get(key)`.

---

### Write Path
1. **Steps**:
   - Write is logged to a commit log for durability.
   - Data is cached in memory.
   - When memory is full, data is written to **SSTable** on disk (sorted string table).
2. **Why?**
   - Ensures durability and efficiency.
     
![image](https://github.com/user-attachments/assets/9ed51f19-4f3a-4019-86e2-ad78b50a14ec)

---

### Read Path
1. **Steps**:
   - Check memory for the requested data.
   - If not in memory, use a **Bloom filter** to identify the SSTable that might contain the key.
   - Retrieve data from disk.
2. **Efficiency**:
   - Bloom filters reduce disk reads by narrowing down data location.

![image](https://github.com/user-attachments/assets/40f43700-6060-4dee-bb48-b292c768dc6f)

---

### Summary of Features and Techniques
| **Feature**           | **Techniques Used**               |
|------------------------|-----------------------------------|
| Data Partitioning      | Consistent Hashing               |
| Data Replication       | Cross-Data Center Replicas       |
| Failure Handling       | Gossip Protocol, Hinted Handoff |
| Consistency Management | Vector Clocks, Merkle Trees      |
| Fast Data Access       | Commit Logs, Bloom Filters       |

