### Summary of Consistent Hashing and the Rehashing Problem:

1. **Purpose of Consistent Hashing**:
   - Consistent hashing is a technique used for distributing requests or data across a dynamic set of servers.
   - The primary goal is to ensure efficient and balanced load distribution while minimizing disruptions when servers are added or removed.

2. **Traditional Hashing Issue**:
![image](https://github.com/user-attachments/assets/2246f6d7-9625-4295-a028-5ff468f32fb6)

   -  For Example For instance, hash(key0) % 4 = 1 means a client must contact server 1 to fetch the cached data.
  
![image](https://github.com/user-attachments/assets/9386b042-92f8-48a7-b583-ef2a81607a75)

![image](https://github.com/user-attachments/assets/567929f9-bdeb-4f55-807f-760e478bb470)

3. **Problem with Rehashing**:
   - If the server pool size \( N \) changes (e.g., a server goes offline or a new server is added), the modular operation leads to reassigning keys to new servers.
   - This disrupts data locality since most keys will map to different servers. For instance, reducing the server pool from 4 to 3 changes the server mapping of many keys (as shown in Table 5-2).

![image](https://github.com/user-attachments/assets/64712e14-6ff1-4695-9caf-b94aa1239202)

![image](https://github.com/user-attachments/assets/b2b68745-26a4-4a84-aede-5d9816a46abf)


4. **Key Takeaway**:
   - While modular hashing is simple and works for fixed server pools, it causes instability and inefficiency when servers dynamically change, leading to unnecessary data migration across servers.


### Consistent Hashing 

1. **Mapping Servers to the Hash Ring**:
   - Servers are hashed onto the hash ring using the same hash function (e.g., SHA-1).
   - The hash value is based on unique identifiers for servers, such as their IP address or name.
   - Each server occupies a specific position on the circular hash ring.

2. **Distribution of Keys**:
   - Keys are also hashed using the same hash function and mapped to positions on the ring.
   - A key is assigned to the *next clockwise server* from its position on the ring.

### Server LookUP

To find the server for a key:  
1. Hash the key and locate its position on the hash ring.  
2. Move **clockwise** from the key's position until you reach the next server.  
Example:  
- **Key0** → Server 0  
- **Key1** → Server 1  
- **Key2** → Server 2  
- **Key3** → Server 3
  
![image](https://github.com/user-attachments/assets/bdfe327f-d04d-4ff0-965e-8a52e87cb5ee)


3. **Dynamic Server Pool**:
   - **Adding a Server**:
     
    When a **new server** is added:
   
1. The server is hashed and placed on the ring.  
2. Only the keys that fall **between the new server's position** and the next server clockwise are redistributed.  

### Example (Figure 5-8):  
- Before adding **Server 4**, **Key0** was stored on **Server 0**.  
- After adding **Server 4**, **Key0** is reassigned to **Server 4** because it is now the first server encountered clockwise from **Key0**'s position.  
- **Keys k1, k2, and k3** remain on their original servers (**Server 1, Server 2, and Server 3**, respectively).  

This limited redistribution ensures minimal disruption and efficient scaling.

![image](https://github.com/user-attachments/assets/aa84c165-58b2-4697-9160-5f9c7b674c8b)

   - **Removing a Server**:
   -  When a server is removed, only a small fraction of keys require redistribution with consistent
      hashing. In Figure 5-9, when server 1 is removed, only key1 must be remapped to server 2.
      The rest of the keys are unaffected.
      
![image](https://github.com/user-attachments/assets/e83213da-f528-4680-9316-cd9b7ff45247)

### Issues in the Basic Approach of Consistent Hashing:

1. **Uneven Partition Sizes**:
   - The hash space (partition) between adjacent servers on the ring can vary in size.
   - When a server is added or removed, the resulting partitions may become disproportionately small or large.
   - Example (Figure 5-10):
     - If **Server s1** is removed, **Server s2** inherits the partition that spans the hash space between **s0** and **s3**.
     - This makes **s2's partition** much larger compared to **s0** and **s3**, leading to an **imbalance in load distribution**.

![image](https://github.com/user-attachments/assets/e4143900-f1ec-4ecb-98fc-2eeefbf76fa5)
### Second Issue: Non-Uniform Key Distribution

1. **Problem**:
   - When servers are unevenly distributed on the ring, keys may cluster around certain servers while leaving others empty or underutilized.
   - Example (Figure 5-11):
     - If servers are mapped in a way where **Server 2** occupies a large portion of the ring, most keys fall into its partition.
     - **Server 1** and **Server 3** may not receive any keys, leading to an **imbalance in data distribution**.

2. **Cause**:
   - The non-uniform placement of servers on the ring due to uneven hashing results in poor load balancing.

![image](https://github.com/user-attachments/assets/a3e4b651-d5ec-4eb5-b861-7911d04eee06)

### **Virtual Nodes in Consistent Hashing**

1. **Definition**:  
   - A **virtual node** is a representation of a real server on the hash ring.  
   - Each server is mapped to multiple virtual nodes, which are spread uniformly across the ring.

2. **Example**:  
   - For **Server 0 (s0)**, virtual nodes are labeled as \( s0\_0, s0\_1, s0\_2 \), each occupying a separate position on the ring.  
   - Similarly, **Server 1 (s1)** is represented as \( s1\_0, s1\_1, s1\_2 \).

3. **Impact of Virtual Nodes**:  
   - **Balanced Partitioning**:
     - By having multiple virtual nodes per server, partitions are distributed more evenly.
     - Each server is responsible for multiple smaller partitions instead of one large one.
   - **Improved Load Balancing**:
     - Keys are distributed more uniformly across the hash ring, reducing the chances of one server being overloaded.
   - **Fault Tolerance**:
     - When a server goes offline, its virtual nodes are reassigned, but the overall disruption is minimal since the load is spread across multiple servers.

4. **Practical Implementation**:  
   - The number of virtual nodes per server (e.g., 3 in Figure 5-12) is **arbitrary** and typically much larger in real-world systems to ensure fine-grained distribution.

![image](https://github.com/user-attachments/assets/90d16652-416e-429f-884e-b9c9f3cd6c1c)

![image](https://github.com/user-attachments/assets/a95a0b25-cf59-4a9e-b7bb-7e87dbe4678a)

   
### **Virtual Nodes and Trade-offs**

1. **Balancing Keys with Virtual Nodes**:
   - Increasing the number of virtual nodes improves **key distribution** by reducing the **standard deviation**.  
   - Example:
     - **100 virtual nodes**: Standard deviation ~10% of the mean.  
     - **200 virtual nodes**: Standard deviation ~5% of the mean.  

2. **Trade-off**:
   - **More Virtual Nodes** → Better load balancing but requires **more storage** for managing virtual node metadata.  
   - The optimal number of virtual nodes depends on system requirements and available resources.

---

### **Finding Affected Keys During Server Changes**

1. **When a Server is Added**:
   - The **new server's position** on the ring determines the affected range.  
   - Keys in the hash space between the **new server (s4)** and the **previous server (s3)** in the **anticlockwise** direction need to be redistributed.  

2. **Example (Figure 5-14)**:
   - If **Server 4 (s4)** is added:
     - The affected range is from **s3 to s4**.  
     - Keys in this range are reassigned to **s4**, while other keys remain unaffected.

![image](https://github.com/user-attachments/assets/17b6466d-15ef-4f81-9f04-1b18a7bc6b1d)

![image](https://github.com/user-attachments/assets/e36f3414-7f8f-4e96-a5fd-478c67a9785e)

