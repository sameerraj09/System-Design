# System-Design
Maintaining Notes Of System Design


**DAY 1**

**System Design Overview:-**

System design is essentially the process of defining the architecture, components, and flow of a software system to meet specific requirements. Here’s a simple breakdown:

1. **Understanding Requirements**: Before designing, gather what the system needs to do (e.g., handle 1 million users, process payments, store photos). These requirements can be *functional* (what it should do) and *non-functional* (how well it should perform).

2. **Choosing the Components**: Think of a system as a collection of building blocks. These blocks can include:
   - **Database**: Where data is stored (e.g., user info, posts).
   - **Servers**: Machines that handle tasks (e.g., running your code, serving data).
   - **APIs**: Points where different parts communicate, like a bridge for data.
   - **Caching**: Temporary storage to speed up frequent requests (like loading a profile picture faster).

3. **Setting Up Architecture**: The architecture is like the blueprint. Popular styles include:
   - **Monolithic**: All components are tightly linked, simpler to manage but harder to scale.
   - **Microservices**: Each function is its own mini-system (e.g., separate services for login, profile, payments), making it easier to scale but more complex.

4. **Handling Scale and Performance**: When the system grows, it must stay fast and reliable. So, you design for:
   - **Load Balancing**: Splits user requests across multiple servers to avoid overload.
   - **Replication**: Duplicates data to make it available even if one source fails.
   - **Sharding**: Splits data across multiple databases so one database doesn’t get overwhelmed.

5. **Ensuring Security and Reliability**: Build features to keep data safe and systems dependable:
   - **Authentication** and **Authorization**: Control who can access what.
   - **Data Encryption**: Keeps data secure.
   - **Monitoring and Logging**: Tracks system health and detects issues early.

In a nutshell, system design is about piecing together software components thoughtfully so that the whole system performs well, scales as needed, and meets user expectations. It requires balancing simplicity and efficiency with future growth in mind.

 **Core terms in system design:**

### 1. **Scalability**
   - *Definition*: The system’s ability to grow and handle more load (like more users or data) without slowing down or crashing.
   - *Types*: 
     - **Vertical Scaling**: Adding more power (CPU, memory) to a single server.
     - **Horizontal Scaling**: Adding more servers to share the load (like spreading tasks across multiple workers).

### 2. **Latency**
   - *Definition*: The time it takes for data to travel from one point to another (like a user clicking a button and seeing a response).
   - *Goal*: Keep latency low for a fast, responsive user experience.

### 3. **Throughput**
   - *Definition*: The amount of data processed in a certain period. Think of it as the "speed" at which the system completes tasks.
   - *Example*: The number of requests a server can handle per second.

### 4. **Availability**
   - *Definition*: The percentage of time a system is up and running. High availability means it rarely goes down.
   - *Goal*: Keep services available as much as possible, especially for critical systems (e.g., 99.9% uptime or "three nines").

### 5. **Reliability**
   - *Definition*: The consistency of the system in performing without errors.
   - *Example*: When a system is reliable, users can trust that it will work correctly each time.

### 6. **Fault Tolerance**
   - *Definition*: The system’s ability to keep working, even when something goes wrong (like a server failure).
   - *Example*: If one server fails, another takes over to keep things running.

### 7. **Load Balancing**
   - *Definition*: Distributes traffic across multiple servers to avoid overloading one server and keep response times fast.
   - *Example*: Think of it as a traffic cop directing cars (user requests) evenly across all open lanes (servers).

### 8. **Caching**
   - *Definition*: Storing a copy of frequently used data in a fast-access location to speed up response times.
   - *Example*: Loading profile pictures faster because they’re saved (cached) temporarily.

### 9. **Replication**
   - *Definition*: Making copies of data and storing them in multiple locations.
   - *Goal*: Increases data availability and reliability. If one location fails, another has the same data.

### 10. **Sharding**
   - *Definition*: Splitting data across multiple databases or locations to distribute the load.
   - *Example*: Users A-M in one database, and N-Z in another. This keeps each database smaller and faster.

