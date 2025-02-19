# **System Design – Chat System (Chapter 12)**  

## **Introduction**  
- A **chat system** allows users to send and receive messages in **real-time**.  
- Examples: **WhatsApp, Messenger, Slack, Discord**.  
- Chat apps can be for:  
  - **1-on-1 chat** (e.g., Messenger, WhatsApp).  
  - **Group chat** (e.g., Slack, Discord).  
---

## **Step 1: Understanding the Problem & Scope**  
### **Key Questions to Clarify Requirements**  
| **Question** | **Answer** |
|-------------|-----------|
| **1-on-1 or Group Chat?** | Both |
| **Mobile, Web, or Both?** | Both |
| **Scale of the App?** | 50M Daily Active Users (DAU) |
| **Max Group Size?** | 100 users |
| **Features?** | Text chat, online status, multi-device support, push notifications |
| **Max Message Size?** | 100,000 characters |
| **Encryption?** | Not required (for now) |
| **Chat History Storage?** | Stored **forever** |

---

## **Step 2: High-Level Design**  
A **chat service** manages messages and ensures delivery.  
📌 **Key Functions:**  
- **Receive messages from senders.**  
- **Find recipients & deliver messages.**  
- **Store messages for offline users.**  

### **Communication Between Clients & Server (Figure 12-2)**  
- **Clients (mobile/web) do not communicate directly.**  
- Instead, they connect to the **chat service** using network protocols.  

