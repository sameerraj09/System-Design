# **System Design Interview – Web Crawler Notes**  

## **Introduction**  
- A **web crawler** (robot/spider) is used to collect web content like web pages, images, videos, etc.  
- **Uses of web crawlers**:  
  - **Search engine indexing** (Googlebot, Bingbot)  
  - **Web archiving** (US Library of Congress, EU Web Archive)  
  - **Web mining** (Financial firms extracting data from reports)  
  - **Web monitoring** (Checking copyright violations, e.g., Digimarc)  

![image](https://github.com/user-attachments/assets/635b8b37-1cc4-4346-9941-d56138657b15)

---

## **Step 1: Understand the Problem and Design Scope**  
- **Basic Algorithm**:  
  1. Download web pages from a given URL list.  
  2. Extract new URLs from these pages.  
  3. Add new URLs to the download queue and repeat.  
- **Clarifying Questions to Ask**:  
  - **Purpose?** (Search engine indexing, data mining, etc.)  
  - **Pages per month?** (Example: 1 billion pages)  
  - **Content types?** (Example: Only HTML)  
  - **Track updates?** (Yes, newly added/edited pages)  
  - **Store pages?** (Yes, up to 5 years)  
  - **Handle duplicates?** (Ignore duplicate pages)  

- **Key Characteristics of a Good Web Crawler**:  
  - **Scalability** – Must handle billions of pages efficiently.  
  - **Robustness** – Must handle bad HTML, slow servers, errors.  
  - **Politeness** – Should not overload a website with too many requests.  
  - **Extensibility** – Should allow adding new content types (images, PDFs).  

- **Back-of-the-Envelope Estimation**:  
  - **1 billion pages per month**  
  - **400 pages per second**, with peak at **800 pages per second**  
  - **Average page size: 500 KB**  
  - **Storage required**:  
    - **500 TB per month**  
    - **30 PB for 5 years of storage**  

---

## **Step 2: High-Level Design**  
- **Key Components of a Web Crawler (Figure 9-2)**:  
  - **Seed URLs** – Starting points for crawling (e.g., university homepage).  
  - **URL Frontier** – Stores URLs to be processed (FIFO queue).  
  - **HTML Downloader** – Fetches web pages.  
  - **DNS Resolver** – Converts domain names to IP addresses.  
  - **Content Parser** – Checks and validates HTML pages.  
  - **Content Seen?** – Prevents duplicate content storage (uses hashing).  
  - **Content Storage** – Stores HTML content.  
  - **URL Extractor** – Extracts links from pages.  
  - **URL Filter** – Filters out unwanted URLs (e.g., errors, blacklisted sites).  
  - **URL Seen?** – Avoids processing the same URL multiple times.  
  - **URL Storage** – Keeps track of visited URLs.  

![image](https://github.com/user-attachments/assets/7a49b7e4-93b3-4f29-936b-8d10c7347684)

- **Web Crawler Workflow (Figure 9-4)**:  
  1. **Add Seed URLs** to URL Frontier.  
  2. **HTML Downloader** fetches URLs.  
  3. **DNS Resolver** converts URLs to IP addresses.  
  4. **Content Parser** validates pages.  
  5. **Content Seen?** checks for duplicate pages.  
  6. **URL Extractor** extracts links.  
  7. **URL Filter** removes unnecessary links.  
  8. **URL Seen?** checks if a URL was visited before.  
  9. **New URLs** are added to URL Frontier.  


![image](https://github.com/user-attachments/assets/22d96d15-5b91-490d-9642-336345b1beed)

---
---

## **Graph Traversal: BFS vs DFS**
A web crawler essentially navigates the **web as a graph**, where:
- **Nodes** represent web pages.
- **Edges** (links) connect pages to one another.

To traverse this graph and discover new web pages, we need an efficient **graph traversal algorithm**. The two most common choices are:
1. **Depth-First Search (DFS)**
2. **Breadth-First Search (BFS)**

### **Why BFS is Better Than DFS?**
| Feature | DFS | BFS |
|---------|-----|-----|
| **Traversal Method** | Explores one path deeply before backtracking | Explores all neighbors before moving deeper |
| **Memory Usage** | Lower (only needs to track one path) | Higher (stores all neighbors in a queue) |
| **Best for Web Crawling?** | ❌ No – Can go too deep, missing important links | ✅ Yes – Covers wide areas first, ensuring better discovery |

- **DFS Issue**: If a web page has deep nested links, DFS will continue crawling in one direction indefinitely, potentially missing high-value pages.
- **BFS Advantage**: It ensures the crawler explores **more important and diverse** content first.

📌 **Example**:
Imagine we are crawling Wikipedia:
- **DFS** may go from the **homepage → Science → Physics → Quantum Mechanics → Subatomic Particles**, taking a long time before reaching other important pages.
- **BFS** will explore **Homepage → Science, Technology, Sports, History**, ensuring broader and faster coverage.

Thus, BFS is the **preferred choice** for web crawling.


![image](https://github.com/user-attachments/assets/45030464-e807-49b2-967b-68a39b608c7f)

---

## **URL Frontier**
A **URL Frontier** is a key data structure that manages the URLs waiting to be downloaded. It ensures:
1. **Politeness** – Avoiding overloading the same website.
2. **Prioritization** – Visiting high-value pages first.
3. **Freshness** – Keeping data updated.

### **Challenges with a Simple FIFO Queue**
If we only use a **simple FIFO queue**, the crawler will:
1. Download too many pages from the **same site** (impolite).
2. Ignore important pages from high-ranking websites.
3. Be slow in updating frequently changing pages.

To fix these issues, **modern URL Frontiers** use:
1. **Politeness Mechanism** (Figure 9-6)
2. **Priority Scheduling** (Figure 9-7)
3. **Hybrid Memory + Disk Storage** (Figure 9-8)

---

## **Politeness (Figure 9-6)**
**Problem**: If a crawler sends too many requests to a single website (e.g., Wikipedia), it might:
- Overload the website.
- Get blocked by the server.

**Solution**: We maintain **separate queues per website** to control the request rate.

### **How It Works?**
1. **Queue Router**: Ensures each website has its own queue.
2. **Mapping Table**: Maps each host (e.g., `wikipedia.org`) to a queue.
3. **Queue Selector**: Assigns each queue to a download worker.
4. **Worker Threads**: Ensure polite request delays.

![image](https://github.com/user-attachments/assets/5fe89890-ed2c-4a53-94ef-7c2dce86eba2)
![image](https://github.com/user-attachments/assets/4dc0c74e-d0b9-45a3-866c-cd38c20b6c94)

📌 **Example**:  
- A polite crawler might wait **1 second** before sending a second request to Wikipedia.
- Meanwhile, it can download pages from other domains in parallel.

---

## **Priority Scheduling (Figure 9-7)**
Some pages are more important than others. Instead of processing URLs in FIFO order, a **Prioritizer** ranks them based on:
1. **PageRank** – Pages linked by many other sites are crawled first.
2. **Traffic** – Highly visited pages (e.g., `bbc.com`) have higher priority.
3. **Update Frequency** – Pages that change often (e.g., news sites) should be recrawled more frequently.

### **How It Works?**
1. **Prioritizer assigns scores to URLs.**
2. **High-priority URLs are placed in front queues.**
3. **Queue Selector picks URLs from high-priority queues first.**

📌 **Example**:  
- **A blog with 2 visitors/day** → Low priority.  
- **Amazon homepage with millions of visits** → High priority.

---
![image](https://github.com/user-attachments/assets/00becd0b-ac3a-4062-a0c8-ee099ece4243)

## **Freshness (Keeping Data Updated)**
Web pages constantly change. If we **never revisit** a page, our data becomes outdated.

### **Recrawling Strategies**
1. **Recrawl based on past update patterns** (e.g., news sites update daily).
2. **Prioritize frequently changing pages** (e.g., Twitter, e-commerce).
3. **Use machine learning to predict update frequency.**

---

## **Storage for URL Frontier (Figure 9-8)**
- **URLs are too many** (hundreds of millions).
- **RAM is limited**, so we store most URLs on disk.
- **Solution**: Hybrid **Memory + Disk** architecture.
  - **Frequently accessed URLs in memory**.
  - **Bulk storage on disk**.

![image](https://github.com/user-attachments/assets/e96a7618-048c-45b7-9927-edbe7c822eda)

📌 **Example**:
- **Top 100,000 URLs** → Stored in RAM for quick access.
- **Old URLs** → Moved to disk.

---

## **HTML Downloader**
Once the URL Frontier picks a URL, the **HTML Downloader** fetches the page using **HTTP requests**.

### **Handling `robots.txt` (Figure 9-9)**
Some sites restrict crawlers using a `robots.txt` file.

![image](https://github.com/user-attachments/assets/5fda3501-b951-4759-a775-0878e0fce192)


📌 **Example (Amazon `robots.txt`)**:
```
User-agent: Googlebot
Disallow: /creatorhub/*
```
This means **Googlebot is not allowed** to crawl `/creatorhub/`.

### **Optimizations**
1. **Distributed Crawling** – Multiple servers share the workload.
2. **DNS Caching** – Reduce slow DNS lookups.
3. **Locality Optimization** – Place crawlers near target websites.
4. **Short Timeouts** – Avoid waiting for unresponsive sites.

---

## **Robustness & Extensibility**
### **Ensuring Robustness**
- **Consistent Hashing** – Distributes load evenly among crawler nodes.
- **Crash Recovery** – Saves state to resume crawling after failure.
- **Error Handling** – Prevents crashes due to bad HTML.

### **Making Crawlers Extensible (Figure 9-10)**
- Add new modules **without redesigning the system**.
- Example:
  - **PNG Downloader** → Crawls images.
  - **Web Monitor** → Tracks copyright infringements.

---

## **Detecting and Avoiding Problematic Content**
### **1. Removing Duplicate Pages**
- **30% of web pages are duplicates**.
- **Solution**: Use **hashing** to detect and remove them.

📌 **Example**:
- `example.com/page1`
- `example.com/page2`
If they have the **same hash**, they are duplicates.

### **2. Avoiding Spider Traps**
**Problem**: Some sites create **infinite loops of URLs**.
📌 **Example**:
```
example.com/foo/bar/foo/bar/foo/bar/...
```
**Solution**:  
- **Set max depth limit** (e.g., stop after 10 nested links).
- **Detect abnormally large sites**.

### **3. Removing Spam & Noise**
- Many pages contain **ads, spam, junk data**.
- **Solution**: Use **filters** to remove:
  - Advertisements.
  - Clickbait pages.

---

## **Step 4: Wrap-Up**
### **Key Takeaways**
- **BFS is better than DFS** for web crawling.
- **URL Frontier manages politeness, priority, and freshness**.
- **HTML Downloader handles page fetching & optimizations**.
- **Duplicate detection & spam filtering improve efficiency**.
- **Crawlers must be robust & extensible** to scale effectively.

### **Additional Considerations**
1. **JavaScript & AJAX Handling** – Some pages load dynamically.
2. **Database Sharding** – Large crawlers use **distributed databases**.
3. **Keeping Crawlers Stateless** – Large-scale systems should avoid dependencies.

---

## **Final Thoughts**
Building a **scalable, efficient, and polite** web crawler requires:
✅ **BFS-based traversal**  
✅ **Smart URL scheduling**  
✅ **Efficient storage techniques**  
✅ **Duplicate detection & spam filtering**  

🔹 **This is just one example of system design thinking.**  
🔹 **Mastering these concepts will help in real-world applications!** 🚀  

---
