## **Design a URL Shortener**
### **Step 1 - Understand the problem and establish design scope**
- URL shortening service creates an alias for a long URL.
- Example:  
  - **Original URL**: `https://www.systeminterview.com/q=chatsystem&c=loggedin&v=v3&l=long`  
  - **Shortened URL**: `https://tinyurl.com/y7keocwj`
- **Basic Use Cases**:
  1. Shorten a long URL.
  2. Redirect a short URL to the original URL.
  3. Ensure high availability, scalability, and fault tolerance.
- **Traffic Estimation**:
  - **Write Operations**: 100 million URLs/day = **1160 writes/sec**.
  - **Read Operations**: 10:1 read-to-write ratio → **11,600 reads/sec**.
  - **Storage Requirement (10 years)**: 365 billion records → **365 TB** (assuming 100 bytes per URL).

---

## **Step 2 - High-Level Design**
- **API Endpoints**:
  1. **Shorten a URL**  
     `POST api/v1/data/shorten`  
     - Request: `{longUrl: longURLString}`  
     - Response: `shortURL`
  2. **Redirect a URL**  
     `GET api/v1/shortUrl`  
     - Response: `longURL` (HTTP redirection)
  
- **URL Redirecting (Fig. 8-1, Fig. 8-2)**
  - **301 Redirect (Permanent)**:
    - Browser caches the response, reducing load on the service.
  - **302 Redirect (Temporary)**:
    - Requests always go to the URL shortener, useful for analytics.
  - **Implementation**:
    - Use **hash tables** to store `<shortURL, longURL>` mappings.


![image](https://github.com/user-attachments/assets/632742f6-e7dd-4acf-a2e3-0a3b9520d8f8)


- **URL Shortening (Fig. 8-3)**
  - Generate a unique **hashValue** for the long URL.
    
![image](https://github.com/user-attachments/assets/f81cfad6-aa13-47a6-a0c1-78247471be10)

---

## **Step 3 - Deep Dive**
### **Data Model (Fig. 8-4)**
- Store `<shortURL, longURL>` mapping in a **relational database**.
- **Columns**: `id, shortURL, longURL`

![image](https://github.com/user-attachments/assets/546037ab-7c6d-424f-b7f2-61f99d6745ba)


### **Hash Function**
- Converts `longURL → hashValue`
- **Hash Value Length Calculation**:
  - Allowed characters: `[0-9, a-z, A-Z]` = **62 characters**.
  - Find `n` such that `62^n ≥ 365 billion` → **n = 7**.
  - **7-character hash** supports ~3.5 trillion URLs.

### **Two Hashing Approaches**
1. **Hash + Collision Resolution (Fig. 8-5)**
   - Use hash functions like CRC32, MD5, SHA-1.
   - If collision occurs, append a unique string.
   - Use **Bloom filters** to optimize collision checking.

![image](https://github.com/user-attachments/assets/97546f13-8ce9-42c7-a0c3-c3b8d0a74678)


![image](https://github.com/user-attachments/assets/c28b6e3c-a5ad-4d61-9ebe-fa474a0fe052)

   
2. **Base 62 Conversion (Fig. 8-6)**
   - Converts a unique ID to a **7-character** short URL.
   - Example:  
     - Convert `11157` (Base 10) → `[2, T, X]` (Base 62).
   - More efficient than traditional hashing.
     
![image](https://github.com/user-attachments/assets/151c5868-b207-4048-8d0f-c7fd44786fd8)


### **URL Shortening Flow (Fig. 8-7)**
1. Check if `longURL` exists in the database.
2. If yes → Return existing `shortURL`.
3. If no → Generate a **unique ID**.
4. Convert ID to `shortURL` using **Base 62 conversion**.
5. Store (`ID, shortURL, longURL`) in the database.


![image](https://github.com/user-attachments/assets/d6c8cacc-3394-4a04-b115-4d85c1801ce0)


- **Example**:
  - **Input**: `https://en.wikipedia.org/wiki/Systems_design`
  - **Unique ID**: `2009215674938`
  - **Shortened URL**: `https://tinyurl.com/zn9edcu`

### **URL Redirecting Flow (Fig. 8-8)**
1. User clicks **short URL**.
2. Load balancer forwards request to web servers.
3. Check **cache** for `shortURL`:
   - If found → Redirect immediately.
   - If not found → Query **database**, then cache it.

![image](https://github.com/user-attachments/assets/155f7200-fdcd-4a1a-afde-bbfdc8562720)

---

## **Step 4 - Additional Considerations**
- **Rate Limiting**: Prevent spam by filtering requests (IP-based rules).
- **Web Server Scaling**: Easily scale due to stateless design.
- **Database Scaling**: Use **replication** & **sharding** for performance.
- **Analytics**:
  - Track click rate, user source, etc.
- **Availability & Reliability**:
  - Discussed in **Chapter 1**.

---
