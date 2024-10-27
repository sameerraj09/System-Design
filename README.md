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