### 11. **Partitioning**
   - *Definition*: Similar to sharding, it divides data logically, but might be based on different rules, such as dividing by region or data type.
   - *Example*: Data from different regions (e.g., US vs. Europe) in separate partitions.

### 12. **Data Consistency**
   - *Definition*: Ensures data remains the same (consistent) across all places where it's stored or used.
   - *Types*: 
     - **Strong Consistency**: Data updates immediately and is the same everywhere.
     - **Eventual Consistency**: Data may be temporarily different in some places but will eventually match.

### 13. **Concurrency**
   - *Definition*: Handling multiple tasks at the same time, like multiple users using the app at once.
   - *Goal*: Ensure tasks don’t interfere with each other, especially when accessing shared data.

### 14. **Database Indexing**
   - *Definition*: Creating a "shortcut" in the database so searches and lookups are faster.
   - *Example*: Like a book’s index, which helps you find pages quickly.

### 15. **API (Application Programming Interface)**
   - *Definition*: A set of rules and endpoints that allow different parts of a system or different systems to talk to each other.
   - *Example*: A login API lets different apps use the same authentication system.

### 16. **Message Queue**
   - *Definition*: A system for sending messages between different parts of a system. Messages are stored in a queue until they’re processed.
   - *Example*: If too many people try to place orders, the queue helps handle requests one by one.

### 17. **Microservices**
   - *Definition*: Splitting a big application into smaller, independently running parts (services) for each feature (e.g., login, search, payments).
   - *Advantage*: Each service can be scaled or updated separately.

### 18. **Monolithic Architecture**
   - *Definition*: The entire application is built as one large unit.
   - *Example*: Easier to manage in the beginning but harder to scale and update as it grows.

### 19. **CDN (Content Delivery Network)**
   - *Definition*: A network of servers located around the world to deliver content (like images or videos) faster to users based on their location.
   - *Example*: If you’re in the US, the CDN delivers data from a nearby server, reducing load times.

### 20. **Rate Limiting**
   - *Definition*: Limiting the number of requests a user or service can make in a certain time to prevent overloading.
   - *Example*: A website allowing only 10 requests per second per user.

These are some of the basic terms that are essential for understanding and designing systems effectively.

**Flow for a single server system :-**

How we browse a website

