### Notes on Unique ID Generator in Distributed Systems

#### 1. **Understanding the Problem and Requirements**
   - **Goal**: Generate unique IDs in a distributed system.
   - **Characteristics of the ID**:
     - IDs must be unique.
     - IDs are numerical.
     - Fit into 64 bits.
     - Ordered by date (sortable).
     - Should handle 10,000+ IDs per second.

       ![image](https://github.com/user-attachments/assets/0635972c-b78e-487c-8328-0395c482b9d9)


#### 2. **High-Level Design Options**
   - **Multi-Master Replication**:
     - Use database auto-increment with increments based on the number of servers.
     - Pros: Scalable within a single server environment.
     - Cons: Difficult to scale across multiple data centers, lack of strict ordering.
     - **Figure**: Visualize auto-increment with two servers, each incrementing by a step of `k`.
    
       ![image](https://github.com/user-attachments/assets/6367e096-f8d0-48ec-bd52-003f286be738)


   - **UUID**:
     - A 128-bit unique identifier.
     - Pros: No coordination between servers, scalable.
     - Cons: Too large (128 bits) and non-numeric.
    
       ![image](https://github.com/user-attachments/assets/96af3d32-23da-4b02-824b-7748efadcbc6)
!


   - **Ticket Server**:
     - Centralized auto-increment.
     - Pros: Simple, numeric IDs.
     - Cons: Single point of failure unless multiple ticket servers are used, causing synchronization challenges.
    
       ![image](https://github.com/user-attachments/assets/672ed154-8ad9-4fd5-94d9-39b74c31bac7)


   - **Twitter Snowflake**:
     - Divide 64-bit ID into sections:
       - **Sign Bit (1 bit)**: Reserved for future use.
       - **Timestamp (41 bits)**: Milliseconds since a custom epoch.
       - **Datacenter ID (5 bits)**: Support up to 32 data centers.
       - **Machine ID (5 bits)**: Support up to 32 machines per data center.
       - **Sequence Number (12 bits)**: Handle up to 4,096 IDs per millisecond per machine.
     - **Pros**:
       - Unique, sortable IDs.
       - Scalable across distributed systems.
     - **Cons**:
       - Requires clock synchronization.
         
         ![image](https://github.com/user-attachments/assets/9008b848-0de5-463c-a7d8-f27fbeea4a23)


#### 3. **Deep Dive into the Snowflake Approach**
   - **Datacenter and Machine IDs**:
     - Assigned at startup, rarely change to avoid conflicts.
   - **Timestamp**:
     - IDs sortable by time due to 41-bit timestamp.
     - Supports ~69 years from custom epoch.
   - **Sequence Number**:
     - Resets every millisecond, allowing up to 4,096 IDs/ms/server.
    
       ![image](https://github.com/user-attachments/assets/cc5ee8ac-8725-44e5-9b56-72dbf1131f0d)


#### 4. **Challenges and Considerations**
   - **Clock Synchronization**:
     - Essential for accurate timestamp generation.
     - Use solutions like Network Time Protocol (NTP).
   - **Section Length Tuning**:
     - Adjust bit allocation based on system needs (e.g., higher timestamp bits for longer durations).
   - **High Availability**:
     - Mission-critical systems require redundancy.

#### 5. **Wrap-Up**
   - Chosen Design: Twitter Snowflake approach.
   - Key Advantages:
     - Meets all requirements (unique, sortable, scalable).
     - Well-suited for distributed systems.

If you add relevant figures (e.g., Snowflake bit allocation, multi-master replication process), label them for clarity and tie them to these points for easy understanding.
