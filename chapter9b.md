# **System Design – News Feed System (Chapter 11)**  

## **Introduction**  
- A **news feed** is a list of updates from friends or followed pages, sorted by relevance or time.  
- Example: **Facebook, Instagram, Twitter timeline**.  

---

## **Step 1: Understanding the Problem & Scope**  
### **Key Questions to Clarify Requirements**  
| **Question** | **Answer** |
|-------------|-----------|
| **Is it for mobile, web, or both?** | Both |
| **What features are needed?** | Users can post & see friends' posts |
| **How is the feed sorted?** | Reverse chronological order (latest posts first) |
| **Max number of friends?** | 5,000 friends per user |
| **Traffic volume?** | 10 million Daily Active Users (DAU) |
| **Can posts contain media?** | Yes, images & videos |

---

## **Step 2: High-Level Design**  
The system has **two main flows**:  
1. **Feed Publishing** – When a user posts, it is stored and sent to friends.  
2. **News Feed Building** – Aggregates posts from friends and displays them.  

### **News Feed APIs**  
| **API** | **Method** | **Purpose** |
|---------|-----------|-------------|
| `/v1/me/feed` | `POST` | Publish a new post |
| `/v1/me/feed` | `GET` | Retrieve user's news feed |

---

### **Feed Publishing Flow (Figure 11-2)**  
1. **User** makes a post → Sent via API (`POST /v1/me/feed`).  
2. **Load Balancer** distributes requests to **Web Servers**.  
3. **Web Servers** authenticate & validate requests.  
4. **Post Service** stores post in **Database & Cache**.  
5. **Fanout Service** sends post to friends' news feed.  
6. **Notification Service** alerts friends (push notifications).  


![image](https://github.com/user-attachments/assets/b3c1b9c5-5a90-4b46-b4aa-92bfc426d2a7)

---

### **News Feed Retrieval Flow (Figure 11-3)**  
1. **User requests news feed** (`GET /v1/me/feed`).  
2. **Load Balancer** routes request to **Web Servers**.  
3. **Web Servers** forward to **News Feed Service**.  
4. **News Feed Service** fetches posts from **Cache**.  
5. **User sees aggregated news feed**.  


![image](https://github.com/user-attachments/assets/45dcc200-436d-4017-9814-cf6d3240786d)

---

## **Step 3: Design Deep Dive**  
### **Feed Publishing Deep Dive (Figure 11-4)**  
📌 **Web Servers**  
- Authenticate users (`auth_token`).  
- Apply **rate limiting** (prevent spam).  

📌 **Fanout Service**  
- **Push Model (Fanout on Write)**  
  - **Pro:** Precomputed news feed → Fast retrieval.  
  - **Con:** Heavy **write load** if the user has many friends (**Hot Key Problem**).  

- **Pull Model (Fanout on Read)**  
  - **Pro:** No unnecessary writes, saves resources.  
  - **Con:** Fetching news feed is **slow**.  

✔ **Hybrid Approach**  
- **Regular users** → **Push Model** for speed.  
- **Celebrities (many followers)** → **Pull Model** to avoid overload.  

📌 **How Fanout Service Works (Figure 11-5)**  
1. Fetch **friends' list** from **Graph Database**.  
2. Check **user settings** (e.g., muted users).  
3. Send friend list & **post ID to Message Queue**.  
4. **Workers** fetch data & update **News Feed Cache**.  
5. Store `<post_id, user_id>` in **cache** (Figure 11-6).  


![image](https://github.com/user-attachments/assets/f592791f-9851-45c2-829f-56ea045df268)

![image](https://github.com/user-attachments/assets/68bc591d-7f0c-4c8a-a436-831693d6b28d)

---

### **News Feed Retrieval Deep Dive (Figure 11-7)**  
📌 **Retrieving a News Feed**  
1. **User requests news feed** (`GET /v1/me/feed`).  
2. **Load Balancer → Web Server → News Feed Service**.  
3. Fetch **post IDs from News Feed Cache**.  
4. Retrieve **full post details from Post & User Cache**.  
5. Media (images, videos) are fetched from **CDN**.  
6. Return **JSON response** to the client.  

![image](https://github.com/user-attachments/assets/1a475f5c-665d-43eb-b285-88efab3569da)


---

### **Cache Architecture (Figure 11-8)**  
To handle **10M+ DAU**, we divide the **cache into 5 layers**:  
1. **News Feed Cache** → Stores **post IDs** per user.  
2. **Content Cache** → Stores **post details** (hot content stays longer).  
3. **Social Graph Cache** → Stores **friend relationships**.  
4. **Action Cache** → Stores **likes, comments, interactions**.  
5. **Counter Cache** → Stores **counts (likes, replies, followers, etc.)**.  


![image](https://github.com/user-attachments/assets/1990a9a2-550b-4d74-9d86-650161c1414b)


✔ **Why Cache?**  
- **Reduces database load.**  
- **Speeds up feed retrieval.**  
- **Improves scalability.**  


---

## **Step 4: Wrap-Up & Scalability Considerations**  
✔ **Scaling the Database**  
- **Vertical vs. Horizontal Scaling**  
- **SQL vs. NoSQL (tradeoffs: consistency vs. scalability)**  
- **Master-Slave Replication** (better read performance)  
- **Database Sharding** (split data by user ID)  

✔ **Other Key Considerations**  
- **Keep Web Tier Stateless** → Use **load balancers**.  
- **Cache Data as Much as Possible** → Avoid DB calls.  
- **Support Multiple Data Centers** → Improve availability.  
- **Use Message Queues** → Decouple services.  
- **Monitor Key Metrics** → Track **latency & QPS** (queries per second).  

---

## **Final Thoughts**
- **News Feed Systems** power platforms like **Facebook, Twitter, Instagram**.  
- **Key Challenges:** Handling **high traffic**, **efficient caching**, and **scalable databases**.  
- **Hybrid Fanout Approach** balances **speed & efficiency**.  

✅ **A well-designed news feed system ensures real-time updates and smooth performance!** 🚀  

---

## **References**
- **How Facebook News Feed Works:** [Facebook Help](https://www.facebook.com/help/327131014036297/)  
- **Graph Database for Friend Relationships:** [Neo4j & SQL](http://geekswithblogs.net/brendonpage/archive/2015/10/26/friend-of-friend-recommendations-with-neo4j.aspx)  