![image](https://github.com/user-attachments/assets/92ebe602-039e-4d0f-84c2-7b228217138f)
![image](https://github.com/user-attachments/assets/f1cfed17-baad-433d-b3f1-909dfc7c910b)
![image](https://github.com/user-attachments/assets/2a76c525-4b87-4600-8f99-c087569559ec)

- Non-Relational databases are also called NoSQL databases. 
Popular ones are CouchDB, Neo4j, Cassandra, HBase, Amazon DynamoDB, etc. [2].
These databases are grouped into four categories: key-value stores, graph stores, column stores, and document stores. Join operations are generally not supported in non-relational databases

**Why nosql:-**

![image](https://github.com/user-attachments/assets/a8d18283-889a-4540-8a43-e272312864d9)

![image](https://github.com/user-attachments/assets/24930d13-b67e-447a-a911-cc593c4f0a7a)

**Here are the key points:**

1. **Vertical Scaling (Scale-Up)**:
   - Involves adding more power (CPU, RAM) to a single server.
   - Suitable for low traffic scenarios due to its simplicity.
   - **Limitations**:
     - **Hard limit**: Cannot add unlimited CPU or memory to one server.
     - **No failover/redundancy**: If the server goes down, the entire application/website goes offline.

2. **Horizontal Scaling (Scale-Out)**:
   - Involves adding more servers to handle traffic.
   - Preferred for large-scale applications due to vertical scaling limitations.
   - Adds more reliability, redundancy, and can handle higher traffic loads.

3. **Problems with Direct Web Server Connection**:
   - Users cannot access the site if the web server is offline.
   - When many users access the server simultaneously, it can exceed the server’s load limit, leading to slow responses or connection failures.

4. **Load Balancer**:
   - Distributes incoming traffic evenly among multiple web servers.
   - Helps manage load and prevent issues like server overload or downtime.
   - Essential for high availability and smooth scaling in large-scale applications.

![image](https://github.com/user-attachments/assets/eb93db5e-b50f-489a-811a-5c692c49e617)

-As shown in Figure 1-4, users connect to the public IP of the load balancer directly.
-With this setup, web servers are unreachable directly by clients anymore. For better security, private IPs are used for communication between servers. 
-A** private IP **is an IP address reachable only between servers in the same network; however, it is unreachable over the internet.
-The load balancer communicates with web servers through private Ips


**DAY - 2**
-In Figure 1-4, after a load balancer and a second web server are added,
we successfully solved no failover issue and improved the availability of the web tier. 
-If server 1 goes offline, all the traffic will be routed to server 2.
 This prevents the website from going offline. We will also add a new healthy web server to the server pool to balance the load.
-If the website traffic grows rapidly, and two servers are not enough to handle the traffic,the load balancer can handle this problem gracefully. You only need to add more servers to the web server pool, and the load balancer automatically starts to send requests to them.
**- Database replication**

- The current design has one database, so it does not support failover and redundancy. Database replication is a common technique to address those problems. Let us take a look

 Quoted from Wikipedia: “Database replication can be used in many database management
 systems, usually with a master/slave relationship between the original (master) and the copies
 (slaves)”.
A master database generally only supports write operations.
A slave database gets copies of the data from the master database and only supports read operations.
All the data-modifying commands like insert, delete, or update must be sent to the master database. 
Most applications require a much higher ratio of reads to writes; thus, the number of slave databases in a system is usually larger than the number of master databases. Figure 1-5 shows a master database with multiple slave databases
![image](https://github.com/user-attachments/assets/119db8c7-e6a8-4b04-ab7e-9640a13d5eb6)

**Advantages of database replication:**

- **Improved Performance**: In a master-slave setup, writes occur on the master, while reads are distributed across slaves, allowing parallel processing and faster query handling.
- **Reliability**: Data is preserved across multiple servers, protecting against data loss from disasters.
- **High Availability**: Replication across locations ensures continuous access even if one database goes offline.

** what if one of the databases goes offline?**

- If one slave and it goes down then all read will be done by master temporarily.Down slave will be replace by the health slave
- If the master database goes offline, a slave database will be promoted to be the new master. A new slave database will replace the old one for data replication immediately.
-  The missing data needs to be updated by running data recovery scripts.

![image](https://github.com/user-attachments/assets/5bdbbcc6-3c1c-4302-a083-bafdbe11178b)


Now, you have a solid understanding of the web and data tiers, it is time to improve the
 load/response time.


**Cache:-**
-A cache is a temporary storage area that stores the result of expensive responses or frequently
 accessed data in memory so that subsequent requests are served more quickly. 
 
** Cache tier**
- The cache tier is a temporary data store layer, much faster than the database.
 - The benefits of having a separate cache tier include better system performance, ability to reduce database workloads, and the ability to scale the cache tier independently.

![image](https://github.com/user-attachments/assets/b40c2db6-6c9b-4a05-9487-d0e442c33eef)

 This caching strategy is called a read-through cache.
 ![image](https://github.com/user-attachments/assets/156f718f-b46a-45c1-af31-4add5d7c6811)

** Considerations for using cache**

-**Decide when to use cache**. Consider using cache when data is read frequently but modified infrequently. 
- **Expiration policy.** It is a good practice to implement an expiration policy.Meanwhile, it is advisable not to make the expiration date too long as the data can become stale or too short.
-Consistency: This involves keeping the data store and the cache in sync.
- **Consistency**: Ensures the data store and cache are in sync, which is challenging across regions since cache and database updates don’t occur in a single transaction.

- **Mitigating Failures**: Using multiple cache servers across data centers prevents single points of failure (SPOF) and improves reliability. Overprovisioning memory also helps handle increased usage.
![image](https://github.com/user-attachments/assets/fabc321b-4d61-425b-889e-31a8a42ef3d3)

- **Eviction Policy**: When cache is full, older items are removed to make space. Common policies include Least Recently Used (LRU), Least Frequently Used (LFU), and First In, First Out (FIFO).
