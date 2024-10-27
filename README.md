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
-A private IP is an IP address reachable only between servers in the same network; however, it is unreachable over the internet.
-The load balancer communicates with web servers through private Ips