![image](https://github.com/user-attachments/assets/03319d43-b407-4b84-a5ee-47ae4964fb1f)

---

### **Choosing a Network Protocol for Chat**
| **Protocol** | **How it Works** | **Pros** | **Cons** |
|-------------|----------------|---------|---------|
| **Polling (Figure 12-3)** | Client repeatedly asks the server for new messages. | Simple | **Inefficient** (too many requests) |
| **Long Polling (Figure 12-4)** | Client holds the request open until a message arrives. | Better than polling | Wastes resources |
| **WebSocket (Figure 12-5, 12-6)** | Persistent, **bidirectional** connection between client & server. | **Fast & efficient** | Needs good **connection management** |

✅ **Final Choice: WebSocket** → Best for **real-time messaging** (used by WhatsApp, Messenger).  

![image](https://github.com/user-attachments/assets/f271849b-79c3-471d-a97c-9c7ca0dc9ee0)

![image](https://github.com/user-attachments/assets/d522ba59-2f84-443e-b333-06cd31790a1b)

![image](https://github.com/user-attachments/assets/399d5e5e-c9d7-47fd-8d06-847077a79da5)

![image](https://github.com/user-attachments/assets/0b464831-94f9-4ee5-b452-5bdffcfda6fa)

---

### **High-Level Design (Figure 12-7)**
📌 **Three Main Components:**  
1. **Stateless Services**  
   - Handles **login, signup, user profile**.  
   - Uses **HTTP requests** (not WebSocket).  
   - Load balancer routes requests.  
   
2. **Stateful Services**  
   - **Chat servers** maintain persistent WebSocket connections.  
   - A user stays on **one chat server** unless it fails.  

3. **Third-Party Integration**  
   - **Push Notifications** (when user is offline).  
   - Uses services like **APNS (Apple), FCM (Google)**.  

![image](https://github.com/user-attachments/assets/7179f48e-91b8-4d4f-a2a7-bce5d3950278)

---

### **Scalability Considerations**
📌 **Key Challenges:**  
- **Can one server handle all connections?**  
  - At **1M concurrent users**, **each needs ~10KB memory** → ~10GB RAM needed.  
  - Single-server design is **not scalable** (Single Point of Failure).  

📌 **Solution (Figure 12-8):**
- **Multiple chat servers** for load balancing.  
- **Presence servers** track online/offline users.  
- **API servers** manage login, signup, and profiles.  
- **Message Storage (Key-Value DB)** stores chat history.  

![image](https://github.com/user-attachments/assets/63adf3a2-00b9-4b2a-9640-d249d5e75050)

---

## **Step 3: Design Deep Dive**
### **Storage Choices**
| **Data Type** | **Storage Type** | **Reason** |
|--------------|----------------|-----------|
| **User Data (Profiles, Friends List)** | **Relational Database (SQL)** | Reliable & structured |
| **Chat Messages** | **Key-Value Store (NoSQL, e.g., HBase, Cassandra)** | **Fast retrieval & scalability** |

📌 **Why Key-Value Stores?**  
- Handles **large-scale message storage**.  
- Fast **read/write speeds**.  
- Used by **Facebook Messenger (HBase)** & **Discord (Cassandra)**.  

---

### **Message Data Models**  
📌 **1-on-1 Chat Table (Figure 12-9)**  
- **Primary Key: message_id** (Ensures order).  
- Cannot use `created_at` (timestamps may be the same).  

![image](https://github.com/user-attachments/assets/bac6762a-4337-4349-9b2c-0cc3cf16ded0)


📌 **Group Chat Table (Figure 12-10)**  
- **Primary Key: (channel_id, message_id)**.  
- Ensures messages in a group **stay ordered**.  

![image](https://github.com/user-attachments/assets/b355779f-42a4-48e8-8ce0-ad57b36bb461)

📌 **Generating Unique Message IDs**  
- **Auto-Increment (MySQL)** ❌ (Not available in NoSQL).  
- **Global Sequence Generator (Snowflake ID)** ✅.  
- **Local IDs within Groups** ✅ (Easier to manage).  

---

### **Service Discovery (Figure 12-11)**
📌 **How does a user connect to the best chat server?**  
1. **User logs in.**  
2. **Service Discovery (Zookeeper)** finds the **best** chat server.  
3. **User connects via WebSocket**.  

![image](https://github.com/user-attachments/assets/7932733a-f588-4d4f-b1fa-6a2104236b2e)

---

### **Message Flow (Figure 12-12)**
📌 **1-on-1 Chat Flow**  
1. **User A sends message** to Chat Server.  
2. **Chat Server generates message ID**.  
3. **Message stored in Key-Value DB**.  
4. If **User B is online** → Forward message to their chat server.  
5. If **User B is offline** → Send **push notification**.  

![image](https://github.com/user-attachments/assets/b9e07a88-eb00-4e7f-b8c2-ce36efc8068a)

---

### **Message Sync Across Devices (Figure 12-13)**
📌 **How do messages sync when a user logs in on multiple devices?**  
- Each device tracks **latest message ID (`cur_max_message_id`)**.  
- Device pulls **new messages from storage** using this ID.  

![image](https://github.com/user-attachments/assets/12a985ec-91f2-441e-8b09-fc5e91d40744)

---

### **Small Group Chat Flow (Figure 12-14, 12-15)**
📌 **Message Storage Strategy for Groups**  
1. **Each member gets a copy of the message** in their inbox.  
2. ✅ **Good for small groups (≤100 members)** → **Fast retrieval**.  
3. ❌ **Bad for large groups (e.g., 100K members)** → **Too much storage**.  

![image](https://github.com/user-attachments/assets/983edee7-0c50-41ca-a594-204282f8a3c8)

![image](https://github.com/user-attachments/assets/87c3d229-7f78-4c84-a324-40cca2e0c847)

📌 **Solution for Large Groups:**  
- Instead of storing **separate copies**, store **one copy** and send on request.  

---

### **Online Presence Indicator (Figure 12-17, 12-18, 12-19)**
📌 **How is "Online" status updated?**  
✔ **User Login** → Status set to **Online**.  
✔ **User Logout** → Status set to **Offline**.  
✔ **User Disconnection (Poor Internet)** → Status updated **after timeout (30 sec)**.  

![image](https://github.com/user-attachments/assets/8de2c50f-b2dd-4cdd-bb0c-f66e4b4005f9)

![image](https://github.com/user-attachments/assets/495b00bb-af0b-409e-b9e6-6d2834e61442)

![image](https://github.com/user-attachments/assets/5667d17a-de78-41ec-b0a8-808c66e55d57)

📌 **Heartbeat Mechanism**  
- Clients **send heartbeat signals** every few seconds.  
- If the server **does not receive a signal** → User is **marked offline**.  

📌 **Online Status Updates for Friends**  
- Uses **Publish-Subscribe Model**.  
- **Each friend pair has a channel** (e.g., `A-B, A-C, A-D`).  
- When **User A changes status**, an event is **published to friends' channels**.  

---

## **Step 4: Wrap-Up & Further Considerations**  
✔ **Extend to Support Media Files (Photos/Videos)**  
   - **Use Cloud Storage (e.g., S3)** for large files.  

✔ **Add End-to-End Encryption (E2EE)**  
   - **Only sender & recipient can read messages** (like WhatsApp).  

✔ **Caching for Faster Message Retrieval**  
   - Store **recent messages on the client-side**.  

✔ **Error Handling**  
   - **Server Failure?** → Use **Zookeeper** to assign a new chat server.  
   - **Message Delivery Failure?** → Retry using **Message Queues**.  

---

## **Final Thoughts**
- **Chat systems require real-time messaging, low latency, and scalability.**  
- **WebSocket is the best protocol** for bi-directional communication.  
- **Key challenges:** Message delivery, synchronization, storage, and scaling.  
- **Designing a chat app involves choosing the right storage, caching, and communication protocols.**  

✅ **A well-designed chat system ensures fast, reliable, and scalable communication!** 🚀  

---

These **notes cover all topics** in a **simple, structured format**. You can **add figure numbers** where needed. Let me know if you need modifications! 🚀😃
